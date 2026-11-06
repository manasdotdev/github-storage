
in db college, create collection student and insert min 20 documents with fields {roll no, name, class, city, marks}, make use insertOne and insertMany methods. 

## `bool`

- has three possible values: true, false, and null
- can be written in many ways like 'yes', '1', 'true', 'no', '0', 'false', 'null'

## `character`

- dont overthink always use varchar or text when using postgres, for other databases google before use
- and for restricted characters use varchar(n) or text.
- the performance difference is nearly negligible unless you are dealing with very large scale.

## `numeric/decimal`

- numbers are stored as base-10 digits rather than binary approximations. eg: NUMERIC(5 precision, 2 scale) means 5 total digits with 2 digits after the decimal point (e.g., 123.45). if the number exceeds the precision, it will be rounded.
- if the () are not specified, the default precision is 18 and the scale is 0 which is bigger than bigint.
- storage cost: variable size; physically larger and uses more disk space.
- performance: slower CPU performance. Math on NUMERIC types is computed in software by the database engine rather than directly inside the CPU's floating-point hardware unit (FPU).
- exactness: 100% Exact. No rounding errors, no representation loss. $0.10 + $0.20 will always equal strictly $0.30.

## `double precision/real`

- numbers are stored in binary format and are approximations of the actual value, fixed 8 bytes per value.
- certain decimal places are lost due to binary representation.
- use `numeric` or `decimal` for exact decimal arithmetic.
- blazing fast: supported natively by CPU hardware instructions.
- real is a synonym for double precision.
- however it's less precise than double precision and fixed 4 bytes per value.

## `integer`

- most accurate for integer values. only for whole numbers
- has three possible values: `smallint` aka int2, `integer` aka int4, `bigint` aka int8. choose one as per your needs
- blazing fast: supported natively by CPU hardware instructions.
- fixed sized, unlike numeric which grows dynamically

## `serial`

- auto-incrementing integer type.
- has three possible values: `smallserial` aka int2, `serial` aka int4, `bigserial` aka int8. choose one as per your needs

## `date and timestamp`

- size is always 4 bytes and the format is `YYYY-MM-DD`.
- for more information, see https://neon.com/postgresql/tutorial/date#postgresql-date-functions
- https://neon.com/postgresql/tutorial/timestamp

## `uuid`

- CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
- `uuid` is a 16-byte binary type that stores a universally unique identifier.
- there are two main functions you should consider: gen_random_uuid() and uuidv7()
- use `gen_random_uuid()` for random UUID generation and `uuidv7()` for time-based UUID generation.
- uuidv7() is more recommended

## `json`

- always use `jsonb` over `json`, unless you have specialized requirements such as retaining the ordering of keys in the JSON documents.
- you can stored objects and arrays as well
- -> and ->>

## `hstore`

- CREATE EXTENSION hstore;
- stores key-value pairs in a single column, similar to structs and objects
- => for storing key-value pairs and -> for retrieving values by key
- both the key and value must be marked as double quotes even numbes and boolean values
- for updating, deleting and much more see, https://neon.com/postgresql/tutorial/hstore

## `array`

- stores a collection of values of the same data type
- for inserting values the `ARRAY` constructor is used or '{"", ""}'
- can create multi-dimensional arrays as well, data_type[][]
