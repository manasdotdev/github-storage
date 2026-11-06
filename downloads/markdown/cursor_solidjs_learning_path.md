# SolidJS learning path
_Exported on 9/7/2026 at 00:58:35 GMT+5:30 from Cursor (3.19.7)_

---

**User**

https://v2.solidjs.com/concepts/reactivity 
the thing is i want to learn and master solidv2 from scratch to senior dev level, this will be the only framework i will be learning and focusing for all my project, 

your job is to teach me in such a manner and style, that i am able to grasp and deeply understand not just solidjs, but the entire web frontend, cause i have a every little to moderate experience in web,, like i have only created basic todolist static application in react, and nothing more, 

i have no knowlege of mutaition, server funciton, server state, etc and all that advanced topics 

use the @AGENTS.md file for v2 refernece and new methods, also you dont have to teach me all the stuff all at once, break it down into chunks and tackle it

---

**Cursor**

I'll start from the project’s Solid v2 guide and the reactivity docs you linked, then we’ll learn in small chunks instead of dumping the whole framework at once.

Your project already has a Counter I can use as a real example. I’ll read that plus the v2 signal/effect docs so Chunk 1 matches this codebase, not React habits.

We’ll treat this as a long course, not a dump. One idea today: **the UI is a live graph of values, not a function that re-runs.** That is the difference between “I can copy a todo list” and actually understanding Solid — and most of the web.

---

## The map (so you know where we are going)

We will **not** touch these yet: routing, `fetch`, `Loading`, `Errored`, queries, mutations, server functions, SSR. Your `repos.tsx` and `users/[id].tsx` already use some of that. Ignore them for now. They will make sense later.

| Phase | What you learn | Web skill that comes with it |
|---|---|---|
| **1. Now** | Signals + “components run once” | DOM, events, what a click actually does |
| **2** | Derived values vs memos | When work is cheap vs when to cache |
| **3** | Effects (Solid 2’s two-phase model) | Side effects: logging, `localStorage`, timers |
| **4** | Lists, conditionals, stores | Real UI: show/hide, lists that stay in sync |
| **5** | Forms + client state | Inputs, events, controlled vs uncontrolled |
| **6** | URLs + routing | What a “page” is, params, navigation |
| **7** | Async: fetch, loading, errors | Promises, HTTP, what “server state” means |
| **8** | Mutations + cache | POST/PATCH, updating after a write |
| **9** | Server functions / SSR | What the server vs browser each do |
| **10** | Architecture, testing, diagnostics | How a senior thinks about this stack |

Each chunk: I explain → we look at **your** code → you get a tiny exercise → you come back with questions or the result. Then we continue.

---

## Chunk 1 — What is actually on screen?

### The browser, stripped down

Three files (or three kinds of code) make a webpage:

1. **HTML** — the structure: “there is a button, there is a heading.”
2. **CSS** — how it looks: color, spacing, layout. Your project uses Tailwind (`class='...'`). Same idea, shorter syntax.
3. **JavaScript** — behavior: “when they click, change the number.”

The browser turns HTML into a tree of objects called the **DOM** (Document Object Model). Every tag becomes a node you can change:

- `button.textContent = "Clicks: 1"` — change the text
- `button.addEventListener("click", ...)` — run code on click

A “static” page never changes those nodes after load. A “dynamic” app (todo list, dashboard, this Solid app) **keeps changing the DOM** as data changes.

That is the entire job of a frontend framework: **keep the DOM in sync with your data, without you writing `querySelector` and `textContent` by hand for every piece.**

React’s strategy: “when data changes, re-run the whole component function, produce new HTML-like output, diff it, patch the DOM.”

Solid’s strategy: “run the component **once**. Wire each piece of the DOM directly to the data it depends on. When that data changes, update **only that piece**.”

`AGENTS.md` says this in one line: *components run once (there is no re-render), reactivity is fine-grained through signals.*

If you keep a React picture in your head (“this function runs again”), Solid will feel cursed. Drop that picture.

---

### The four-step mental model (from the v2 docs)

This is the whole reactivity system. Memorize it.

1. A **signal** (or later, a store property) is a source of changing data.
2. **Reading** that source *inside a tracking scope* subscribes that scope to it.
3. **Writing** stages a new value and notifies subscribers.
4. Solid applies the updates in one pass, on a **microtask** (very soon, not instantly).

Dependencies are whatever was **actually read on the last run**. Take a different `if` branch → old reads drop off, new ones subscribe. The graph is live, not declared up front.

---

### A signal is two functions, not a variable

Your project already has this. Look at `Counter.tsx`:

```1:10:src/components/Counter.tsx
import { createSignal } from 'solid-js';

export default function Counter() {
  const [count, setCount] = createSignal(0);
  return (
    <button class='cursor-pointer rounded border border-slate-300 px-6 py-2 text-base hover:bg-slate-100' onClick={() => setCount(count() + 1)} type='button'>
      Clicks: {count()}
    </button>
  );
}
```

`createSignal(0)` returns:

| Name | What it is | How you use it |
|---|---|---|
| `count` | **accessor** (a function) | `count()` → current number |
| `setCount` | **setter** | `setCount(1)` or `setCount(c => c + 1)` |

`count` is **not** the number `0`. It is a function that *returns* the current number. That is why it is `count()`, not `count`.

Why a function? Because **calling it is how Solid notices the read**. If it were a plain variable, Solid could not know who is listening.

Two places in this file read `count()`:

1. `{count()}` in JSX — this is a **tracking scope**. Solid wires that text node to the signal. When `count` changes, **only that text** updates. The `Counter` function does **not** run again.
2. `setCount(count() + 1)` inside `onClick` — this is **not** a tracking scope. It is a one-time read at click time: “what is the value *right now*, plus one.” That is correct. You are not trying to subscribe; you are computing the next value.

The v2 docs prefer the updater form when several writes can pile up:

```ts
setCount((value) => value + 1);
```

Same idea: “give me the latest staged value, add one.” We will use that from now on.

---

### The rule that will save you weeks of confusion

**Component bodies run untracked.**

This is legal TypeScript and a **bug** in Solid:

```tsx
export default function Counter() {
  const [count, setCount] = createSignal(0);
  const snapshot = count(); // read at the top of the component
  return <button>Clicks: {snapshot}</button>; // always 0 forever
}
```

`Counter()` runs **once**, when the component is created. `snapshot` is a plain number, `0`. Nothing is subscribed. The button never updates.

In v2 this also produces a **dev warning**. The docs say: reactive reads belong in JSX (or another tracking scope), not at the top of the component.

Correct:

```tsx
return <button>Clicks: {count()}</button>;
```

`{count()}` is inside JSX. JSX expressions **are** tracking scopes. That is the subscription.

Think of it like this:

```
Counter() runs once
  └─ creates the signal
  └─ creates the <button>
       └─ the text "{count()}" is a live listener
       └─ onClick is a listener for the click event (browser, not Solid)
```

After that, `Counter` is done. Clicks only run the `onClick` function. That writes the signal. The signal notifies the text node. The text node updates. Nobody re-runs `Counter`.

---

### What happens on a click, in order

You click the button.

1. The **browser** fires a `click` event (this is the DOM, not Solid).
2. Your `onClick` runs: `setCount(count() + 1)`.
3. The setter **stages** `1`. It does not immediately rewrite the DOM.
4. Solid queues an update (a microtask — “after this click handler finishes”).
5. Solid **flushes**: anything that read `count()` and is subscribed runs again. Here, only the `{count()}` text.
6. That text node becomes `"Clicks: 1"`.

Why the delay? So if you do `setCount(1); setCount(2); setCount(3)` in one handler, the DOM updates **once** to `3`, not three times. That is **batching**. Seniors care about this because it is why the UI stays fast.

Your test already shows this:

```8:16:src/components/Counter.test.tsx
  test('it increments on click', () => {
    const { getByRole } = render(() => <Counter />);
    const button = getByRole('button');
    expect(button).toHaveTextContent('Clicks: 0');
    fireEvent.click(button);
    // Solid batches DOM updates; flush() applies them synchronously.
    flush();
    expect(button).toHaveTextContent('Clicks: 1');
  });
```

In the real browser you never call `flush()`. The microtask runs on its own and you see the new number. In tests, you call `flush()` so the assertion does not run *before* Solid has applied the write.

Remember `flush()` as: “apply pending signal writes *now*.” App code almost never needs it. Tests and weird imperative interop do.

---

### Derived values: a function is enough (most of the time)

From the same docs page you linked:

```tsx
const [count, setCount] = createSignal(0);
const doubled = () => count() * 2;

return (
  <button onClick={() => setCount((v) => v + 1)}>
    {count()} × 2 = {doubled()}
  </button>
);
```

`doubled` is **not** a memo. It is a plain function. When JSX calls `doubled()`, that call happens *inside* the JSX tracking scope, so the read of `count()` inside `doubled` still counts as a dependency of that JSX.

Rule we will keep using:

- Used in **one** place in JSX → a function is enough.
- Used in **many** places, or expensive, or you need an equality boundary → `createMemo`. That is Chunk 2.

Do **not** reach for `createEffect` to compute `doubled`. Effects are for talking to the outside world (console, DOM APIs, subscriptions). Derived data stays in functions or memos.

---

## What you should do before Chunk 2

Do this in your head or in a scratch file. Do **not** skip ahead to `repos.tsx`.

**1. Trace it.** For the Counter you already have, write down (on paper is fine):

- What runs once?
- What runs on every click?
- What is subscribed to `count`?

**2. Predict.** If someone writes this, what appears on screen after 3 clicks, and why?

```tsx
function Broken() {
  const [count, setCount] = createSignal(0);
  const label = `Clicks: ${count()}`;
  return (
    <button type="button" onClick={() => setCount((v) => v + 1)}>
      {label}
    </button>
  );
}
```

**3. Predict the other way.** This one:

```tsx
function Fine() {
  const [count, setCount] = createSignal(0);
  const label = () => `Clicks: ${count()}`;
  return (
    <button type="button" onClick={() => setCount((v) => v + 1)}>
      {label()}
    </button>
  );
}
```

Why does wrapping `label` in a function change everything?

Reply with your answers (wrong is useful). I will correct them, then we do **Chunk 2: memos, equality, and when a function is not enough** — still no server, no fetch.

If any sentence above felt fuzzy (“what is a microtask?”, “what is the DOM really?”), say which one. We fill that hole before moving on. That is how this stays senior-level instead of becoming a pile of APIs you half-remember.

---

**User**

i think i got everything till now, let's move on Chunk 2

---

**Cursor**

You said you’re ready, so I’ll pull the v2 memo docs and your notes, then we’ll do Chunk 2 only: derived functions vs memos, and when a function is not enough.

Your notes are solid. I’ll pull the v2 `createMemo` API and equality rules so Chunk 2 matches this version, not Solid 1.

Found the official Solid 2 `createMemo` page. I’ll read that plus signal equality so this lesson matches v2, not the old API.

Your notes are in good shape. One leftover React word to drop: there are **no subsequent re-renders**. A top-level `count()` is a plain number, frozen at create time. The component never comes back to refresh it.

Now Chunk 2.

---

## Chunk 2 — A function is a recipe. A memo is a cached node in the graph.

### The web idea first (this is not Solid-specific)

Any time you have data `A` and you can compute `B` from `A`, you have two choices:

1. **Recompute `B` whenever someone asks for it.** Cheap. No extra memory. If two people ask, you do the work twice.
2. **Compute `B` once, remember it, and only redo it when `A` changes.** That is a cache. Extra node. Extra rules. Worth it when the work is expensive **or** when many things need the same answer **or** when you want to say “this output did not actually change, so stop the ripple.”

In the browser that last part matters a lot. Updating the DOM is work. If a heading already says `"even"` and the number went from 2 to 4, rewriting that heading is wasted. A senior frontend instinct is: **do not notify downstream if the derived answer is the same.**

Solid’s name for that cached node is `createMemo`.

---

### Two shapes you already met

**Recipe (plain function)** — from Chunk 1:

```tsx
const [count, setCount] = createSignal(0);
const doubled = () => count() * 2;

return <button>{count()} × 2 = {doubled()}</button>;
```

`doubled` is not reactive by itself. It has no cache. It has no subscribers. When JSX calls `doubled()`, that call happens **inside** the JSX tracking scope, so `count()` is a dependency of **that JSX**, not of `doubled`. Call it from two JSX spots and the math runs twice. For `* 2`, that is what you want.

**Cached node (memo)** — Solid 2:

```tsx
const [first, setFirst] = createSignal("Ada");
const [last] = createSignal("Lovelace");

const fullName = createMemo(() => `${first()} ${last()}`);
```

`createMemo` does three things a function does not:

1. It creates **its own tracking scope**. The compute function is that scope. `first()` and `last()` subscribe **the memo**, not whoever later calls `fullName()`.
2. It **stores** the last result. Later `fullName()` calls return that stored string until a dependency changes.
3. After a recompute, it asks: **is the new result equal to the old one?** Only if not, it notifies whoever read `fullName()`.

`fullName` is an accessor, same calling convention as a signal: `fullName()`. It is **read-only**. There is no `setFullName`. You change it by changing `first` or `last`.

Creating the memo at the top of a component is fine. That is **building a node**, not reading a value. This is the bug:

```tsx
const fullName = createMemo(() => `${first()} ${last()}`);
const snapshot = fullName(); // untracked read, frozen string, same class of bug as Chunk 1
```

Reads of the memo still belong in JSX or another tracking scope.

---

### Picture the graph

Recipe — no middle node:

```text
count  ──►  JSX "{doubled()}"     (JSX itself subscribed to count)
```

Memo — a real node:

```text
first ──┐
        ├──►  fullName (memo, cached)  ──►  JSX "{fullName()}"
last  ──┘                              └──►  JSX "{fullName()}" again
```

Two JSX expressions that both call `fullName()` share **one** computation. They subscribe to the memo, not directly to `first` / `last`.

That is why the v2 docs say: use a memo when **multiple consumers need the same derivation**, or when the value should form an **equality boundary**.

---

### The equality boundary (this is the senior part)

Default comparison is reference equality (`===` / Solid’s `isEqual`). Numbers and strings compare by value. Objects compare by identity: a newly created `{ name: "Ada" }` is never `===` the previous `{ name: "Ada" }`.

Watch this:

```tsx
const [count, setCount] = createSignal(1);
const isPositive = createMemo(() => count() > 0);

return <p>{isPositive() ? "positive" : "not"}</p>;
```

| write | `count` | memo recomputes? | new result | notify the `<p>`? |
|---|---|---|---|---|
| start | `1` | yes (first run) | `true` | yes, first paint |
| `setCount(5)` | `5` | yes | `true` | **no** — `true === true` |
| `setCount(9)` | `9` | yes | `true` | **no** |
| `setCount(-1)` | `-1` | yes | `false` | **yes** |

The memo **did** re-run (its inputs changed). Downstream **did not**. The `<p>` is subscribed to `isPositive`, not to `count`. That is the boundary.

Without the memo, `{count() > 0 ? "positive" : "not"}` still works, and for one cheap expression you should just write that. The memo earns its keep when something **expensive** or **many things** sit behind that boolean.

Custom equals, when you need them:

```tsx
const userId = createMemo(() => user(), {
  equals: (a, b) => a.id === b.id,
});
```

Or `{ equals: false }` to notify on every recompute even if `===` would say “same.” You will almost never need that in app code. Default is correct for strings, numbers, booleans.

**Object trap:**

```tsx
const card = createMemo(() => ({ name: first() }));
```

Every time `first` changes, you allocate a **new object**. Downstream always updates, even if you later add another input that changes while `name` stays the same. If you only care about the string, memoize the string: `createMemo(() => first())`. If you need an object and want value equality, pass a custom `equals`.

---

### Same batching rule as signals

From the v2 docs, this is not a special memo quirk — it is the scheduler you already learned:

```ts
setFirst("Grace");
fullName(); // still "Ada Lovelace" until the microtask flush
```

In the app, you wait; the next paint has the new name. In a test, `flush()` then read. Do not call `flush()` in components.

---

### Decision rule (put this in `markdown.md`)

Before `createMemo`, ask:

1. Is this used in **one** JSX expression and cheap? → `const x = () => ...` or write it inline.
2. Is it used in **several** places, or expensive (filter a big list, format, derive a key)? → `createMemo`.
3. Do I need to **stop updates** when the output is unchanged (`isPositive`, `hasItems`, `label`)? → `createMemo` as an equality boundary.
4. Am I about to `createEffect` + `setSomething` to keep a copy of derived data? → **stop.** That is the React-shaped mistake. Derive it.

The official guide is blunt: do not copy derived data into another signal with an effect. A function or a memo cannot go stale independently of its inputs.

Name memos while you learn (`{ name: "fullName" }`). `AGENTS.md` uses those names when you later debug “why did this run.”

---

### What you will see in *this* repo — and should ignore for now

```5:8:src/routes/repos.tsx
  const repository = createMemo(async () => {
    const result = await fetch('https://api.github.com/repos/TanStack/query');
    if (!result.ok) throw new Error('Failed to fetch data');
    return result.json();
```

That is an **async memo**. Same idea (cached derived value), extra rules (`Loading`, pending, `loadingValue`). Chunk 7. Do not open that rabbit hole yet.

Also exists, not today: `createSignal(() => someDerivation)` — a **writable memo** (local override on top of a derivation). That is forms, later.

---

### Worked contrast (one list, two readers)

Imagine a search box and a long name list.

**Wrong instinct:** wrap everything in memos.

**Right instinct:**

```tsx
const [query, setQuery] = createSignal("");

const filtered = createMemo(
  () => NAMES.filter((n) => n.toLowerCase().includes(query().toLowerCase())),
  { name: "filteredNames" },
);

return (
  <>
    <p>Count: {filtered().length}</p>
    <ul>
      {/* later we will use <For> — for now, imagine a list that reads filtered() */}
    </ul>
  </>
);
```

`filter` is not free, and **two** consumers need the same array. One memo, one filter pass. `query()` updates → memo recomputes → if the array identity/result changes, both the count and the list update together.

`query().toLowerCase()` used in one placeholder? No memo.

---

## Exercises (same format as Chunk 1)

**1. Count the work.** After a click, how many times does `count() * 2` run?

```tsx
const [count, setCount] = createSignal(0);
const doubled = () => count() * 2;

return (
  <>
    <span>{doubled()}</span>
    <span>{doubled()}</span>
  </>
);
```

If you change `doubled` to `createMemo(() => count() * 2)`, how many times does the multiply run?

**2. Boundary.** `isPositive` as above, starting at `1`. You call `setCount(2)`, then `setCount(3)`, then `setCount(0)`. The `<p>` only reads `isPositive()`. When does the text change, and to what?

**3. Pick one.** For each, function or memo — and one sentence why.

- `const tax = () => price() * 0.18` shown in a single `<span>`
- `const visible = items().filter(i => i.done === showDone())` used for a count **and** a list
- `const title = () => tab() === "a" ? "Account" : "Billing"` in one `<h1>`

**4. Identity.** Why will this notify downstream on every `tick`, even when `first` is unchanged?

```tsx
const [first, setFirst] = createSignal("Ada");
const [tick, setTick] = createSignal(0);

const card = createMemo(() => {
  tick();
  return { name: first() };
});
```

Reply with answers (wrong is useful). Then we do **Chunk 3: effects** — the two-phase Solid 2 model, and the rule “almost never use an effect for data.” Still no fetch, no server.

---

**User**

 3. Pick one. For each, function or memo — and one sentence why.

const tax = () => price() * 0.18 shown in a single <span>
const visible = items().filter(i => i.done === showDone()) used for a count and a list
const title = () => tab() === "a" ? "Account" : "Billing" in one <h1

1. function
2. memo
3. memo

4. Identity. Why will this notify downstream on every tick, even when first is unchanged?

didnt understand, ?


---

**Cursor**

**#3 is the miss.** `tax` → function and `visible` → memo are right. `title` should be a **function**, not a memo. Then #4 is a JavaScript identity thing, not a Solid trick.

---

### Exercise 3

| | Your pick | Correct | Why |
|---|---|---|---|
| `tax` in one `<span>` | function | function | Cheap multiply, one reader. A memo adds a graph node for nothing. |
| `visible` for a count **and** a list | memo | memo | `filter` is real work, and two consumers need the **same** array. One pass, shared result. |
| `title` in one `<h1>` | memo | **function** | Cheap `===` + ternary, one place. Write `{tab() === "a" ? "Account" : "Billing"}` or a `() =>` helper. |

Why `title` feels like a memo: it only has two possible strings, so you might think “equality boundary.” That boundary only matters when **something expensive** sits *behind* the derived value. Here the only consumer is one heading. If `tab()` goes `"a"` → `"b"`, you *want* the `<h1>` to update. If `tab()` is set to `"a"` again, the **signal’s** own `equals` already stops the write. The memo would not earn its keep.

Rule again: **one cheap JSX use → function. Several readers or expensive work → memo.**

---

### Exercise 4 — why `tick` still notifies, even when `first` is `"Ada"`

This is how JavaScript compares **objects**, plus the memo rule you already learned.

**Two kinds of “same” in JS**

```js
5 === 5                 // true  — numbers compare by value
"Ada" === "Ada"         // true  — strings compare by value

{ name: "Ada" } === { name: "Ada" }  // false
```

The last one is `false` because `{ name: "Ada" }` means **create a new object in memory**. Two objects with the same fields are still two different objects. `===` asks “is this the **same box**?”, not “do the fields match?”

A memo’s default `equals` uses that same `===`.

**What the memo does on each `tick`**

```tsx
const card = createMemo(() => {
  tick();                 // read → memo is subscribed to tick
  return { name: first() }; // always allocates a new object
});
```

1. `tick()` is a read. Changing `tick` **recomputes** the memo, even if you throw the number away.
2. `first` is still `"Ada"`, so the *contents* you want are unchanged.
3. The `return` line still runs ` { name: "Ada" } ` — a **new** object.
4. Memo compares old object `===` new object → `false`.
5. Downstream that read `card()` gets notified. The `<p>` or whoever updates, even though `name` is still `"Ada"`.

So: **`tick` forces a recompute. The new object fails `===`. Notification is correct given those rules, and useless for your UI.**

If the memo returned the string instead:

```tsx
const name = createMemo(() => {
  tick();
  return first(); // "Ada" === "Ada" → no notify
});
```

`tick` still recomputes, but `equals` says the result is the same, so downstream stays still.

If you need an object and care about fields, you pass a custom comparator:

```tsx
const card = createMemo(() => ({ name: first() }), {
  equals: (a, b) => a.name === b.name,
});
```

(You would still not read `tick()` unless you actually need it.)

**Tiny picture**

```text
tick changes
  → memo runs again
  → new { name: "Ada" }  (different box, same label)
  → === fails
  → JSX updates for no visible reason
```

That is the identity trap: **a new object is always “changed,” even when every field is the same.**

---

If that click is clear, say so (or ask about the `===` part). Then we go to Chunk 3 — effects.

---

**User**

it's clear, move to chuck 3

---

**Cursor**

Chunk 3 is effects. I’ll pull the Solid 2 `createEffect` docs so we teach the two-phase model, not the old one-function API.

Chunk 3 is the exit door of the graph: **when a value must leave Solid and touch the real world.** Most of the time you will not need this.

---

## Chunk 3 — Effects (Solid 2’s two-phase model)

### The web idea: calculation vs side effect

A **calculation** stays inside your program:

```ts
const doubled = count() * 2;
```

Same input → same output. Nothing in the browser changes except the number in memory.

A **side effect** changes the world *outside* that calculation:

- `console.log(...)` — the terminal
- `document.title = "..."` — the browser tab
- `localStorage.setItem(...)` — disk
- `setInterval` / `addEventListener` / `WebSocket` — subscriptions
- a third-party map widget’s `.setCenter(...)`

The DOM text `{count()}` is **not** something you do in an effect. JSX already wires that. Solid’s job is the DOM. An effect is for systems **Solid does not own**.

React muscle memory is “when something changes, `useEffect`.” In Solid that is usually wrong. Derived data → function or memo. Clicks → `onClick`. Effects are the last resort.

---

### Solid 2: two functions, not one

v2 `createEffect` is **two phases**. The old one-argument form is gone (`MISSING_EFFECT_FN` if you write it).

```ts
createEffect(
  () => count(),                          // 1. compute — tracked
  (value, previous) => console.log(value) // 2. effect  — untracked
);
```

| Phase | When | Tracking? | What belongs here |
|---|---|---|---|
| **compute** | When a dependency changes (batched, then flush) | **Yes** | Every `count()`, `userId()`, store read that should *retrigger* this effect. Return the value the next phase needs. |
| **effect** | After the queue flushes, with that returned value | **No** | The imperative work: log, talk to a widget, subscribe. May return a **cleanup**. |

Compute is “what do I depend on, and what value should leave the graph?”  
Effect is “now that I have a plain value, do the outside work.”

Reads in the **effect** function do **not** subscribe. That is intentional. If you write:

```ts
createEffect(
  () => count(),
  (value) => {
    console.log(value, name()); // name() is a snapshot, not a dependency
  }
);
```

Changing `name` will **not** re-run this effect. Changing `count` will, and then `name()` is whatever it is at flush time.

Need both? Put both reads in compute:

```ts
createEffect(
  () => [count(), name()] as const,
  ([c, n]) => console.log(c, n)
);
```

`as const` just keeps the tuple types honest. The idea is: **pack every dependency in compute, unpack a plain value in effect.**

Same batching as always: `setCount(1)` stages; the log happens after the microtask (`flush()` in tests).

---

### Cleanup — the subscription rule of the web

If you **start** something that lives on after this run (a timer, a socket, an `AbortController`, a `resize` listener), you must **stop** it later. Otherwise you leak: two sockets, requests for a user you already left, timers after the page is gone.

The effect function may return a cleanup. Solid runs it:

1. **before** the next effect run (dependencies changed — tear down the old one first)
2. when the effect is **disposed** (component gone, or its owner disposed)

```ts
createEffect(
  () => userId(),
  (id) => {
    const ctrl = new AbortController();
    fetch(`/users/${id}`, { signal: ctrl.signal });
    return () => ctrl.abort();
  }
);
```

That snippet is here to show **cleanup**, not to teach fetching. Fetching data belongs in an **async memo** (Chunk 7). If you put load-into-a-signal inside an effect, you are copying derived data — the Chunk 2 sin.

A better “real” cleanup example for this chunk:

```ts
createEffect(
  () => props.roomId,
  (roomId) => {
    const connection = chat.connect(roomId);
    connection.open();
    return () => connection.close();
  }
);
```

`roomId` goes `"lobby"` → `"office"`: cleanup closes lobby, effect opens office. Component unmounts: cleanup closes whatever is open.

---

### Ownership (just enough)

Effects attach to an **owner**. A component is an owner. Leave the page → Solid disposes that owner → cleanups run → no leftover timers.

Create an effect with **no** owner (random module-level `createEffect` outside any component) and nothing will ever dispose it. That is a leak. You will meet `createRoot` later; for app code, **put effects inside components.**

---

### The anti-pattern (do not do this)

```ts
const [count, setCount] = createSignal(0);
const [doubled, setDoubled] = createSignal(0);

createEffect(
  () => count(),
  (value) => setDoubled(value * 2) // copying derived data
);
```

This is React-shaped: “sync B when A changes.” You now have two sources of truth, an extra write, an extra flush, and a way for `doubled` to disagree with `count`.

Correct:

```ts
const doubled = () => count() * 2;
// or createMemo if several readers / a boundary
```

Same rule for “run a request when `query` changes and `setResults`.” That is an async memo, not an effect. Same rule for “user clicked Save, watch a `submitted` signal.” That belongs in `onClick` (or later, an `action`). The click **is** the cause. Do not hide the cause in a signal and then watch it.

---

### When an effect *is* correct

Use `createEffect` when a **settled** reactive value must drive something Solid does not render:

- `document.title` / some analytics ping when a page name changes
- `localStorage.setItem("theme", theme())` (write *out*; reading back on init is just `createSignal(localStorage.getItem(...) ?? "light")`)
- third-party widget: map, chart, video player
- a subscription keyed by reactive state (`roomId`, `userId`)

Your `Counter` does **not** need an effect. `{count()}` is enough.

Official checklist, shortened:

1. Can it be a function or JSX? → do that  
2. Several readers / equality? → memo  
3. Async data? → async memo (later)  
4. User clicked / typed? → event handler  
5. Must leave Solid and poke an imperative API? → **effect**

If (5) is no, you do not want `createEffect`.

Name them: `createEffect(() => count(), v => ..., { name: "logCount" })`. Same habit as memos.

---

### Tiny picture

```text
count (signal)
  └─ compute: count()          ← tracking scope
        └─ after flush → effect(value)  ← console / widget / socket
                              └─ optional cleanup()
```

JSX is also a consumer of `count`, but it stays in the graph and updates the DOM. The effect is the arrow **out**.

---

## Exercises

**1. Need an effect?** Yes or no, one sentence.

- Show `count()` on a button (your Counter).
- When `theme()` changes, write it to `localStorage`.
- `fullName` from `first()` + `last()`, shown in a `<p>`.
- When `roomId()` changes, open a chat socket and close the old one.
- On button click, `setCount(c => c + 1)`.

**2. Tracking.** After this mounts, you `setName("Ada")`. Does the effect run? Why?

```ts
const [count, setCount] = createSignal(0);
const [name, setName] = createSignal("Grace");

createEffect(
  () => count(),
  (value) => console.log(value, name())
);
```

**3. Cleanup order.** `roomId` starts `"a"`. Then `setRoomId("b")`. List the `console.log`s in order (ignore the exact flush delay; assume it flushed).

```ts
createEffect(
  () => roomId(),
  (id) => {
    console.log("open", id);
    return () => console.log("close", id);
  }
);
```

**4. Rewrite.** This is the anti-pattern. What does it become? (function or memo — say which and why)

```ts
const [price, setPrice] = createSignal(100);
const [tax, setTax] = createSignal(0);

createEffect(
  () => price(),
  (p) => setTax(p * 0.18)
);

return <span>{tax()}</span>;
```

Reply with answers. Then Chunk 4: **lists, `Show`, and stores** — still no server.

---

**User**

 Need an effect? Yes or no, one sentence.
1. No
2. Yes
3. No, use memo or derived state
4. Yes
5. No, use event listener 


Tracking. After this mounts, you setName("Ada"). Does the effect run? Why?
no, cause the tracking compute is () => count(), is count() and not name()

Cleanup order. roomId starts "a". Then setRoomId("b"). List the console.logs in order (ignore the exact flush delay; assume it flushed).
answer : ("open a") and then when id changes it will be ("close a") cause it's a cleanup function     return () => console.log("close", id);

Rewrite. This is the anti-pattern. What does it become? (function or memo — say which and why)
answer i would go with function and not memo

and also the @src/components/Counter.test.tsx  was aldready there in inti, so i dont get the flush, 
could you please elaborate more and simple on microtask queue, updates and flush, etc related to it


---

**Cursor**

I’ll grade those answers, then explain `flush` using the test that shipped with the project — microtasks first, then why the test needs that extra call.

Your answers are almost all right. One cleanup step is missing. Then `flush` — that test is the easiest way to see the queue.

---

### Your answers

| # | You | Verdict |
|---|---|---|
| 1 Show `count` on a button | No | Correct |
| 2 `theme` → `localStorage` | Yes | Correct |
| 3 `fullName` in a `<p>` | No — derived / memo | Correct |
| 4 chat socket on `roomId` | Yes | Correct |
| 5 click increments | No — event handler | Correct (`onClick` is that listener) |
| Tracking / `setName("Ada")` | No — compute only reads `count()` | Correct |
| Rewrite `tax` | function, not memo | Correct — one cheap `<span>` |

**Cleanup order — almost.** You have the first two logs. The third is the new room opening:

```text
open a          ← first run, room "a"
close a         ← cleanup of the old run (you had this)
open b          ← new run, room "b"   ← this one
```

Cleanup does not mean “stop forever.” It means “tear down the *previous* run, then run again.” `"a"` → `"b"` is close-old then open-new. If the component later unmounts, you would get `close b` as well. The exercise stopped at the change.

---

### Why that test has `flush()`

The file was in the starter because **Solid does not update the DOM in the same instant as `setCount`**. The test has to wait for that delay — or force it.

**What a click does, in order**

```text
1. You click (or fireEvent.click in the test)
2. onClick runs:  setCount((prev) => prev + 1)
      → Solid writes "1" on a sticky note (staged)
      → the live value is still 0
      → Solid says "I'll apply this in a moment"
3. onClick finishes
4. Very soon after — a microtask — Solid applies the note
      → {count()} runs again
      → button text becomes "Clicks: 1"
5. The browser paints the new text
```

In the real app you never see the gap. The click, the microtask, and the paint all happen before your eyes get a frame. **You do not call `flush()` in components.**

A test is not a browser frame. It is a script that keeps going on the same turn:

```8:16:src/components/Counter.test.tsx
  test('it increments on click', () => {
    const { getByRole } = render(() => <Counter />);
    const button = getByRole('button');
    expect(button).toHaveTextContent('Clicks: 0');
    fireEvent.click(button);
    // Solid batches DOM updates; flush() applies them synchronously.
    flush();
    expect(button).toHaveTextContent('Clicks: 1');
  });
```

Without `flush()`:

```text
fireEvent.click(button);   // step 2 only — note is staged, text still "Clicks: 0"
expect(... 'Clicks: 1');   // same turn, still before step 4 → FAIL
```

`flush()` means: **don’t wait for “in a moment.” Apply the sticky notes right now.** Then the expect sees `"Clicks: 1"`.

That is the whole reason the starter test imports `flush`.

---

### Microtask, in plain words

JavaScript runs **one thing at a time**. The thing running now is the current **turn** (your `onClick`, or the whole test function).

When that turn ends, JS first runs leftover tiny jobs called **microtasks** (`queueMicrotask`, `Promise.then`). Solid puts its “apply staged writes” job there.

Then the browser may paint.

```text
[ onClick / test code ]  →  [ Solid flush (microtask) ]  →  [ paint ]
     you are here                "in a moment"              you see it
```

`flush()` jumps the middle step forward so tests (and weird imperative glue) can look at the result **before** yielding.

---

### Why “in a moment” exists: batching

If Solid updated the DOM on every `setCount`, this click would paint three times:

```ts
setCount(1);
setCount(2);
setCount(3);
```

Instead it stacks three notes and, on the next flush, the DOM goes to `3` once. That is batching. Faster, no flicker.

**Solid 2 extra rule** (this surprises people): until flush, a normal read still sees the **old committed** value.

```ts
setCount(5);
count();   // still 0 — the note is not applied yet
flush();
count();   // 5
```

The **updater** form is the exception: it sees the latest *staged* value, so several writes in one turn compose:

```ts
setCount((c) => c + 1);
setCount((c) => c + 1);
// staged is 2, even though count() still reads 0 until flush
```

That is why your button uses `setCount((prev) => prev + 1)` and not `setCount(count() + 1)` twice in one handler — the second `count()` would still be `0`.

---

### Cheat sheet

| Place | Do you `flush()`? |
|---|---|
| Component / `onClick` / everyday app | No. The microtask runs on its own. |
| Vitest / testing-library after a click or `setCount` | Yes, before you `expect` the DOM or a memo. |
| Teaching snippets in the docs | Yes, so `console.log` shows the new value on the next line. |

`flush` is a test/debug lever, not a feature you build the UI with.

If that picture is solid, say so and we go to **Chunk 4: `Show`, lists, and stores**.

---

**User**

thanks now it's cleared, we can move on to chunk 4

---

**Cursor**

Chunk 4 is UI structure: show/hide, lists, and stores. I’ll pull the Solid 2 docs for `Show`, `For`, and `createStore` so this matches v2, not the old APIs.

Chunk 4 is how a real screen is built: **show or hide a piece of the DOM, render a list without remounting every row, and store nested data so one field can update without replacing the whole object.**

---

## The web idea: create, update, or destroy

The DOM is a tree of nodes. When data changes you can:

| Action | Example | Cost |
|---|---|---|
| **Update** a node | text `"Clicks: 0"` → `"Clicks: 1"` | cheap — node stays |
| **Create** a node | show a signed-in panel that was not there | new element + new component owner |
| **Destroy** a node | hide that panel | remove from the page, run cleanups |

`{count()}` is an **update**. A login wall vs a dashboard is **create/destroy**. A todo list that reorders is “keep the same row nodes if they are the same todo.”

React’s `array.map` + `key` is the same problem. Solid’s answer is `Show` / `Switch` / `For`, plus **stores** when the data is nested.

Do **not** use `.map()` in JSX to build lists. The whole expression re-runs, rows get new owners, inputs lose focus, effects re-subscribe. `For` reuses the row that still represents the same item.

---

## `Show` — one condition

`Show` renders children when `when` is **truthy**, otherwise `fallback`.

Truthy in JS: a real object, a non-empty string, a non-zero number, `true`.  
Falsy: `undefined`, `null`, `false`, `0`, `""`.

So `when={count()}` hides the children at `0`. For “is there a user?”, pass the user (or `undefined`). For a boolean flag, pass the boolean.

```tsx
import { Show, createSignal } from "solid-js";

type User = { name: string };

function Account() {
  const [user] = createSignal<User | undefined>({ name: "Ada" });

  return (
    <Show when={user()} fallback={<a href="/sign-in">Sign in</a>}>
      {(current) => <p>Signed in as {current().name}</p>}
    </Show>
  );
}
```

The function child is not decoration. Solid **narrows** the value: inside the callback, you have a user, not `User | undefined`. Default (no `keyed`): you get an **accessor** `current()`. The child **stays mounted** while `when` stays truthy. If `user()` goes from Ada’s object to Grace’s object, the same `<p>` stays; `current()` updates.

`keyed` remounts when the value’s **identity** changes (new object → destroy + create). Use that when the subtree should start over (reset local state). Default is what you want most of the time.

Several exclusive branches → `Switch` / `Match` (first truthy `Match` wins). Same idea as `if / else if`.

```tsx
<Switch fallback={<p>Unknown</p>}>
  <Match when={props.code === 200}><p>Ready</p></Match>
  <Match when={props.code === 404}><p>Not found</p></Match>
</Switch>
```

`{user() && <Dashboard />}` can work for a simple native tag. Prefer `Show` as soon as you have a fallback, a component with its own signals, or you need the narrowed value. That is the control-flow that owns create/destroy correctly.

---

## `For` — a list that keeps its rows

```tsx
import { For, createSignal } from "solid-js";

type Todo = { id: number; text: string };

function TodoList() {
  const [todos] = createSignal<Todo[]>([
    { id: 1, text: "Read the guide" },
    { id: 2, text: "Build an example" },
  ]);

  return (
    <ul>
      <For each={todos()} fallback={<li>No tasks</li>}>
        {(todo, index) => (
          <li>
            {index() + 1}. {todo.text}
          </li>
        )}
      </For>
    </ul>
  );
}
```

Default mode is **keyed by identity** (the item itself, `===`):

- callback: raw `todo`, `index` is an **accessor** (`index()`, because position can change when the list reorders)
- same object still in the array → that `<li>` is reused
- item removed → that row’s owner is disposed (effects clean up)
- empty array → `fallback`

When you replace the whole array with **new** objects (`todos().map(t => ({ ...t, done: true }))`), identity changes even if `id` is the same → `For` treats it as a new row. That is why a **store** (or `keyed={(item) => item.id}`) matters for real todos.

You will see `Repeat` later: it is for “row 0…n of a store,” not “diff this array.” Ignore it for now.

---

## Props — do not destructure

Before you extract a `TodoRow` component, one Solid rule that will bite you.

Parents pass props. Compiled props can be **getters**, so `name={selected().name}` stays live until the child **reads** `props.name` in a tracking scope.

```tsx
function Greeting(props: { name: string }) {
  return <p>Hello, {props.name}</p>; // good — read inside JSX
}
```

This is the Chunk 1 bug again:

```tsx
function Greeting(props: { name: string }) {
  const name = props.name; // snapshot, untracked
  return <p>Hello, {name}</p>; // frozen
}

function Broken({ name }: { name: string }) { // destructure in the parameter — same snapshot
  return <p>Hello, {name}</p>;
}
```

v2 warns about both in dev. Keep the `props` object. If you need a derived local, `createMemo(() => ...)` from `props`.

---

## Stores — nested state, per-property subscriptions

**Signal:** one value, replaced as a unit. `count`, `name`, `theme`.  
**Store:** an object or array whose **fields** should update independently.

```ts
const [state, setState] = createStore({
  profile: { name: "Ada", online: false },
  todos: [] as { id: string; text: string; done: boolean }[],
});
```

Reads use **property syntax**, not `()`. In a tracking scope:

```tsx
<p>{state.profile.name}</p>
```

That JSX subscribed to **`profile.name` only**. `setState` that flips `online` does not update that `<p>`.

Writes use a **draft** (Solid 2’s main API). Mutate it like a normal object. The store you read stays read-only; the draft is the writable copy for that update.

```ts
setState((draft) => {
  draft.profile.name = "Grace";
  draft.todos.push({ id: "1", text: "Write docs", done: false });
});
```

Or return a replacement when mutation is awkward (filter):

```ts
setTodos((current) => current.filter((todo) => !todo.done));
```

Same batching as signals: the draft write **stages**; JSX sees the new `name` after the microtask (`flush()` in tests).

---

### Why a todo list wants a store

Signal-of-array toggle usually builds a **new** object for that row. `For`’s default identity says “new item” → remount. A focused `<input>` in that row blinks. Effects in the row re-run.

Store toggle:

```ts
setTodos((draft) => {
  const todo = draft.find((t) => t.id === "1");
  if (todo) todo.done = true;
});
```

Same row proxy. Only `{todo.done}` updates. That is fine-grained reactivity applied to objects.

Rule:

- one number / string / boolean → `createSignal`
- `{ profile, todos[] }` that screens read in pieces → `createStore`
- derived *array* shared by count + list → still often a memo (Chunk 2); a **projection** is the store-shaped version of that — later

`createStore(() => ...)` (writable derived store), `createProjection`, `createOptimisticStore` exist. Not this chunk.

---

### One picture

```text
todos store
  [0].text  ──►  row 0 <li> text
  [0].done  ──►  row 0 checkbox
  [1].text  ──►  row 1 <li> text

Show when={user()}
  truthy ──► create Account subtree
  falsy  ──► destroy it, show fallback
```

---

## Mini app in your head (do not skip to `repos.tsx`)

A local todo screen, all client:

- `createStore` of `{ todos: [...], filter: "all" | "open" }`
- `Show` for the empty state vs the list
- `For` over the visible todos
- checkbox `onClick` / `onChange` writes `draft.todos[i].done = ...`
- derived `visible`: function or memo (Chunk 2 rule)

That is Chunks 1–4 glued together. Forms as a topic (controlled inputs, writable memos) is Chunk 5.

---

## Exercises

**1. `Show`.** `count` starts at `0`. What is on screen, and why?

```tsx
<Show when={count()} fallback={<p>None</p>}>
  <p>You have {count()}</p>
</Show>
```

**2. `For` vs `map`.** One sentence: why is this the wrong list tool in Solid?

```tsx
<ul>
  {todos().map((todo) => (
    <li>{todo.text}</li>
  ))}
</ul>
```

**3. Signal or store?** Pick one each, one sentence.

- the string in a search box
- a user `{ id, name, email }` where the header only reads `name` and a form only edits `email`
- `true`/`false` for “sidebar open”

**4. Draft.** After this flush, what is `state.profile.name`, and does a `<p>{state.profile.online}</p>` update?

```ts
const [state, setState] = createStore({
  profile: { name: "Ada", online: false },
});

setState((draft) => {
  draft.profile.name = "Grace";
});
```

**5. Props.** This child does not update when the parent’s `selected()` changes. Why?

```tsx
function NameTag({ name }: { name: string }) {
  return <span>{name}</span>;
}

// parent
<NameTag name={selected().name} />
```

Reply with answers. Then **Chunk 5: forms and inputs** — still no server.

---

**User**

1. we have None, cause JS zero as fasly value
2. it has to something with the rendering the textnode again, so just use For
3.  store, store, signal
4. yes, its now Grace
5. cause we have destructed the props, loosing all the reactivity

---

**Cursor**

I’ll grade these against the Chunk 4 questions, then we’ll fix the two misses (`search` as a signal, and which store field actually updates).

**1, 5, and the last two of 3 are right.** Two misses: the search box is a **signal**, and changing `name` does **not** update a paragraph that only reads `online`. Your `TodoList` already has the signal/store split correct in code.

---

### 1. `Show` when `count` is `0`

**Correct.** `0` is falsy, so you get the fallback `"None"`. The children never mount. Same trap: `when={count()}` is “is this number truthy?”, not “is there a count?”. Use `when={count() > 0}` if zero should still show the message.

---

### 2. `.map()` vs `For`

The instinct is right (`For`). The reason is not “the text node.” It is **row identity**.

`{todos().map(...)}` sits in a tracking scope. Any change to `todos` **re-runs the whole map**. Every `<li>` (and any child component, input, effect in that row) is thrown away and created again.

`For` keeps the row whose item is still the same object. You add a third todo → two existing `<li>`s stay, one new one is created. You toggle `completed` on a **store** item → the row stays, only the readers of `completed` update.

That is why a focused input in a row does not blink, and why row effects do not re-subscribe on every list edit.

---

### 3. Signal or store

| Thing | You | Correct |
|---|---|---|
| search-box string | store | **signal** — one string, replaced as a unit |
| `{ id, name, email }` (header reads `name`, form edits `email`) | store | store — different fields, different subscribers |
| sidebar open `true`/`false` | signal | signal |

You already did this in `TodoList.tsx`: `input` is a signal, `todos` is a store. That is the right split. A store for a single search string would work, but it is a heavier node for one value. Signal is the tool.

---

### 4. Draft write

`state.profile.name` is **`"Grace"`**. The `<p>{state.profile.online}</p>` does **not** update.

That `<p>` subscribed to **`profile.online` only**. You wrote `name`. Different property → that paragraph is not a subscriber. After flush you still see `false`, and you never re-ran that JSX. That is the point of a store.

---

### 5. Destructured props

**Correct.** `{ name }` is a one-time snapshot in the component body (untracked). Parent can pass a new `selected().name`; this child never subscribed. Keep `props.name` and read it in JSX.

---

If that store-field rule is clear, we go to **Chunk 5: forms and inputs** — `value` + `onInput`, what “controlled” means in Solid, and the writable-memo pattern for a draft that starts from props. Your todo input is the starting example. Say when to continue.

---

**User**

yes understood, let's move on to chunk5

---

**Cursor**

Chunk 5 is forms. I’ll pull Solid

 2’s writable-signal (draft-from-props) docs and teach from the input you already have in `TodoList`.

You already built the core of this chunk in `TodoList`. Chunk 5 names it, then adds the one Solid 2 pattern React does not have: a **writable memo** (local draft that starts from props).

---

## The web idea: who owns the box?

An `<input>` is a real DOM node. By default **it** owns what you typed. The browser updates the box on every key. Your JS can read `inputElement.value` later. That is **uncontrolled**: the DOM is the source of truth.

A **form** is a group of fields plus a submit. Enter in a text field, or a `type="submit"` button, fires `submit`. The browser’s default is “send the fields to a URL and reload.” In an SPA you call `event.preventDefault()` and handle it in JS.

| Event | When (text input) | Use in Solid |
|---|---|---|
| `input` | every keystroke | `onInput` — this is what you want |
| `change` | usually when the field **blurs** | `onChange` — not “every key” |

React’s `onChange` is a lie: it fires like `input`. **Solid uses the real DOM names.** For a live text box, `onInput`. This is the #1 React → Solid form footgun.

`event.currentTarget` is the element the handler is attached to (the `<input>`). Prefer `e.currentTarget.value` over `e.target.value` — `target` can be a child, and typing is sloppier.

Checkboxes are different: the boolean is `checked`, not `value`.

```tsx
<input
  type="checkbox"
  checked={todo.completed}
  onChange={(e) => {
    setTodos((draft) => {
      const row = draft.find((t) => t.id === todo.id);
      if (row) row.completed = e.currentTarget.checked;
    });
  }}
/>
```

`onChange` is correct here: that is when a checkbox actually toggles.

---

## Controlled input — you already have this

```16:20:src/components/TodoList.tsx
        <input
          type='text'
          value={input()}
          onInput={(e) => setInput(e.target.value)}
```

Two bindings, one loop:

```text
keystroke
  → browser would change the box
  → onInput runs, setInput(...)
  → signal stages, then flushes
  → value={input()} writes the signal back onto the input
```

The **signal** is the source of truth. The box is a mirror. That is why `setInput('')` after Add **clears the field**. You wrote the signal; JSX pushed `""` into `value`. If the input were uncontrolled (no `value={...}`), `setInput('')` would change your signal and **leave the letters on screen**.

When to control:

- you need to **read** the text in JS (`input()` on Add)
- you need to **set** it from JS (clear, reset, fill from a button)
- you derive UI from it (disable Add when empty, a character count)

A search box you only read on Submit can stay uncontrolled and use a `ref` or `FormData`. Your todo field should stay controlled. You already chose right.

Prefer `e.currentTarget.value`. Same result here; better habit.

`if (input())` uses the same falsy rule as `Show`: empty string is falsy. Good enough. `input().trim()` if you want to reject `"   "`.

---

## Put submit on a `<form>`

`onClick` on Add works. The web pattern is a form so **Enter** also adds:

```tsx
<form
  onSubmit={(e) => {
    e.preventDefault(); // no full-page submit
    const text = input().trim();
    if (!text) return;
    handleAddTodo(text);
    setInput("");
  }}
>
  <input value={input()} onInput={(e) => setInput(e.currentTarget.value)} />
  <button type="submit">Add Task</button>
</form>
```

`preventDefault` is the whole “SPA forms” trick. Without it the browser navigates. `type="submit"` is the default for a button inside a form; `type="button"` means “do not submit.” Your current Add is a lone button, not inside a `<form>`, so it does not submit anything — that is why `onClick` was fine.

---

## Two kinds of `createSignal` (Solid 2)

You know the first:

```ts
const [input, setInput] = createSignal(""); // plain signal — you own this string
```

The second takes a **function**. That is a **writable memo**: derived from something else, but you can overwrite it locally.

```tsx
function NameField(props: { value: string }) {
  const [draft, setDraft] = createSignal(() => props.value);

  return (
    <input
      value={draft()}
      onInput={(e) => setDraft(e.currentTarget.value)}
    />
  );
}
```

| | Plain `createSignal("")` | `createSignal(() => props.value)` |
|---|---|---|
| Starts as | the value you passed | whatever `props.value` is **now** (tracked) |
| User types | you `setDraft` — local | same — local **override** |
| Source changes (`props.value`) | ignored | derivation runs again, **override is replaced** |

Use the plain signal when the field’s life is **only this component** (your todo composer).  
Use the writable memo when the field **starts from a parent / server value** and the user may edit it, and a new incoming value should win (selected user changed, reset, refetch).

The React-shaped mistake:

```ts
const [draft, setDraft] = createSignal(props.value); // snapshot — Chunk 1 bug

createEffect(
  () => props.value,
  (v) => setDraft(v) // copying derived data — Chunk 3 anti-pattern
);
```

Solid 2’s answer is the function form. No effect. No second source of truth to keep in sync.

Nested object (name + email)? Same idea with a **writable derived store**:

```ts
const [draft, setDraft] = createStore(() => props.profile, {
  name: "",
  email: "",
});
```

You mutate `draft` locally; when `props.profile` changes, the projection reconciles. Validation stays a memo:

```ts
const emailError = createMemo(() =>
  draft.email.includes("@") ? undefined : "Enter a valid email address."
);
```

Do not `createEffect` to set an `error` signal. Derive it.

---

## Decision rule

1. Field lives only here, you set/clear it → **plain signal** (your `input`).
2. Several fields of one object, toggled in pieces → **store** (your `todos`, or a draft profile).
3. Starts from `props` / other reactive source, user may override, new source should replace the edit → **`createSignal(() => ...)`** or **`createStore(() => ...)`**.
4. User clicked Save / Add → **handler** (`onClick` / `onSubmit`). Not an effect watching `submitted`.
5. Need a yes/no from the draft → **function or memo**, not an effect.

Server submit, `action`, optimistic overlay: later chunks. Today the form only talks to local signals/stores.

---

## Exercises

**1. Control.** If you delete `value={input()}` and keep `onInput` + `setInput('')` on Add, what happens to the letters in the box after Add? Why?

**2. Events.** You type `"hi"` in a Solid `<input onChange={...}>` (not `onInput`). When does the handler run?

**3. Pick one.** Plain signal, writable memo (`createSignal(() => ...)`), or store?

- Todo composer text (what you have).
- “Edit user” input whose initial text is `props.user.name`, and choosing another user should replace whatever you typed.
- `completed` on each todo in your list.

**4. Anti-pattern.** In one sentence, what is wrong, and what is the Solid 2 replacement?

```ts
const [draft, setDraft] = createSignal(props.title);
createEffect(
  () => props.title,
  (title) => setDraft(title)
);
```

**5. Checkbox.** Why is `value={todo.completed}` the wrong prop on `<input type="checkbox">`?

Reply with answers. Then **Chunk 6: URLs and routing** — still no fetch/server data.

---

**User**

move on

---

**Cursor**

Chunk 6 is URLs and routing. I’ll read your app’s router and the Solid 2 router docs so we learn from this project, not a generic example.

Chunk 6 is the address bar. Your app already has a full router — we will read **that**, and leave `query` / `preload` / `fetch` on the user page for Chunk 7.

---

## The web idea: a URL is a shareable piece of UI state

A URL is not decoration. It is how the browser answers “what should be on screen?”

```text
http://localhost:3000/users/1?tab=bio#bio
│                        │        │      │
│                        │        │      └─ hash (in-page jump)
│                        │        └─ search / query string
│                        └─ pathname
└─ origin
```

Without a router, clicking a link does this:

1. Browser asks the **server** for a new HTML document.
2. The old page is thrown away (JS heap, signals, scroll).
3. A new page boots from scratch.

That is a **full navigation**. Fine for a blog. Painful for an app (you would lose the todo list on every click).

A **client router** intercepts same-origin `<a href="...">` clicks:

1. The URL still changes (you can bookmark `/users/1`, the back button works).
2. The **document stays**. Your root layout (the nav in `App`) stays mounted.
3. Only the matched page is created/destroyed — same `Show`/`For` idea, but the condition is the pathname.

Solid does not require a router. This project uses **Solid Router 2** (`@solidjs/router`).

---

## How *this* project is wired

```1:7:src/router.ts
import { createRouter } from '@solidjs/router';
import { fileRoutes } from '@solidjs/router/fs';
import { pageRoutes } from 'virtual:file-routes';

export const Router = createRouter({ routes: fileRoutes(pageRoutes) });

export const { paths } = Router;
```

Three jobs, one module:

1. Scan `src/routes/**` → a route tree (`virtual:file-routes`).
2. `createRouter(...)` → a provider component + matcher.
3. `paths` → **typed URL builder**. You never hand-write `"/users/" + id`.

`Router` and `paths` are **app-wide config**, not “where the user is right now.” The current URL is a **session**. You read that with `useLocation` / `useParams` / `props.params` **inside** `<Router>`.

---

## Files → URLs (nested convention)

| File | URL | Role |
|---|---|---|
| `src/routes/index.tsx` | `/` | Home (your `TodoList`) |
| `src/routes/repos.tsx` | `/repos` | Repos page |
| `src/routes/users.tsx` | `/users` | **Layout** around anything under `users/` |
| `src/routes/users/[id].tsx` | `/users/:id` | One user (`:id` is a param) |
| `src/routes/[...404].tsx` | anything else | Catch-all 404 |

Rules you will reuse:

- `index.tsx` → that folder’s `/`
- `[id].tsx` → one dynamic segment, `:id`
- `[[page]].tsx` → optional param
- `[...404].tsx` → splat / rest (`*404`)
- `users.tsx` **next to** a `users/` folder → layout: it stays mounted while you go from `/users/1` to `/users/2`

`[id]` in the filename is a **placeholder**. The real value lives in the URL: `/users/1` → `params.id === "1"` (always a **string**, even if you built the link with the number `1`).

---

## The root layout stays mounted

```10:34:src/app.tsx
export default function App() {
  return (
    <Router>
      {(props) => (
        <>
          <Title>Solid App</Title>
          <nav>… Home · Users · Repos …</nav>
          <Loading fallback={…}>{props.children}</Loading>
        </>
      )}
    </Router>
  );
}
```

The function child of `<Router>` is the **root layout**. It does **not** unmount when you change pages. `props.children` is whichever leaf matched.

That is why the dark nav never blinks. Same rule as Chunk 4: put shared chrome in the parent, page-only state in the leaf.

`UsersLayout` is the same idea one level down:

```5:7:src/routes/users.tsx
export default function UsersLayout(props: ParentProps) {
  return <main class='px-4 py-12'>{props.children}</main>;
}
```

`/users/1` → App nav → Users `<main>` → `User` page.  
`/users/2` → nav and `<main>` **stay**; only `User` updates (or remounts as the param changes).  
`/` → Users layout is gone; Home mounts.

**State lives where the owner lives.** `TodoList` signals/stores sit in Home. Leave `/` → Home is disposed → todos reset. That is not a bug. If you wanted todos to survive navigation, you would lift that store to `App` (or a module-level store). Same ownership rule as effects.

`<Loading>` around `props.children` is already here so **async** page data can show “Loading…”. Ignore how the data is fetched until Chunk 7. Mentally: “this hole can wait.”

---

## Navigate with real `<a>` tags and `paths`

Solid Router 2 does **not** need a `<Link>` component. Same-origin anchors under the router are intercepted.

```tsx
<a href={paths()}>Home</a>           // "/"
<a href={paths.users(1)}>Users</a>   // "/users/1"
<a href={paths.repos()}>Repos</a>    // "/repos"
```

`paths.users(1)` is a typed node; in `href` it becomes a string. Call `paths()` with no args for `/`.

The router **does not** intercept: other origins, `target="_blank"`, `rel="external"`, downloads. Those are real browser navigations (your 404 page’s docs link is one).

When navigation is **not** a link (after Save, after login):

```tsx
const navigate = useNavigate();
navigate(paths.users(2));
navigate(paths.account, { replace: true }); // replace history entry (login → don't go "back" to the form)
navigate(-1); // browser back
```

Use an `<a>` when it looks like a link. Use `navigate` when JS decided to move.

Active link styling is free on claimed anchors: `aria-current="page"` (exact), `data-active` (this or a child), `data-pending` (navigation in flight). You can style those in CSS later.

---

## Read the current URL

On a page, the match is already in **props** (your user page):

```25:26:src/routes/users/[id].tsx
export default function User(props: RouteProps<'/users/:id'>) {
  const user = createMemo(() => getUser(props.params.id));
```

`RouteProps<'/users/:id'>` types `props.params.id`. Anywhere deeper you can also:

```ts
const location = useLocation(); // pathname, search, hash, …
const params = useParams();     // { id: "1" } on this page
```

`location` and `params` are reactive. Read them in JSX / memos, not as a top-level snapshot.

`?page=2` is **search params** (`useSearchParams`). Same idea as a signal, but the URL is the store — shareable, back-button friendly. We will use that when a filter belongs in the address bar. Not required for todos sitting only in memory.

---

## 404

`[...404].tsx` is “nothing else matched.” `httpStatus(404)` in `preload` matters when the **server** renders (correct HTTP status). In the browser it is a no-op. You will care when we do SSR. For now: unknown path → this component.

---

## What you should ignore on the user page (for one more chunk)

`query`, `preload`, `getRequestEvent`, `createMemo(() => getUser(...))` — that is **route data**. Same mental model as “the page’s derived value,” but async and cached by key. Chunk 7.

Until then, treat `/users/1` as: **URL param `id` → this component runs → it will later load a user.**

---

## Picture

```text
address bar: /users/1
        │
        ▼
   Router matcher
        │
        ├─ App layout (always)     nav, Title
        │     └─ Users layout      <main>
        │           └─ User        params.id = "1"
        │
click "Next user" → href=/users/2
        │
        └─ same App + Users owners
              User sees new param (Home / TodoList still disposed)
```

---

## Exercises

**1. Map.** What file mounts if the bar says `/users/1`? Which layouts stay around it?

**2. Full reload vs router.** You click Home (`href={paths()}`). Does `App`’s `<nav>` get destroyed? Does `TodoList`’s store reset if you had added items, left to Repos, then came back?

**3. Types.** `paths.users(1)` vs `props.params.id` — number or string for each? Why can that matter?

**4. Pick the tool.** `<a href={paths...}>` or `useNavigate()`?

- A “Users” item in the nav
- After a successful “create user” handler, go to the new user’s page
- A link to `https://docs.solidjs.com`

**5. Own it.** You want “which todo filter is active” to survive a refresh and be sendable as a link. Signal in `TodoList`, or the URL? One sentence.

Reply with answers. Then **Chunk 7: async data** — `fetch`, promises, `Loading` / `Errored`, and the `query` already sitting in `users/[id].tsx`.

---

**User**

move on and on chunk 7 make sure to spend ample amount to time on this topic, cause i have no idea what it is and how it works and why

---

**Cursor**

Chunk 7 is the big one: why the network exists, what a promise is, then how Solid 2 turns that into `Loading` / `Errored` / `query`. I’ll load the v2 async docs and your user/repos pages so we go slowly and use your code.

This chunk is bigger on purpose. Everything before this was **your computer’s memory**. This is **someone else’s computer, later, maybe failing**. We will go in layers: the web, then JavaScript, then Solid, then **your** two pages.

Mutations (`POST`, save, optimistic UI) stay for Chunk 8. Today is **reads**.

---

## 1. Why this topic exists

Your todo list lives in a `createStore` in the Home component. Refresh the tab → gone. That is **client state**: this browser, this mount, this owner.

A user profile at `/users/1` is different. Ada’s name is not “whatever this tab invented.” It already exists **somewhere else** (a file, a database, GitHub). Many tabs, many users, should see the same Ada. That data is **server state** (a common name for “truth that lives outside this page”).

You cannot `createSignal` Ada into existence and call it done. You have to **ask**, wait, and handle “not yet” and “it failed.”

That ask is the network. The wait is a **promise**. Solid’s job is: keep that wait **inside the reactive graph**, so `{user().name}` is still just a read — not a pile of `isLoading` / `setUser` / `useEffect`.

---

## 2. The web: a request is a letter

The browser and a server talk **HTTP**. One round trip looks like this:

```text
You (browser)                         Them (server)
     |                                      |
     |  GET /users.json                     |
     | -----------------------------------> |
     |                                      |  find the file, pack JSON
     |  200 OK                              |
     |  { "1": { "name": "Ada Lovelace" } } |
     | <----------------------------------- |
```

**GET** means “give me this, do not change anything.” Bookmark-safe, cacheable. (Changing data is usually **POST** / **PATCH** / **DELETE** — Chunk 8.)

The **status code** is the first thing you read:

| Code | Meaning | What your code should do |
|---|---|---|
| `200` | OK | parse the body |
| `404` | not found | empty state or “unknown user” |
| `500` | server broke | error UI |
| `4xx` / `5xx` | something wrong | **do not** treat the body as success data |

`fetch` in the browser **does not throw** on 404 or 500. It throws on “could not talk” (offline, blocked). That is why your repos page does this:

```ts
if (!result.ok) throw new Error("Failed to fetch data");
```

`.ok` is “status 200–299.” Without that check, you might try to read `.full_name` on an error HTML page.

The body in this project is **JSON**: text that looks like JS objects. `response.json()` parses that text into a real object. That parse is also async (the bytes may still be arriving).

Your user data is a static file the Vite app serves:

```1:5:public/users.json
{
  "1": { "name": "Ada Lovelace", "title": "First programmer" },
  "2": { "name": "Grace Hopper", "title": "Compiler pioneer" },
  "3": { "name": "Margaret Hamilton", "title": "Software engineering" }
}
```

`/users/1` does **not** mean the server has a `/users/1` JSON file. It means the **router** shows the User page, and **that page** fetches `/users.json` and picks key `"1"`. URL for UI, fetch for data — two different addresses. That split will keep confusing you until it clicks.

---

## 3. JavaScript: `fetch` returns a promise, not the user

This is illegal in the way beginners expect:

```ts
const user = fetch("/users.json"); // user is NOT the JSON
user.name; // undefined — user is a Promise
```

A **promise** is an IOU: “I do not have the value yet. I will have it, or I will fail.”

```ts
fetch("/users.json")           // Promise<Response>  — HTTP headers/status
  .then((res) => res.json())   // Promise<object>    — parsed body
  .then((data) => data["1"]);  // now you have Ada
```

`async` / `await` is the same IOU, written like steps:

```ts
async function loadAda() {
  const res = await fetch("/users.json"); // pause this function until the Response exists
  const data = await res.json();          // pause again until the body is parsed
  return data["1"];
}
```

`await` **pauses only this function**. The rest of the page keeps running. That is why you cannot do this in a component body and expect JSX to wait:

```ts
export default function User() {
  const user = await fetch(...); // not how components work
  return <h2>{user.name}</h2>;
}
```

The component function runs **once**, synchronously (Chunk 1). The network is not done yet. Something else must **subscribe** to “when the IOU pays out, update this text.”

In React, people stuff that into `useEffect` + `useState`. In Solid, the memo **is allowed to return a promise**. The graph already knows how to wait.

---

## 4. The React-shaped mistake (so you never build it)

```ts
const [user, setUser] = createSignal(undefined);
const [loading, setLoading] = createSignal(true);
const [error, setError] = createSignal(undefined);

createEffect(
  () => props.params.id,
  (id) => {
    setLoading(true);
    fetch(...).then(setUser).catch(setError);
  }
);
```

You now have **three** signals that can disagree, a race (user 1’s response arriving after you clicked user 2), and an effect that copies derived data (Chunk 3). Solid’s answer: **one derived value**. If it is not ready, that is a status of the graph, not a fourth piece of state you invented.

---

## 5. Solid: an async memo is still a memo

```ts
const user = createMemo(async () => {
  const id = id();                 // reactive read — BEFORE await
  const res = await fetch(`/api/users/${id}`);
  if (!res.ok) throw new Error("...");
  return res.json();
});
```

Same rules as Chunk 2:

- The compute function is a tracking scope.
- `id()` subscribes the memo.
- `user()` is an accessor. Callers read **the settled value**, not the `Promise`. You write `user().name`, never `user().then(...)`.

What is new:

Until the first promise settles, **there is no value**. Reading `user()` in JSX is “not ready.” That is not `undefined`. It is a special graph status. A **`Loading`** boundary turns that status into fallback UI.

If the promise **rejects** (throw, or `fetch` network failure), that status is an error. **`Errored`** turns it into fallback UI.

`Loading` does not eat errors. `Errored` does not replace loading. They are two different doors. Nest them:

```tsx
<Errored fallback={<p>Broken</p>}>
  <Loading fallback={<p>Loading…</p>}>
    <h2>{user().name}</h2>
  </Loading>
</Errored>
```

Nearest matching boundary wins, same idea as “nearest `Show`.”

---

## 6. Two times on the timeline (this is the part people skip)

**Time A — no answer yet** (first visit to Repos, first visit to `/users/1`):

```text
user() is not ready  →  Loading shows fallback  →  promise settles  →  you see Ada
```

**Time B — you already have Ada, you ask for Grace** (`id` 1 → 2):

Solid **keeps Ada on screen** while the next request is in flight. Then it swaps to Grace when that request settles. You do **not** go back to the big “Loading…” hole by default. That is called a **held update**: old committed view stays; new work prepares the next commit.

Why: flashing a skeleton on every “Next user” feels broken. You still want a small hint that something is updating:

```tsx
<article aria-busy={isPending(user) ? "true" : "false"}>
  <h2>{user().name}</h2>
</article>
```

`isPending(user)` is **true** only when a **replacement** is in flight and there is already a settled value.

| Situation | What you use |
|---|---|
| Never had data | `<Loading fallback={...}>` |
| Had data, asking again | keep showing old data + `isPending` |
| Request failed | `<Errored fallback={...}>` |

`Loading`’s optional `on={id()}` means: “if **this** value changed, I am allowed to show the fallback again.” Useful when switching users should skeleton, but a theme toggle should not. Your `App` loading hole does not use `on`.

---

## 7. Walk `repos.tsx` (the teaching page)

```4:21:src/routes/repos.tsx
export default function Repos() {
  const repository = createMemo(async () => {
    const result = await fetch('https://api.github.com/repos/TanStack/query');
    if (!result.ok) throw new Error('Failed to fetch data');
    return result.json();
  });

  return (
    <main>
      <div>Static Content</div>
      <Errored fallback={<div>Something went wrong!</div>}>
        <Loading fallback={<div>Something Loading...</div>}>
          <div>{repository().full_name}</div>
        </Loading>
      </Errored>
    </main>
  );
}
```

Line by line:

1. Component runs **once**. It **creates** the memo. The async function starts. `fetch` goes to GitHub.
2. JSX builds `<main>`, then **Static Content** — that is **outside** both boundaries. It appears immediately. The nav in `App` is also outside. That is the “keep chrome visible” rule.
3. The inner `<div>{repository().full_name}</div>` **reads** the memo. First time: not ready. Nearest `Loading` shows `"Something Loading..."`.
4. GitHub answers. Memo commits. `Loading` shows the children. You see `TanStack/query` (that is `full_name`).
5. If GitHub is down or `!result.ok`, the memo errors. `Loading` does nothing with that. `Errored` shows `"Something went wrong!"`.

There is no `query` here. Every visit to `/repos` creates a new memo and fetches again. Fine for a demo. Bad if Home and Repos both wanted the same repo, or if you hover the Repos link and then click — you would fetch twice.

---

## 8. Why `query` exists (the user page)

`query` is **not** Solid’s reactivity. It is the **router’s cache** around a function.

```ts
const getUser = query(async (id: string) => { ... }, "user");
```

- Second argument `"user"` is the **name** of this cache.
- Arguments are serialized into the key: `getUser("1")` → something like `user["1"]`, `getUser("2")` → `user["2"]`.
- Same key, already in flight or fresh → **reuse**, do not hit the network again.

That solves:

| Problem | Without `query` | With `query` |
|---|---|---|
| Hover Preload + click | two fetches | one fetch, render reads the same entry |
| `/users/1` → `/users/2` → back to `1` | fetch 1 again | can reuse the cached 1 (while it is still live) |
| Two components ask for user 1 | two fetches | one |

Server vs browser (you do not need to memorize timings yet):

- **Browser:** app-wide cache. Preload result reused for a few seconds so preload and the page share work. Unused entries get swept after a few minutes.
- **Server (SSR):** cache is **per request**, so user A never sees user B’s memoized fetch.

`revalidate(getUser.key)` / `revalidate(getUser.keyFor("1"))` means “this cache is stale, live readers should ask again.” That is how a **save** later refreshes the profile. Chunk 8.

---

## 9. Walk `users/[id].tsx` (the real pattern)

**The loader** (module scope — not inside the component, so every visit shares the same cached function):

```11:18:src/routes/users/[id].tsx
const getUser = query(async (id: string) => {
  const origin = getRequestEvent()?.request.url ?? location.origin;
  const response = await fetch(new URL('/users.json', origin));
  const users: Record<string, { name: string; title: string }> = await response.json();
  return users[id] ?? { name: 'Unknown', title: 'No such user' };
}, 'user');
```

`new URL('/users.json', origin)` builds an **absolute** URL (`http://localhost:…/users.json`).

Why the `origin` dance: on the **server** there is no `window.location`. `getRequestEvent()` is “the HTTP request currently being rendered.” In the **browser** that is `undefined`, so you use `location.origin`. Same function, two runtimes. We will do SSR properly later; for now: **browser-only `location` does not exist on the server.**

Missing id → you still return an object, not throw. So `/users/99` is a **successful** query with “Unknown,” not an `Errored` boundary. That is a product choice (empty/unknown vs hard error).

**Preload** — start the fetch **before** the page component exists:

```21:23:src/routes/users/[id].tsx
export const route = {
  preload: ({ params }) => void getUser(params.id!),
} satisfies RouteDefinition;
```

Hover/focus on “Users” or “Next user” can start `getUser("1")` early. Click → component mounts → memo reads the same cache → often **already done**, so you never see “Loading…”.

`void` means “I am calling this for the side effect (fill the cache), I am not using the return as `props.data`.” This file reads via a memo instead. Both styles exist; this project chose memo + `query`.

**The page:**

```25:37:src/routes/users/[id].tsx
export default function User(props: RouteProps<'/users/:id'>) {
  const user = createMemo(() => getUser(props.params.id));

  return (
    <section>
      <h2>{user().name}</h2>
      <p>{user().title}</p>
      <a href={paths.users(Number(props.params.id) + 1)}>Next user</a>
    </section>
  );
}
```

`createMemo(() => getUser(props.params.id))` — `getUser` returns a promise, so this memo is async. `props.params.id` is the reactive input. Change `/users/1` → `/users/2` → memo asks `getUser("2")`.

There is **no** `<Loading>` on this page. Look at `App`:

```33:33:src/app.tsx
          <Loading fallback={<main class='px-4 py-12'>Loading…</main>}>{props.children}</Loading>
```

The **nearest** loading boundary is the whole page slot. First visit with a cold cache → the **entire** `props.children` (the user section) is replaced by `"Loading…"`. The **nav stays** — it is outside that `Loading`. That is why they put the boundary in `App`, not around the `<nav>`.

Repos is more precise: it wraps only `{repository().full_name}`, so “Static Content” never unmounts. Two valid designs. Smaller boundary = less of the page blinks.

Click Next: you already have a settled user, so App’s `Loading` **keeps the old user** until Grace lands (held update), unless they set `on={...}`. You may see Ada briefly, then Grace. That is Time B.

---

## 10. The `await` trap (senior, and easy to hit)

Dependency tracking is **synchronous**. Only reads **before** the first `await` subscribe the memo.

```ts
// WRONG — permissions() is after await; changing permissions will not rerun this memo
const profile = createMemo(async () => {
  const userId = id();
  const res = await fetch(`/api/users/${userId}`);
  const user = await res.json();
  return { user, permission: permissions() };
});

// RIGHT — every input first, then the gap
const profile = createMemo(async () => {
  const userId = id();
  const permission = permissions();
  const res = await fetch(`/api/users/${userId}`);
  const user = await res.json();
  return { user, permission };
});
```

Rule: **collect all signals/params/store fields you depend on, then `await`.** Dev mode will yell if you first-read something after `await`.

---

## 11. Picture (pin this in `markdown.md`)

```text
URL  /users/1
  →  params.id = "1"
  →  preload / memo calls getUser("1")
  →  query cache key user["1"]
        miss → fetch /users.json → pick "1" → commit Ada
        hit  → reuse Ada
  →  user() in JSX
        no value yet     → nearest Loading fallback
        has Ada          → show Ada
        asking for Grace → still Ada + isPending
        rejected         → nearest Errored fallback
```

**Client state:** `input()`, `todos` — you own it, no IOU.  
**Server state (read):** `getUser(id)` — they own it, you cache a copy, it can be stale.

---

## 12. What we are *not* doing yet

- **Writing** (save todo on a server, `action`, optimistic overlay, `revalidate`) — Chunk 8.
- `Reveal`, `latest`, `loadingValue` — polish. You do not need them to understand the model.
- Server functions (`"use server"`) — the fetch runs on the **server** and the browser calls it like a function. Same async-memo + `query` story, different transport. Later.

---

## Exercises (do these; this chunk is the foundation)

**1. Client vs server state.** Is the text in your todo `<input>` server state? Is `Ada Lovelace` on `/users/1`? One sentence each.

**2. `fetch`.** GitHub returns status `500` with a JSON body. Does `repos.tsx` show `full_name`, the Loading fallback, or the Errored fallback? Why?

**3. Boundaries.** Why does “Static Content” stay visible while `repository()` is loading, but the User page’s `<h2>` does **not** stay visible on a **cold** first load? Point at the JSX.

**4. Timeline.** You are on `/users/1` looking at Ada (settled). You click Next. For a moment the next `getUser` is in flight. What is on screen by default — App’s `"Loading…"`, or Ada? What API would you use to dim the article during that moment?

**5. `query`.** You hover “Users” (preload starts `getUser("1")`), then click. Why is that better than only fetching inside `User()` with a plain `createMemo(async () => fetch(...))` like Repos?

**6. `await`.** This memo will not update when `tab()` changes. Why?

```ts
const data = createMemo(async () => {
  const id = props.params.id;
  const res = await fetch("/users.json");
  const users = await res.json();
  return { user: users[id], tab: tab() };
});
```

**7. Product choice.** `/users/99` shows “Unknown” / “No such user,” not the Errored fallback. Where in the code was that decided, and what would you change if you wanted a hard error instead?

Reply with answers. Wrong is useful. Then we do **Chunk 8: mutations** — the write side of the same story.

