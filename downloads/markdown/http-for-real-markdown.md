# RFC 9110 — HTTP Semantics, taught in one pass

This is a teaching walkthrough of **RFC 9110** (STD 97, June 2022), *HTTP Semantics*, edited by Fielding, Nottingham, and Reschke. The source of truth is the RFC itself: <https://datatracker.ietf.org/doc/html/rfc9110> and <https://www.rfc-editor.org/rfc/rfc9110>.

How to use this file:

- It follows the RFC’s own section numbers, so you can jump between this note and the spec.
- It is a **teaching rewrite**, not a substitute for the legal text. MUST / SHOULD / MAY still mean what BCP 14 says they mean in the RFC.
- Caching is **not** fully specified here. That lives in RFC 9111. HTTP/1.1 framing lives in RFC 9112. HTTP/2 and HTTP/3 live in RFC 9113 and RFC 9114. RFC 9110 is the shared brain they all use.

---

## What this document is

HTTP is a family of **stateless, application-level, request/response** protocols. They share:

- a uniform interface to resources,
- extensible semantics,
- self-descriptive messages.

RFC 9110 defines **those shared semantics**. It does not define how bytes sit on the wire for HTTP/1.1 vs HTTP/2 vs HTTP/3. Major version is about messaging syntax; this RFC is about meaning.

It **obsoletes** (in whole or in the semantic parts): RFC 2818, 7230 (portions), 7231, 7232, 7233, 7235, 7538, 7615, 7694. It **updates** RFC 3864 (header registration). Caching moved to RFC 9111. HTTP/1.1 syntax/connection management moved to RFC 9112.

The design bet, from [RFC 9110 §1.1](https://www.rfc-editor.org/rfc/rfc9110#section-1.1):

- Clients should not need to know how a service is implemented.
- Servers should not need to know a client’s purpose.
- Each request can be understood in isolation.
- The protocol can sit in the middle (proxies, gateways) and translate other systems into HTTP.
- You cannot define what happens *behind* the interface — only the syntax, the intent of received communication, and expected recipient behavior.

History in one paragraph ([§1.2](https://www.rfc-editor.org/rfc/rfc9110#section-1.2)): GET of a pathname (1990) → HTTP/0.9 and HTTP/1.0 → HTTP/1.1 (length framing, negotiation, validators, cache, ranges, persistent connections) → HTTP/2 (multiplexed TLS/TCP, HPACK, push) → HTTP/3 (QUIC over UDP). **All three current majors use these semantics.** They did not obsolete each other.

Core loop ([§1.3](https://www.rfc-editor.org/rfc/rfc9110#section-1.3)): a client sends a **request** (method + target) toward an origin; a server interprets it against a **resource** and returns one or more **responses** (status + optional content). Semantics live in methods, request fields, status codes, representation metadata, and content negotiation.

---

## 2. Conformance

### 2.1 Syntax

Grammar is ABNF ([RFC 5234](https://www.rfc-editor.org/rfc/rfc5234)) plus case-sensitive string notation ([RFC 7405](https://www.rfc-editor.org/rfc/rfc7405)). The `#` list operator is HTTP’s own extension ([§5.6.1](https://www.rfc-editor.org/rfc/rfc9110#section-5.6.1)). Rules named `obs-*` are obsolete but still parsed.

### 2.2 Requirements words

MUST / SHOULD / MAY are BCP 14. Requirements are aimed at **roles**: sender, recipient, client, server, user agent, intermediary, origin server, proxy, gateway, cache — not “the program as a whole.”

**Generate** means “create,” not “forward.” A proxy that copies a field it received is not generating it.

A sender MUST NOT emit protocol elements that fail the ABNF, and MUST NOT emit syntax that belongs to a role it does not have for that message.

Semantic conformance matters as much as syntax. If you claim HTTP/1.1 but do not understand HTTP/1.1 recipient features, you will not interoperate. Recipients MAY work around a known-buggy implementation **only for that implementation**.

### 2.3 Lengths

There is **no** global max length. Recipients SHOULD parse defensively. At minimum, you MUST be able to parse elements at least as long as those you yourself generate. Recommended floor for URI-ish elements: **8000 octets** ([§4.1](https://www.rfc-editor.org/rfc/rfc9110#section-4.1)).

### 2.4 Errors

Interpret received elements by this spec, unless you have determined the sender is wrong (e.g. a User-Agent known to send broken `Accept-Encoding`). Recovery from invalid constructs is application-specific, except where security is implicated. Idempotent requests MAY be retried after connection failure ([§9.2.2](https://www.rfc-editor.org/rfc/rfc9110#section-9.2.2)).

### 2.5 Protocol version

`HTTP/major.minor`. **Major** = messaging syntax. **Minor** = highest minor the sender understands inside that major. Semantics do not version with this number; extensions ([§16](https://www.rfc-editor.org/rfc/rfc9110#section-16)) add features without bumping the version.

- HTTP/1.1 = this document + RFC 9111 + RFC 9112.
- A major with no minors implies `.0`.
- Clients SHOULD send the highest version they speak whose major is ≤ what the server supports.
- Servers SHOULD reply with a major ≤ the request’s major.
- A higher minor than you implement, same major: process as your highest known minor. It is supposed to be backward compatible.
- 505 is how you refuse a major version.

---

## 3. Terminology and core concepts

### 3.1 Resource

The **target of a request**. HTTP does not care what it *is*. Identification (URI) is separate from semantics (method + a few request fields). A resource MUST NOT treat a request inconsistently with the method. A URI that “looks unsafe” does not make GET unsafe.

### 3.2 Representation

Information intended to reflect a past, current, or desired **state** of a resource: metadata + a potentially unbounded stream of data. HTTP transfers **representations**, not the resource itself. That is why a URI can name “the weather in Laguna Beach now.”

A resource may have many representations. **Content negotiation** picks a **selected representation**. That selected representation is what 200 / 206 / 304 and conditionals talk about.

### 3.3 Connections, clients, servers

HTTP runs over a reliable connection. **Client** opens it and sends requests; **server** accepts and answers. The same program can be both on different connections.

HTTP is **stateless**: each request’s semantics stand alone. A server MUST NOT assume two requests on one connection are the same user agent unless the connection is secured and dedicated to that agent.

### 3.4 Messages

A **request** has method, request target, optional header fields, optional content, optional trailers. A **response** has a status code, same optional pieces. **Sender** / **recipient** are about a given message, not a process identity.

### 3.5 User agent

Anything that initiates a request: browsers, spiders, CLI tools, bulbs, firmware. “Report to the user” MAY mean a log. Confirming an unsafe action MAY mean a prior config choice.

### 3.6 Origin server

Whoever can originate **authoritative** responses for a target resource.

Simplest picture ([Figure 1](https://www.rfc-editor.org/rfc/rfc9110#section-3.6)):

```
UA ======================================= O
        request >              < response
```

### 3.7 Intermediaries

Three HTTP roles:

| Role | Chosen by | Behavior |
|---|---|---|
| **Proxy** | client (forward proxy) | Client-selected forwarding agent; may translate protocols |
| **Gateway** (reverse proxy) | server side | Looks like the origin outbound; forwards inbound |
| **Tunnel** | often CONNECT | Blind byte relay; not an HTTP party once up |

Direction words ([§3.7](https://www.rfc-editor.org/rfc/rfc9110#section-3.7)):

- **upstream → downstream**: message flow.
- **inbound**: toward the origin. **outbound**: toward the user agent.

A gateway’s *outbound* face must obey origin-server rules. Its *inbound* HTTP-to-HTTP face must obey user-agent rules if it wants to interoperate.

**Interception / transparent proxies** are not HTTP intermediaries in this taxonomy. They hijack packets. At protocol level they are indistinguishable from an on-path attacker.

### 3.8 Caches

A local store of previous responses. Any client or server MAY cache; a tunnel MUST NOT. Whether a response is cacheable, and when a stored copy may be reused, is RFC 9111.

### 3.9 Example exchange

```http
GET /hello.txt HTTP/1.1
User-Agent: curl/7.64.1
Host: www.example.com
Accept-Language: en, mi
```

```http
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain

Hello World! My content includes a trailing CRLF.
```

That is HTTP/1.1 *syntax* illustrating these *semantics*: GET of a representation, validators, ranges advertised, length, negotiation variance, media type.

---

## 4. Identifiers in HTTP

HTTP identifies resources with URIs ([RFC 3986](https://www.rfc-editor.org/rfc/rfc3986)).

### 4.1 URI references

Used to target requests, indicate redirects, and name relationships. Fragments are **not** part of the target URI (client-side only).

HTTP defines `absolute-path = 1*( "/" segment )` (non-empty path) and `partial-URI` (relative, no fragment). Unless a field says otherwise, URI references are resolved against the **target URI**.

### 4.2 Schemes

| Scheme | Default port | Authority idea |
|---|---|---|
| `http` | 80 | whoever listens on that TCP port for that host |
| `https` | 443 | whoever can use the private key of a cert the client trusts for that origin, over a secured channel |

Minting a URI does **not** mean a server exists.

Empty host is invalid. `http` and `https` are **distinct origins** even on the same host. Cookies and similar host-scoped extensions can leak across that boundary; they must be designed carefully.

**https** means: before the request is sent, it is secured; only secured responses are accepted. “Secured” = server authenticated as that authority, plus confidentiality and integrity acceptable to both sides. Not necessarily TCP.

**Normalization** ([§4.2.3](https://www.rfc-editor.org/rfc/rfc9110#section-4.2.3)):

- default port omitted,
- empty path ≡ `/` except as OPTIONS target,
- scheme and host case-insensitive (usually lowercase); other components case-sensitive,
- unreserved characters equivalent to their percent-encoding; normal form is decoded.

These are equivalent:

```
http://example.com:80/~smith/home.html
http://EXAMPLE.com/%7Esmith/home.html
http://EXAMPLE.com:/%7esmith/home.html
```

Do not identify distinct resources with URIs that normalize equal.

**userinfo** (`user:password@`) MUST NOT be generated in http(s) URIs inside messages. Recipients SHOULD treat userinfo from untrusted sources as an error (phishing).

Fragments: allowed only where the field’s ABNF says so. They are not in the `http-URI` / `https-URI` productions.

### 4.3 Authoritative access

**Origin** = `(scheme, host, port)` after lowercasing scheme/host and stripping leading zeros on port. Missing port → default.

```
https://Example.Com/happy.js
→ origin { "https", "example.com", "443" }
```

Different scheme, host, or port → different origin, even if the same operator controls both, unless the authoritative server aliases them.

**http origins.** Resolve host, TCP connect, send a request whose target matches the client’s target URI. A **non-interim** response is an authoritative answer. Weak: DNS and routing can be attacked. Alt-Svc and caches are other ways to get an answer.

**https origins.** Authority is the private key matching a cert the client trusts. HTTP/1.1: only when the connection is to that origin’s host. HTTP/2 and HTTP/3: OK if the origin host is in the cert **and** the client believes it could open a connection to that host (in practice a DNS check that IPs match), unless ORIGIN frame ([RFC 8336](https://www.rfc-editor.org/rfc/rfc8336)) relaxes this.

The request still carries host and port so one process can host many origins. The server MUST check the target URI; a network attacker can land port 443 traffic on port 8000. https does **not** trust TCP port number.

**Certificate verification** ([§4.3.4](https://www.rfc-editor.org/rfc/rfc9110#section-4.3.4), via RFC 6125):

- Build a reference identity from the URI host: IP-ID if literal IP, else DNS-ID.
- MUST NOT use CN-ID (legacy exception for old clients).
- Invalid cert: user agent MUST ask the user or terminate; automated clients SHOULD terminate and MUST offer a way to enable the check.

**IP-ID:** IPv4 = 4 octets, IPv6 = 16, matched against `subjectAltName` iPAddress.

---

## 5. Fields

Name/value pairs in header and trailer sections. Extensible registered namespace.

### 5.1 Names

`field-name = token`. Case-insensitive. Register in the HTTP Field Name Registry. Unknown fields: proxies MUST forward them unless listed in `Connection` or locally blocked; other recipients SHOULD ignore them. That is how HTTP extends without upgrading every hop.

`Host` and `Connection` ought to be recognized even by implementations that do not claim HTTP/1.1.

### 5.2 Combined field value

One name, many lines → combine **in order** with commas:

```http
Example-Field: Foo, Bar
Example-Field: Baz
```

Combined value: `Foo, Bar, Baz`.

### 5.3 Order

Same-name order is significant. Proxies MUST NOT reorder those lines. Senders MUST NOT emit multiple lines for a name unless the field is defined as a list (`#rule`). **Set-Cookie is the famous exception** — it is not list syntax; treat it as a special case.

Different names: order is not significant. Good practice: send control data first (`Host` on requests, `Date` on responses).

A server MUST NOT start applying a request until the **entire header section** is received (conditionals, auth, duplicate-field tricks).

### 5.4 Limits

No spec max. A server that will not process huge fields MUST send an appropriate 4xx. Ignoring them enables request smuggling. Clients MAY truncate fields that are safe to ignore.

### 5.5 Values

No leading/trailing whitespace in the value (strip it if a version allows it on the wire). Prefer US-ASCII; new fields SHOULD be VCHAR / SP / HTAB. `obs-text` (`%x80-FF`) SHOULD be treated as opaque.

CR, LF, or NUL in a field value: MUST reject the message **or** replace those bytes with SP. Other CTL: invalid; MAY keep them in a safe quoted context.

**Singleton** fields: one member. **List-based** fields: `#` operator. Commas inside a member need quoting or another delimiter (dates, URIs).

### 5.6 Common value rules

**Lists (`#`).** Senders MUST NOT generate empty elements. Recipients MUST tolerate a reasonable number of empty elements (merged values), not so many that it is a DoS.

**Tokens.** No whitespace, no delimiters. Delimiters are `DQUOTE` and `(),/:;<=>?@[\]{}`.

**Whitespace.**

| Rule | Meaning |
|---|---|
| OWS | optional; generate as one SP if you generate it |
| RWS | required; generate as one SP |
| BWS | historical; senders MUST NOT generate; recipients MUST strip |

OWS/RWS MAY be collapsed to one SP before interpreting or forwarding.

**Quoted strings.** Double quotes. `\"` and `\\` via quoted-pair. Only quote when you must.

**Comments.** Parentheses, nested, only where the field grammar includes `comment`.

**Parameters.** `; name=value`. Names case-insensitive. Token and quoted-string values equivalent. No whitespace around `=`.

**HTTP-date.** Three historical formats; senders MUST generate **IMF-fixdate**:

```
Sun, 06 Nov 1994 08:49:37 GMT
```

Obsolete: RFC 850 (`Sunday, 06-Nov-94 08:49:37 GMT`) and asctime (`Sun Nov  6 08:49:37 1994`). Recipients MUST accept all three. Always UTC (called GMT). rfc850 two-digit years: if it looks >50 years in the future, it is the most recent matching year in the past. HTTP-date is case-sensitive here; caches relax that (RFC 9111).

---

## 6. Message abstraction

Independent of HTTP/1.1 vs 2 vs 3, a message is:

1. **Control data** (method + target + version, or status + version)
2. **Header fields** (name/value table)
3. **Content** (octet stream, possibly empty)
4. **Trailer fields** (name/value table after content)

Order on the wire: framing/control → headers → content → trailers.

Messages are **self-descriptive**, except: a client MUST remember the request when parsing the response (HEAD looks like GET without a body).

HTTP/1.1 trailers live after chunked content. HTTP/2 and HTTP/3 trailers are the terminating header block.

### 6.1 Framing and completeness

Each major version defines framing. HTTP/0.9 / early 1.0 ended responses by closing the connection; HTTP/1.1 still allows that, but it cannot distinguish incomplete from complete. Modern practice is explicit lengths.

A message is **complete** when all framed octets are present. Connection-close framing counts as complete unless the transport says otherwise.

### 6.2 Control data

HTTP/1.x: first line. HTTP/2 and HTTP/3: pseudo-headers (`:method`, `:path`, `:authority`, `:status`, …).

When forwarding, an intermediary updates the version to **its** version and records the upstream version in `Via`.

### 6.3 Header fields

Fields sent **before** content. Some names are header-only by definition.

### 6.4 Content

The data **after** stripping transfer framing. Chunked HTTP/1.1: content is the concatenated chunks, **not** the chunk sizes or trailers.

`Content-*` is an informal prefix. Some of those fields describe **message content**; some describe the **selected representation**. Read the field.

**Request content meaning** comes from the method (PUT = desired state; POST = data to process).

**Response content meaning** comes from method + status + fields.

| Situation | Content? |
|---|---|
| HEAD | never |
| 2xx CONNECT | no — tunnel starts |
| 1xx, 204, 304 | never |
| everything else | yes, possibly zero length |
| 206 GET | one or more parts of the selected representation |
| error statuses | usually an explanation of the error |

**Identifying what the content is a representation of** ([§6.4.2](https://www.rfc-editor.org/rfc/rfc9110#section-6.4.2)), for responses, first match wins:

1. HEAD / 204 / 304 → no content
2. GET + 200 → target resource
3. GET + 203 → possibly modified target resource (intermediary)
4. GET + 206 → parts of target resource
5. `Content-Location` equals target URI → target resource
6. `Content-Location` differs → sender *claims* that other resource (untrusted unless verified)
7. else unidentified by HTTP

On requests, `Content-Location` is an assertion about the enclosed representation; do not blindly trust it.

### 6.5 Trailers

Useful for checksums, signatures, delivery metrics — things you only know after sending the body.

Do not merge trailers into headers unless the field definition says how. Many intermediaries drop them. `TE: trailers` means “I will not drop the trailer **section**,” not “I understand every trailer.”

A sender MUST NOT put a field in trailers unless that field’s definition allows it. Framing, routing, auth, request modifiers, response controls, content format: header-only.

`Trailer` (the header) lists names you expect to send as trailers, so the recipient can prepare (e.g. running checksum).

### 6.6 Date and Trailer

**Date:** origination time, IMF-fixdate. Origin with a clock MUST send it on 2xx/3xx/4xx, MAY on 1xx/5xx. Origin without a clock MUST NOT send it. Recipient with a clock and a missing Date on a response MUST record receipt time if caching or forwarding. Invalid Date MAY be replaced with receive time. Clients rarely send Date.

**Trailer:** `#field-name` of anticipated trailer fields.

---

## 7. Routing

The client chooses the path from target resource + proxy config + connection reuse. The response comes back the same chain.

### 7.1 Target resource

Resolve the URI reference to an absolute **target URI** (no fragment). Send enough of it for recipients to identify the same resource: the **request target** in control data plus `Host`.

Special targets:

- CONNECT: `host:port` (port required, no default)
- OPTIONS: may be `*`

Servers reconstruct the target URI from received pieces; that reconstruction is version-specific (see RFC 9112 §3.3 for HTTP/1.1). Old name: “effective request URI.”

### 7.2 Host / `:authority`

```http
GET /pub/WWW/ HTTP/1.1
Host: www.example.org
```

User agent MUST send `Host` unless it sends `:authority`. SHOULD send `Host` first. This is application-level routing — a cache-poisoning and malware favorite. Interception proxies that trust `Host` without checking the IP are in trouble.

### 7.3 Inbound routing

Typical client order: cache → configured proxy → scheme handler (http: TCP; https: TCP + TLS).

### 7.4 Misdirected requests

Once parsed, the server decides: process, forward, redirect, error, or drop. `Host` vs connection host/port mismatch can be expected from a trusted gateway; otherwise it is an attack.

Unless the connection is a trusted gateway, an origin MUST reject a request that fails scheme requirements. https MUST have arrived on a connection secured with a cert valid for that origin.

**421 Misdirected Request** is the status for “wrong origin / wrong connection context.”

### 7.5 Correlation

Version-dependent (HTTP/1.1 = order; HTTP/2/3 = stream id). Responses, including 1xx, MAY arrive before the request is finished. Clients SHOULD keep sending the request unless told otherwise. Clients MAY abandon a slow request.

### 7.6 Forwarding

Unrecognized methods, statuses, and fields SHOULD be forwarded (extensibility). Non-tunnels MUST implement `Connection` and strip hop-by-hop fields. MUST NOT forward a message to yourself without loop protection. Incremental streaming is allowed; you cannot *rely* on it.

**Connection** ([§7.6.1](https://www.rfc-editor.org/rfc/rfc9110#section-7.6.1)): hop-by-hop options. If a field is only for this connection, list its name in `Connection`. Intermediaries parse `Connection`, remove those fields, remove `Connection` itself, optionally add their own.

Always strip before forwarding (whether listed or not): `Proxy-Connection`, `Keep-Alive`, `TE`, `Transfer-Encoding`, `Upgrade`.

Never put an end-to-end field (`Cache-Control`) in `Connection`.

**Max-Forwards:** TRACE and OPTIONS only. Decrement; at 0, answer yourself. MAY ignore on other methods.

**Via:** like email `Received`. Each hop appends `protocol-version pseudonym`. Protocol name omitted if HTTP. Example: `Via: 1.0 fred, 1.1 p.example.net`. Firewalls SHOULD pseudonymize internal hosts. MAY collapse adjacent same-protocol hops under one org.

### 7.7 Transformations

A **transforming proxy** changes meaning (transcode, annotate, malware filter). It MUST NOT:

- change hostname if the target URI has an FQDN,
- change absolute-path or query except as the forwarding protocol requires (empty path → `/` or `*` in HTTP/1.1),
- transform content of a response with `Cache-Control: no-transform`.

It MAY transform other content; a transformed 200 MAY become **203**. SHOULD NOT rewrite endpoint/resource/representation metadata unless the field allows it or privacy/security requires it.

### 7.8 Upgrade

Switch protocols **on the same connection** (HTTP/1.1 → WebSocket, etc.). Client offers a preference list; server MAY ignore. Cannot be used to insist.

- 101 + `Upgrade` = switched. MUST only switch to something the client offered. After 101, still finish the original request in the new protocol (no extra round trip).
- 426 + `Upgrade` = please upgrade; here are acceptable protocols.
- Any other response MAY advertise future upgrade options.

Must also send `Connection: upgrade`. Ignore Upgrade on HTTP/1.0. If both Upgrade and `Expect: 100-continue`, send 100 before 101.

Upgrade cannot change the *transport*. For a different connection, use 3xx.

Token `HTTP` is registered for this family; others go in the Upgrade Token Registry.

---

## 8. Representation data and metadata

Two-layer encoding:

```
representation-data := Content-Encoding( Content-Type( data ) )
```

### 8.3 Content-Type

Media type of the enclosed or selected representation, **after** Content-Encoding is undone.

```http
Content-Type: text/html; charset=ISO-8859-4
```

SHOULD send it when content is present. If missing, recipient MAY assume `application/octet-stream` or sniff. Sniffing is a security risk. If the field is wrongly sent multiple times, last-valid-member is a common (and dangerous) recovery.

`media-type = type "/" subtype parameters`. Type/subtype case-insensitive. Register with IANA (BCP 13).

**Charset** names are case-insensitive. Register in the Character Sets registry.

**Multipart** types share MIME syntax and a `boundary` parameter. HTTP framing does **not** use that boundary as message length. `multipart/form-data` (RFC 7578) and `multipart/byteranges` (this RFC, §14.6) are the HTTP-relevant ones. Body part line breaks MUST be CRLF.

### 8.4 Content-Encoding

Codings applied **beyond** the media type, in order applied. `identity` is reserved for Accept-Encoding; SHOULD NOT appear here.

This is a property of the **representation**, unlike `Transfer-Encoding` (HTTP/1.1 hop). Typical use: compress without changing `Content-Type`.

If the media type is inherently compressed, do not restated that in Content-Encoding unless you compressed twice.

Unknown request coding → 415.

Registered here:

| Coding | What |
|---|---|
| compress | LZW (UNIX compress); `x-compress` ≡ compress |
| deflate | zlib wrapper + DEFLATE (some stacks omit the zlib wrapper — nonconformant) |
| gzip | gzip format; `x-gzip` ≡ gzip |

### 8.5 Content-Language

Language(s) of the **intended audience**, not “every language that appears.” `da` = for Danish readers. Multiple: `mi, en` for a dual-language treaty. A Latin primer for English readers is `en`, even though it contains Latin. Applies to any media type. Tags: RFC 5646 (`en-US`, `es-419`, …).

### 8.6 Content-Length

Decimal octet count of the **associated representation data**. When transferring content, it also delimits HTTP/1.1 framing.

- User agent SHOULD send it on requests whose method expects content, including `0` for empty POST; SHOULD NOT send it when there is no content and the method does not anticipate any.
- HEAD: MAY send it; MUST equal the GET body’s length.
- 304: same rule vs a 200 body.
- MUST NOT send on 1xx, 204, or 2xx CONNECT.

MUST NOT forward a known-wrong or non-ABNF length (smuggling / splitting). Exception: `Content-Length: 42, 42` MAY be collapsed to `42`.

Watch integer overflow.

### 8.7 Content-Location

URI of the specific resource **this content** represents. Not a replacement for the target URI.

On 2xx:

- Same as target URI → MAY treat content as current representation of that resource. After PUT/POST this means “here is the new state,” not just “it worked.”
- Different URI on GET/HEAD → more specific identifier for a negotiated representation.
- 201 and equal to `Location` → representation of the new resource.
- Otherwise → a status report also available later at that URI (receipt after POST).

On a request: “this is where I got this content.” Origin MUST treat it as transitory context, MUST NOT change request semantics. PUT to a negotiated resource still replaces **the resource**, not “the variant named by Content-Location.”

### 8.8 Validators

Metadata used in preconditions ([§13](https://www.rfc-editor.org/rfc/rfc9110#section-13)). In safe responses they describe the selected representation. After a successful state change they describe the **new** representation (ETag on 201 is how you avoid lost updates).

**Strong vs weak.**

- **Strong:** changes whenever a 200 GET body would observably change. Unique across all versions of all representations of **that** resource over time (not unique across resources). Needed for ranges and lost-update avoidance.
- **Weak:** might not change on every data change (clock resolution, deliberate grouping). Prefixed `W/` on entity tags. Fine for cache validation when exact bytes do not matter.

Same bytes under two media types MAY share a strong tag. Same bytes gzipped vs not MUST NOT share a **strong** tag (the coded form *is* the representation data). Transfer-Encoding does not create a new tag.

**Last-Modified.** Timestamp the origin believes the selected representation last changed. SHOULD send it. MUST NOT be later than Date. Without a clock, only send if some other clocked system assigned it.

As a validator it is **implicitly weak**, unless you can prove it strong (origin knows no two changes in that second; or cache/client has Date ≥ Last-Modified + 1s from the same clock).

**ETag.** Opaque quoted string, optional `W/`.

```http
ETag: "xyzzy"
ETag: W/"xyzzy"
ETag: ""
```

Avoid backslash in tags (legacy quoted-string unescaping). MAY send in trailers; header is safer.

Comparison:

| ETag 1 | ETag 2 | Strong | Weak |
|---|---|---|---|
| W/"1" | W/"1" | no | match |
| W/"1" | W/"2" | no | no |
| W/"1" | "1" | no | match |
| "1" | "1" | match | match |

Negotiated gzip vs identity: different strong tags (`"123-a"` vs `"123-b"`), plus `Vary: Accept-Encoding`.

---

## 9. Methods

The method is the primary source of request semantics. Case-sensitive; standardized methods are uppercase.

| Method | Meaning | Safe | Idempotent | Cacheable (this RFC) |
|---|---|---|---|---|
| GET | current selected representation | yes | yes | yes |
| HEAD | GET without content | yes | yes | yes |
| POST | process the enclosed representation | no | no | only with explicit freshness + Content-Location = target |
| PUT | replace all current representations with this content | no | yes | no (invalidates) |
| DELETE | remove the URI mapping | no | yes | no (invalidates) |
| CONNECT | tunnel to host:port | no | no | no |
| OPTIONS | communication options | yes | yes | no |
| TRACE | loop-back the request | yes | yes | no |

General-purpose servers MUST implement GET and HEAD. Unknown method → 501. Known but not allowed here → 405 + `Allow`.

### 9.2 Safe, idempotent, caching

**Safe:** client does not request a state change. Logging and ads still happen; the client is not accountable for that. Safe methods: GET, HEAD, OPTIONS, TRACE. Spiders and prefetch rely on this. `?do=delete` on a GET is the resource owner’s bug.

**Idempotent:** N identical requests have the same intended effect as 1. PUT, DELETE, and all safe methods. Automatic retry after a lost response is why this exists. Proxies MUST NOT auto-retry non-idempotent requests. Clients SHOULD NOT retry a failed retry.

**Caching:** a method must explicitly allow it. This RFC defines GET, HEAD, POST; almost all caches only do GET/HEAD. Details: RFC 9111.

### 9.3 Definitions

**GET.** Transfer the current selected representation. Range makes it a partial GET. Content on GET has **no defined semantics**; clients SHOULD NOT send it except by private agreement with the origin; origins SHOULD NOT rely on that (intermediaries). Do not put secrets in GET query strings; use POST if needed ([§17.9](https://www.rfc-editor.org/rfc/rfc9110#section-17.9)).

**HEAD.** Same headers as GET where practical. MAY omit fields only known while generating the body (`Content-Length`, `Vary` on some dynamic resources). Same warning about request content.

**POST.** Resource-specific processing: forms, posting a message, creating a yet-unidentified resource, appending. Almost any status is legal except 206, 304, 416. Created resources → 201 + `Location`. Equivalent to an existing resource → MAY 303 to that URI (cache-friendly GET). Cacheable only with explicit freshness **and** `Content-Location` equal to the POST URI; that stored response can satisfy later GET/HEAD, never another POST.

**PUT.** Create or replace the resource’s state with this representation. Create → 201. Modify → 200 or 204. SHOULD enforce configured constraints (type vs URI); else 409 or 415. Ignore unrecognized fields (do not save them as state). MUST NOT send validators in a successful PUT response unless the stored bytes are **identical** to what was received (so the client knows its copy is current). If you will not apply PUT to *this* URI, 3xx — do not silently apply it elsewhere. POST is for “server picks the URI.” Partial PUT via `Content-Range` is a private extension ([§14.5](https://www.rfc-editor.org/rfc/rfc9110#section-14.5)). Invalidates caches.

**DELETE.** Remove the **mapping**, not necessarily the bytes (like `rm`). 202 = will probably happen; 204 = done, nothing to say; 200 = done, here is a status document. Request content undefined. Invalidates caches.

**CONNECT.** Proxy builds a tunnel. Target is `host:port` with **required** port. 2xx = tunnel starts after headers. No Content-Length or Transfer-Encoding on that 2xx. Proxies SHOULD restrict destination ports (CONNECT to `:25` is an open relay). Data after the request headers is version-specific.

**OPTIONS.** `*` = the server in general (ping). Else = this resource. SHOULD return `Allow` and similar. `Max-Forwards` can target a hop. Request content needs Content-Type; this RFC defines no use for it.

**TRACE.** Final recipient (origin or Max-Forwards=0) echoes the received request, typically as `message/http`. MUST NOT send content. MUST NOT put credentials/cookies in the request. Final hop SHOULD strip sensitive request fields. `Via` is the interesting part.

---

## 10. Message context

### 10.1 Request context

**Expect.** Only standardized expectation: `100-continue`. Means: I have (probably large) content; send 100 if headers are not already enough to reject/redirect/succeed. Client MUST NOT send it without content. Server on HTTP/1.1 MUST immediately 100 or a final status; MUST NOT wait for the body before 100. HTTP/1.0 Expect is ignored. Unknown expectation MAY → 417. After 417, retry without Expect.

**From.** Machine-usable mailbox of a human responsible for a **robot**. SHOULD NOT send without user config. MUST NOT use for access control (it is public and logged).

**Referer** (sic). URI of the resource from which the target was obtained. MUST strip fragment and userinfo. Keyboard/bookmark → omit or `about:blank`. MAY truncate to origin. SHOULD NOT send from a secure referring resource to a different origin unless the referrer allows it. MUST NOT send Referer on unsecured HTTP if the referrer was HTTPS. Intermediaries SHOULD NOT delete Referer when same scheme+host (CSRF protection depends on it).

**TE.** Client transfer-coding / trailer capabilities. `trailers` = do not drop trailer sections. Also HTTP/1.1 transfer-coding preferences with qvalues. Hop-by-hop: send `Connection: TE`.

**User-Agent.** `product["/"version] *(comment)`. SHOULD send. SHOULD be coarse (fingerprinting). MUST NOT put ads in product tokens. Masquerading as another UA invites responses tailored for that UA.

### 10.2 Response context

**Allow.** Methods this resource supports. MUST appear on 405. Empty = none allowed (temporarily disabled). Proxies MUST NOT rewrite it.

**Location.** URI-reference. 201 = created resource. 3xx = redirect target. Relative values resolve against the target URI. If Location has no fragment, a 3xx redirect **inherits** the original request’s fragment. 201 should not play that game. Location is a singleton; commas are legal in URIs so you cannot list. Distinct from Content-Location.

**Retry-After.** HTTP-date or delay-seconds. 503 = when to retry; 3xx = minimum wait before following.

**Server.** Like User-Agent for the origin. MAY send; SHOULD stay coarse (latency + attack surface).

---

## 11. HTTP authentication

A **framework**, not a scheme. Basic = RFC 7617, Digest = RFC 7616. Cookies are a different, custom mechanism.

```
challenge   = auth-scheme [ 1*SP ( token68 / #auth-param ) ]
credentials = auth-scheme [ 1*SP ( token68 / #auth-param ) ]
```

`token68` is base64-ish (URI unreserved + `+/` and padding `=`). Parameters: `token = token / quoted-string`, names unique per challenge, case-insensitive names. New schemes should accept both token and quoted-string.

**401** + `WWW-Authenticate` = origin challenge. **407** + `Proxy-Authenticate` = proxy challenge. Client answers with `Authorization` or `Proxy-Authorization`. Valid credentials that are not enough → **403**, not another 401.

**Realm.** Protection space = origin + realm. Credentials MAY be reused inside that space. A scheme MUST NOT stretch a space off-server unless it defines how. Senders MUST quote realm; recipients should accept token too.

| Field | Direction | Scope |
|---|---|---|
| WWW-Authenticate | origin → client | this resource; proxies MUST NOT modify |
| Authorization | client → origin | this realm; proxies MUST NOT modify; caches treat like `private` |
| Authentication-Info | origin → client | after success; scheme-defined params; MAY be a trailer |
| Proxy-Authenticate | proxy → next client | hop; 407 MUST include it |
| Proxy-Authorization | client → next proxy | consumed by the first proxy that asked |
| Proxy-Authentication-Info | proxy → next client | hop; MAY be a trailer |

List well-known schemes first; many clients choke on unknown schemes. Multiple challenges on one line are also fragile — multiple header lines are safer.

HTTP auth is **stateless**: everything needed MUST be in the request. Binding auth to the TCP connection is out of scope and unsafe unless the connection cannot be stolen ([§16.4.2](https://www.rfc-editor.org/rfc/rfc9110#section-16.4.2)).

---

## 12. Content negotiation

One resource, many representations (format, language, encoding). HTTP defines three visible patterns:

| Pattern | Who chooses | Typical mechanism |
|---|---|---|
| **Proactive** (server-driven) | server, from request prefs | Accept-* |
| **Reactive** (agent-driven) | client, after a menu | 300 / 406 list, then GET |
| **Request content** | client’s *next* request, from server prefs | Accept / Accept-Encoding **on a response**; Accept-Patch (RFC 5789) |

Also real, not specified here: conditional HTML, active scripts, Transparent Content Negotiation (RFC 2295).

HTTP does not know resource meaning. “Sameness” over time is entirely the origin’s selection algorithm.

### 12.1 Proactive — costs

Cannot know what is “best.” Sending prefs on every request is wasteful and a fingerprint. Complicates servers. Hurts shared caches. Server MAY ignore prefs rather than send 406. Send `Vary` for whatever you actually used.

Accept-* apply to **any** response content, including errors.

### 12.4 Shared features

**Absence** of a negotiation field = no preference on that axis. If present and nothing matches: 406 **or** ignore the field.

**qvalues:** `q=` 0.000–1.000, at most 3 decimals. Missing q = 1. `q=0` = not acceptable.

**Wildcards:** `*` = unspecified values. Without `*`, unlisted values are unacceptable. In Vary, `*` means “variance is unlimited.” `Accept: */*;q=0` is how you *ask* for 406 if nothing better exists — the server may still ignore you.

### 12.5 Fields

**Accept.** Media ranges: `*/*`, `type/*`, `type/subtype`, optional parameters, then q. More specific beats less specific. `q` is not allowed as a media-type parameter in the registry. Accept in a **response** = preferred types for the *next* request to this resource.

**Accept-Charset.** Deprecated. UTF-8 won; sending a charset list fingerprints you. Most browsers do not send it.

**Accept-Encoding.** Codings, `identity` (no coding), `*`. Empty field = I want no coding. Missing field = any coding is fine. `identity;q=0` or `*;q=0` without a more specific identity forbids unencoded. On a 415 about codings, echo Accept-Encoding of what you *would* have accepted; do not send it on 415 for other reasons. On 2xx, can advertise “you could have compressed that.”

**Accept-Language.** Language ranges (RFC 4647) + q. Matching scheme is implementation-defined; Basic Filtering matches old RFC 2616. User MUST be able to control this; a UA without that control MUST NOT send the field.

**Vary.** What request bits besides method and URI affected selection.

```http
Vary: accept-encoding, accept-language
```

`*` = maybe even things outside the message (client IP). Proxies MUST NOT generate `*`. Two jobs: (1) expand the cache key (RFC 9111); (2) tell the UA this was negotiated. Do not list `Authorization` (already uncacheable across users). SHOULD send Vary on cacheable negotiated responses; MAY omit if cache directives already prevent reuse and Vary would cost too much.

---

## 13. Conditional requests

Preconditions tested **before** applying the method. Two jobs: cheap cache revalidation, and **lost-update** protection on PUT/DELETE.

Evaluate after normal request checks, before processing content / performing the action. If the same request without conditionals would not be 2xx or 412 (redirects, 401, 404, …), **skip** conditionals. Non-origin, non-cache MUST forward them. Ignore them on CONNECT/OPTIONS/TRACE. HEAD conditionals are legal but pointless.

### 13.1 Fields

**If-Match.** Strong comparison. `*` = “resource exists.” List = any listed tag matches selected representation. False → 412, **or** 2xx if the state change already happened (optional; be strict for semaphores). Origin only; caches MAY ignore. `*` mixed with other tags is invalid.

**If-None-Match.** Weak comparison. GET/HEAD false → **304**. Other methods false → **412**. `*` on PUT = “create only if missing.”

**If-Modified-Since.** GET/HEAD only. Ignored if If-None-Match is present, if the date is invalid, if not GET/HEAD, or if the resource has no modification date. Timestamp is in the **origin’s** clock. False → SHOULD 304.

**If-Unmodified-Since.** Like If-Match when you only have a date. Ignored if If-Match is present. False → 412 or optional 2xx-if-already-applied.

**If-Range.** Only with `Range`. If validator matches **exactly** (strong ETag, or strong Last-Modified exact match), honor Range (206). Else **ignore Range** and send 200 — one round trip instead of 412 then a second GET. Weak tags and weak dates forbidden here.

### 13.2 Precedence

Fixed order ([§13.2.2](https://www.rfc-editor.org/rfc/rfc9110#section-13.2.2)):

1. Origin + If-Match → false: 412 (unless already applied)
2. Origin + no If-Match + If-Unmodified-Since → false: 412 (unless already applied)
3. If-None-Match → false: 304 (GET/HEAD) or 412 (else)
4. GET/HEAD + no If-None-Match + If-Modified-Since → false: 304
5. GET + Range + If-Range → true: 206 if range applies; else 200 ignoring Range
6. Perform the method

Lost-update checks first, then cache validation, then ranges. Entity tags outrank dates.

---

## 14. Range requests

Optional. Unsupported Range on GET → behave like normal GET. Partial success uses a distinct status (206) so dumb caches do not treat it as a full body.

### 14.1 Units

Extensible registered tokens. HTTP defines **bytes**.

Generic specifiers:

```
int-range     = first-pos "-" [ last-pos ]     ; 0-499
suffix-range  = "-" suffix-length              ; last N units
other-range   = 1*(VCHAR except comma)         ; extensions
```

A set is **satisfiable** if at least one spec is.

**Bytes:** 0-based, inclusive, relative to **encoded** representation data (after Content-Encoding). Unknown last-pos or last-pos past the end → rest of the resource. Suffix longer than the resource → whole resource. Zero-length resource: only a non-zero suffix is satisfiable.

```
bytes=0-499          first 500
bytes=500-999        second 500
bytes=-500           last 500
bytes=9500-          last 500 of a 10000-byte resource
bytes=0-0,-1         first and last byte
```

### 14.2 Range

GET only (this spec). Server MAY ignore. MUST ignore unknown methods, unknown units (proxies MAY drop those). MAY reject overlapping / many tiny / out-of-order ranges (DoS). Evaluated only if without Range the result would be 200 (so 304 wins). Satisfiable → SHOULD 206. Unsatisfiable or unknown unit → SHOULD 416. Server need not send every requested range in one go.

### 14.3 Accept-Ranges

```http
Accept-Ranges: bytes
Accept-Ranges: none
```

Advice, not a promise. Client MAY still ask. `none` is reserved to discourage Range. Prefer header over trailer so a failed large transfer can restart.

### 14.4 Content-Range

```
Content-Range: bytes 42-1233/1234
Content-Range: bytes 42-1233/*
Content-Range: bytes */1234          ; 416
```

On 206 (single part), each part of multipart 206, and 416. `*` complete-length = unknown. Invalid ranges MUST NOT be recombined. Only 206 and 416 give this field meaning in this spec.

### 14.5 Partial PUT

Some origins honor `Content-Range` on PUT as “write these bytes at this offset.” Not interoperable; SHOULD 400 if you do not support it. PATCH (RFC 5789) is the defined partial-update method.

### 14.6 multipart/byteranges

Multiple 206 parts. Each part has Content-Type and Content-Range. Required `boundary` parameter. Not actually limited to bytes. Do not send multipart for a single requested range. Client MUST read Content-Range per part; order is not guaranteed. Coalesce overlapping/small-gap ranges when the ~80-byte boundary overhead is not worth it. Combining stored 206s later requires the **same strong validator**.

---

## 15. Status codes

Three digits, 100–599. First digit is the class. Unknown code: treat as `x00` of that class (471 → 400). Outside 100–599: treat as 5xx.

A request has zero or more **interim** (1xx) responses, then exactly one **final** response.

Reason phrases are not normative.

**Heuristically cacheable** (may be reused with heuristic freshness unless the method or Cache-Control says no): 200, 203, 204, 206, 300, 301, 308, 404, 405, 410, 414, 501. Anything with *explicit* freshness can still be cached. See RFC 9111.

### 15.2 1xx Informational

Interim. No content, no trailers. MUST NOT send to HTTP/1.0. Clients MUST parse them even if unexpected. Proxies MUST forward unless they generated the expectation themselves.

**100 Continue.** Headers accepted; send the body. Discard it and keep going.

**101 Switching Protocols.** See Upgrade. Response MUST include Upgrade.

### 15.3 2xx Successful

**200 OK.** Content depends on method:

| Method | 200 content is |
|---|---|
| GET | representation of the target resource |
| HEAD | same metadata, no data |
| POST | status or result of the action |
| PUT, DELETE | status of the action |
| OPTIONS | communication options |
| TRACE | the request as received |
| CONNECT | none (tunnel) |

Prefer 204 if success should have no body. Heuristically cacheable. GET/HEAD SHOULD send validators (strong ETag + Last-Modified preferred). State-changing 200 validators are for the **new** representation; PUT may forbid them ([§9.3.4](https://www.rfc-editor.org/rfc/rfc9110#section-9.3.4)).

**201 Created.** Location or, if missing, the target URI identifies the primary new resource.

**202 Accepted.** Queued; not a promise. Point at a status monitor. No later status replay in HTTP.

**203 Non-Authoritative Information.** Transforming proxy modified a 200. Heuristically cacheable.

**204 No Content.** Success, no body, no trailers. Metadata is the post-action selected representation (ETag after PUT = new tag). Stay on this document view. Heuristically cacheable.

**205 Reset Content.** Success; reset the form/canvas that issued the request. MUST NOT generate content.

**206 Partial Content.** Range success. MUST send Date, Cache-Control, ETag, Expires, Content-Location, Vary if a 200 would have. Content-Length is **this message**, not the whole resource. If-Range: SHOULD omit extra representation headers the client already has.

- Single part: Content-Range + that slice.
- Multiple: `Content-Type: multipart/byteranges; boundary=...`, **no** Content-Range on the HTTP headers (it lives on each part).
- Combining parts: only with a shared **strong** validator; union becomes 200 if complete.

### 15.4 3xx Redirection

Four kinds: new URI (301/302/307/308), pick among representations (300), indirect other resource (303), use your stored copy (304).

History: 301/302 originally method-preserving; browsers ended up changing POST → GET. **307/308** were added to preserve method. 301/302 MAY still rewrite POST to GET.

Following a redirect: replace URI from Location; strip hop-by-hop, proxy, origin, cache-validator, and resource-specific fields (`Referer`, `Origin`, `Authorization`, `Cookie`); change method if the status says so; if method became GET/HEAD, strip content fields. Detect loops. (Some clients still cap at five hops.)

Unsafe methods: do not auto-redirect without care.

**300 Multiple Choices.** Reactive negotiation. Preferred choice in Location. Heuristically cacheable. (The old URI/Alternates headers died; `Link: rel=alternate` is a possible substitute.)

**301 Moved Permanently.** New permanent URI. MAY change POST→GET. Heuristically cacheable. Use **308** to keep POST.

**302 Found.** Temporary. Keep using the old URI later. MAY change POST→GET. Use **307** to keep POST.

**303 See Other.** GET (or HEAD) the Location; that is an **indirect** answer, not “this URI is equivalent.” Classic POST/redirect/GET so the result is bookmarkable and cacheable.

**304 Not Modified.** Conditional GET/HEAD would have been 200. MUST send Content-Location, Date, ETag, Vary, Cache-Control, Expires if a 200 would have. SHOULD NOT send other representation metadata except to guide cache updates. No content, no trailers.

**305 Use Proxy.** Deprecated.

**306.** Unused, reserved.

**307 Temporary Redirect.** Temporary, **must keep method**.

**308 Permanent Redirect.** Permanent, **must keep method**. Heuristically cacheable. Younger (2014); some stacks still miss it.

### 15.5 4xx Client error

Except HEAD, SHOULD send an explanation. Applicable to any method. UAs SHOULD show it.

| Code | When |
|---|---|
| **400** | Malformed syntax, framing, or deceptive routing |
| **401** | Missing/bad credentials; MUST send WWW-Authenticate. Repeat with Authorization. Same challenge twice → show the body |
| **402** | Reserved (“Payment Required”) |
| **403** | Understood, refused. Credentials insufficient **or** unrelated reasons. Do not retry with the same credentials. MAY hide existence with 404 |
| **404** | No current representation, or unwilling to say. Heuristically cacheable. Prefer 410 if known permanent |
| **405** | Method known but not allowed; MUST send Allow. Heuristically cacheable |
| **406** | Proactive negotiation matched nothing and you will not send a default; SHOULD list alternatives |
| **407** | Like 401 for a proxy; MUST send Proxy-Authenticate |
| **408** | Incomplete request, waited too long. Client MAY retry (new connection if HTTP/1.1 delimitation is lost) |
| **409** | Conflict with current state (typical on PUT / versioning). SHOULD explain how to merge |
| **410** | Permanently gone; please drop remote links. Heuristically cacheable |
| **411** | Need Content-Length |
| **412** | A precondition was false |
| **413** | Content too large. MAY Retry-After if temporary. (Old name: Payload Too Large.) |
| **414** | Target URI too long. Heuristically cacheable |
| **415** | Content-Type or Content-Encoding not supported. Use Accept-Encoding and/or Accept to say what would have worked |
| **416** | Range unsatisfiable or abusive; SHOULD send `Content-Range: bytes */length`. Clients cannot rely on 416 — many servers send 200 of the whole resource |
| **417** | Expect cannot be met |
| **418** | Reserved (teapot joke). Cannot be reused unless 4xx is exhausted |
| **421** | Wrong origin or connection context. Client MAY retry on another connection even if the method is not idempotent. Proxies MUST NOT generate 421. Not heuristically cacheable |
| **422** | Type and syntax OK, instructions semantically wrong (well-formed but meaningless XML). From WebDAV, now general |
| **426** | Current protocol refused; MUST send Upgrade |

(Other 4xx exist in RFC 6585 and elsewhere; they are not defined in 9110.)

### 15.6 5xx Server error

Server knows it failed. SHOULD explain. Show it to the user.

| Code | When |
|---|---|
| **500** | Unexpected condition |
| **501** | Method not implemented (for any resource). Heuristically cacheable |
| **502** | Gateway got a bad inbound response |
| **503** | Overload or maintenance; MAY Retry-After. Servers MAY just refuse the connection instead |
| **504** | Gateway got no timely inbound response |
| **505** | This HTTP major version not supported; SHOULD say what is |

---

## 16. Extending HTTP

Semantics are **not versioned**. Extension points persist across HTTP/1.1, 2, and 3:

- methods
- status codes
- field names (and field-internal syntax)
- authentication schemes
- range units
- content codings
- upgrade tokens
- cache directives (RFC 9111)

Version-specific extensions (HTTP/1.1 transfer codings, HTTP/2 frames/SETTINGS) cannot override version-independent meaning unless that element allows it (CONNECT does).

### 16.1 Methods

IANA registry. IETF Review. Must declare safe / idempotent / spec pointer. New methods must be generic, must not change parsing, must not ban content (use `Content-Length: 0` if empty is required), must not use CONNECT’s `host:port` or OPTIONS’ `*`. Document cacheability, conditionals, ranges. Do not start names with `M-` (RFC 2774 baggage). `*` as a method name is reserved (CORS).

### 16.2 Status codes

IANA, IETF Review. Must fall in a 1xx–5xx class. Cannot disallow content. Drafts should say `4NN` until the number is assigned. Define when it is sent, header dependencies, scope (this request vs the resource), heuristic cacheability, and implied content identity ([§6.4.2](https://www.rfc-editor.org/rfc/rfc9110#section-6.4.2)).

### 16.3 Fields

The main extension point. Unknown fields are ignored/forwarded. Registry: permanent / provisional / deprecated / obsoleted. Expert review; permanent is Specification Required.

Names: short, descriptive, limited-use fields prefixed with the app (`Foo-Desc`). SHOULD be `ALPHA *(ALPHA / DIGIT / "-" / ".")`. No `X-` prefix (BCP 178). Underscore is hostile to CGI. `Accept-` and `Content-` are conventional, not magic.

Values: prefer this RFC’s ABNF or Structured Fields ([RFC 8941](https://www.rfc-editor.org/rfc/rfc8941)). Design for accidental list combination — quote commas. Singleton fields should say what to do with duplicates.

Document: request vs response, method/status refinements, scope, intermediary mutation, trailers allowed?, hop-by-hop (`Connection`), security, Vary, PUT storage, redirect stripping.

`*` as a field name is reserved (Vary).

### 16.4 Authentication schemes

IETF Review. Must be stateless. `realm` is reserved. Prefer `auth-param` over a single `token68` (extensibility). Parsing is fixed by this RFC. Unknown params: must-ignore. Say whether origin and/or proxy. Credentials outside `Authorization` must explicitly disable caching. Document Authentication-Info security.

### 16.5 Range units

IETF Review. Think about content codings and intermediaries. `bytes` and `none` are registered.

### 16.6 Content codings

IETF Review. Names MUST NOT collide with transfer codings unless the transform is identical. Prefer self-descriptive formats. `identity` is reserved. `x-compress` / `x-gzip` are deprecated aliases.

### 16.7 Upgrade tokens

First Come First Served. Token stays forever, case-insensitive, responsible party, optional specs and expected versions. IESG can reassign if the owner vanishes. `HTTP` is registered with versions like `2.0`.

---

## 17. Security considerations

Not exhaustive. App security, URI security (RFC 3986 §7), and OWASP sit outside. Caching attacks: RFC 9111 §7. HTTP/1.1 parsing: RFC 9112 §11.

**17.1 Authority.** `http` trusts local DNS and IP routing (DNSSEC helps). `https` is the intended mitigation **if** you verify the cert (hard; see Georgiev et al.). Alt-Svc and ORIGIN can delegate. Phishing attacks *perception* of authority; reject userinfo; do not send stored credentials from an untrusted referrer.

**17.2 Intermediaries.** On-path by design. Shared caches are poisoning targets. HTTP cannot make a proxy trustworthy.

**17.3 Path names.** `..`, device names (`COM1`), case folding, Unicode normalization. DoS or file disclosure.

**17.4 Injection.** Method, URI, fields, and content are untrusted. SQL/command/script injection. Compare to fixed strings; encode before interpreters; same when writing logs.

**17.5 Length.** Slowloris / huge fields. 414 and 413 exist. Limit methods, reason phrases, names, numbers, chunk lengths — overflows become RCE or DoS.

**17.6 Shared-dictionary compression.** BREACH-style size leaks when attacker-controlled and secret data share a compressor dictionary. Disable compression on secrets, or isolate dictionaries. HPACK is designed with this in mind.

**17.7 Personal data** on the client (names, keys, history). Do not leak it.

**17.8 Server logs.** Confidential; often legally constrained. Anonymization is not enough if traces re-identify. Purge IPs, user ids, query params when operationally done.

**17.9 Secrets in URIs.** URIs are displayed, logged, bookmarked. Prefer POST (or filter/transform, or redirect to a clean URI) for sensitive form data. Referer leaks the previous URI; see §10.1.3.

**17.10 Field names vs CGI.** `Content-Length` → `CONTENT_LENGTH`; `Content_Length` → `HTTP_CONTENT_LENGTH`. `Transfer_Encoding` vs `Transfer-Encoding` can smuggle. Map the full octet range unambiguously. This is why new names should avoid `_`.

**17.11 Fragments after redirect.** Fragments are not sent, but a 3xx Location without a fragment **inherits** the old one — possibly leaking site A’s fragment to site B. If you use PII in fragments, put an explicit (even empty) fragment on outbound redirects.

**17.12 Product tokens.** User-Agent / Server / Via help attackers slightly; they will brute-force anyway. Firewall proxies should pseudonymize Via.

**17.13 Fingerprinting.** Accept-* are the quiet ones. Accept-Language can imply ethnicity. Proxies that exist for privacy should strip proactive negotiation. User-Agent detail, From, and Cookies (when enabled) also identify.

**17.14 Validators** are not integrity, not anti-tamper. A per-user ETag with long freshness is a supercookie. Clear caches when the user clears cookies / uses private mode.

**17.15 Range DoS.** Many overlapping tiny ranges are cheap to ask and expensive to serve. Ignore, coalesce, or 416.

**17.16 Auth.**

- Confidentiality of credentials is **the scheme’s** problem plus TLS. User-specific credentials identify the user even if opaque.
- HTTP has no “forget these credentials” signal. Idle clients and logout UIs need scheme-level expiry and a user-visible discard.
- Realm-only spaces share credentials across every resource on the origin — bad for multi-tenant hosts. Use different host or port per tenant; do not expose raw Authorization to apps.
- Authentication-Info on cleartext announces that auth happened and may leak scheme params.

---

## 18. IANA considerations

Change controller: IETF.

| Registry | What RFC 9110 put there |
|---|---|
| URI schemes | `http`, `https` |
| Methods | CONNECT, DELETE, GET, HEAD, OPTIONS, POST, PUT, TRACE; `*` reserved |
| Status codes | all of §15 (100, 101, 200–206, 300–308, 400–418, 421, 422, 426, 500–505) |
| Field names | the permanent table below; `*` reserved; Content-MD5 obsoleted |
| Auth schemes | procedure only; none defined here |
| Content codings | compress, deflate, gzip, identity (reserved), x-compress, x-gzip |
| Range units | bytes, none |
| Media types | `multipart/byteranges`; note on `q` parameters |
| Ports | 80 and 443 references |
| Upgrade tokens | `HTTP` |

Fields registered as permanent in this RFC: Accept, Accept-Charset (deprecated), Accept-Encoding, Accept-Language, Accept-Ranges, Allow, Authentication-Info, Authorization, Connection, Content-Encoding, Content-Language, Content-Length, Content-Location, Content-Range, Content-Type, Date, ETag, Expect, From, Host, If-Match, If-Modified-Since, If-None-Match, If-Range, If-Unmodified-Since, Last-Modified, Location, Max-Forwards, Proxy-Authenticate, Proxy-Authentication-Info, Proxy-Authorization, Range, Referer, Retry-After, Server, TE, Trailer, Upgrade, User-Agent, Vary, Via, WWW-Authenticate.

HTTP field registration moved out of RFC 3864’s message-header registries into <https://www.iana.org/assignments/http-fields/>.

---

## 19. References (the map around 9110)

Normative companions you actually need:

| RFC | Role |
|---|---|
| **9110** (this) | Semantics |
| **9111** | Caching |
| **9112** | HTTP/1.1 messaging |
| **9113** | HTTP/2 |
| **9114** | HTTP/3 |
| **3986** | URI |
| **8446** | TLS 1.3 |
| **5234 / 7405** | ABNF |
| **2046** | Media types / multipart |
| **5646 / 4647** | Language tags / matching |
| **6125** | Cert identity (DNS-ID) |
| **5322** | IMF dates |
| **1950/1951/1952** | zlib / deflate / gzip |

Informative neighbors you will hit in the wild: cookies (6265), PATCH (5789), WebDAV (4918), Alt-Svc (7838), ORIGIN (8336), extra statuses (6585), Structured Fields (8941, now revised as 9651), Digest auth (7616), Basic (7617).

---

## Appendix A — collected ABNF

Appendix A expands every `#` list into ordinary ABNF so you can feed it to a parser. You do not need to memorize it. The important productions are the ones already taught:

- `token`, `quoted-string`, `OWS` / `RWS` / `BWS`
- `HTTP-date` / `IMF-fixdate`
- `media-type`, `weight` / `qvalue`
- `entity-tag`, `ranges-specifier`
- `challenge` / `credentials`
- `http-URI` / `https-URI`

If you implement a header parser, implement **list combination + empty-element tolerance** from §5.6.1, not a naive split-on-comma.

---

## Appendix B — what changed vs the 2014 HTTP/1.1 split

**B.1 RFC 2818 (HTTP over TLS):** none (absorbed).

**B.2 RFC 7230 (semantic parts):** origin/authority rewritten for Alt-Svc and non-TCP https; must check target URI scheme; empty parameters via trailing `;`; “field value” = combined lines; trailers are version-agnostic and harder to merge; absolute-form vs Host made explicit; Via `received-by` simplified (no comma in host).

**B.3 RFC 7231:** 8000-octet URI recommendation; CR/NUL handling; **message abstraction** across versions; “payload” renamed **content**; “effective request URI” → **target URI**; looser retries; GET/HEAD/DELETE bodies called out as non-interoperable; partial PUT allowed as extension; OPTIONS Content-Length rule removed; TRACE `message/http` no longer mandatory; Expect list grammar restored; Accept / Accept-Encoding allowed on responses; accept-ext removed; **Accept-Charset deprecated**; Vary `*` clarified; 308 and 421 and 422 added here; 421 not heuristically cacheable.

**B.4 RFC 7232:** Last-Modified strong-validator 60s rule relaxed; 2xx-if-already-applied edge case loosened; If-Unmodified-Since ignored when no modification time; preconditions evaluated before processing content.

**B.5 RFC 7233:** range grammar genericized; Range on extension methods possible; partial PUT described.

**B.6–B.8:** no semantic delta from 7235 / 7538 / 7615 beyond integration.

**B.9 RFC 7694:** client-initiated Content-Encoding (Accept-Encoding on responses) included without that RFC’s examples.

---

## A single mental model to keep

```
client  --request (method + target URI + fields + content)-->  [cache / proxy / gateway]
                                                                 |
                                                                 v
                                                            origin + resource
                                                                 |
client  <--response (status + fields + content)--               selected representation
```

Everything in RFC 9110 is one of:

1. **Who is allowed to answer** (origin, https authority, 421)
2. **What you asked to happen** (method: safe / idempotent / cacheable)
3. **Which bytes you meant** (representation metadata, negotiation, validators, ranges)
4. **Whether it is allowed to happen yet** (auth, Expect, preconditions)
5. **What happened** (status class + specific code)
6. **How hops cooperate** (Host, Connection, Via, Upgrade, transformations)
7. **How to extend without a new version** (registries)

HTTP/1.1, HTTP/2, and HTTP/3 are three ways to ship that same object. When something “HTTP” confuses you, ask which of those seven bins it sits in — then read the matching section above, then the RFC.

---

## Status-code pocket card

```
1xx  wait          100 continue body     101 switched protocol
2xx  done          200 here it is        201 created (Location)
                   202 queued            203 proxy transformed
                   204 done, stay        205 done, reset form
                   206 here is a slice
3xx  go elsewhere  300 pick one          301/308 permanent (308 keeps method)
                   302/307 temporary     303 GET this other URI
                   304 use your copy
4xx  your problem  400/414/417 syntax    401/407 authenticate
                   403/404/410 access    405 method  406 negotiate
                   408/413 capacity      409/412/422 state
                   415 type              416 range   421 wrong origin
                   426 upgrade
5xx  my problem    500/501 me            502/504 my upstream
                   503 later             505 your HTTP major
```

## Method pocket card

```
GET     read        safe  idempotent  cacheable
HEAD    read meta   safe  idempotent  cacheable
POST    process     —     —           only with freshness + Content-Location
PUT     replace     —     idempotent  invalidates
DELETE  unmap       —     idempotent  invalidates
CONNECT tunnel      —     —           —
OPTIONS probe       safe  idempotent  —
TRACE   echo        safe  idempotent  —
```

---

*End of teaching pass. When you implement, implement the RFC text; this file is the map.*
