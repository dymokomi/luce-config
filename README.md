# luce-config

A small, reusable configuration reader for Luce applications. It parses a scalar
subset of TOML — tables, bare keys, quoted UTF-8 strings, decimal numbers and
booleans — and validates a file against the settings an application declares, so
a typo is a clear error rather than a silently ignored line.

It has no dependency on any UI: any Luce program that keeps user settings, a
keymap, or a theme in a `.toml` file can read it the same way.

## Model

`Table(source)` parses the text once. Read values with fallbacks:

```luce
from toml import Table

let table = Table(text)
table.check_keys(["editor.font", "editor.size", "editor.wrap"])
let font = table.string("editor.font", "")
let size = table.number("editor.size", 14.0, 8.0, 40.0)
let wrap = table.boolean("editor.wrap", false)
```

A section can be left open-ended — a keymap binds arbitrary command names — while
every other table is still checked against the schema:

```luce
table.check_within(["editor.font"], ["shortcuts"])
for name in table.keys("shortcuts"):
    let chord = table.string("shortcuts." + name, "")
```

## Reference

| Method | Purpose |
| --- | --- |
| `Table(source)` | Parse configuration text, reporting malformed structure. |
| `check_keys(supported)` | Reject unknown tables and settings against a fixed schema. |
| `check_within(supported, dynamic)` | As above, but the `dynamic` sections accept any key. |
| `keys(section)` | The bare keys present under a section. |
| `string / number / boolean(key, fallback, …)` | Typed reads with a fallback for an absent key. |

## Build and test

Keep `luce-config`, `luce`, and `luce-base` as sibling checkouts. The tested
compiler revisions are recorded in `bootstrap/`. Build the compilers, then run
the module's test blocks in every compiled mode:

```sh
(cd ../luce-base && ./build.sh)
(cd ../luce && LUCE_BASE_COMPILER=../luce-base/build/luce-base ./build.sh)
./test.sh
```

Licensed under either of Apache-2.0 or MIT at your option.
