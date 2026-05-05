# AoC.2025.Chatter — Copilot Instructions

Solutions to **Advent of Code 2025**, written in **Chatter** — an experimental,
HyperTalk-inspired language being developed in parallel at `~/dev/chatter`.

This repo is a *consumer* of the Chatter language. It is one of the primary
real-world workloads driving Chatter's feature set.

## Running solutions

Use the repo's `./chatter` helper script — it wraps the interpreter from the
sibling `~/dev/chatter` checkout:

```bash
./chatter day01/solution.chatter
./chatter day01/solution.chatter < day01/input.txt
```

By default it runs via `ts-node` against Chatter's live `src/` (since
Chatter's committed `dist/` is almost always stale while the language is
evolving). Once you've run `npm run build` in `~/dev/chatter`, you can
opt into the faster compiled path:

```bash
CHATTER_MODE=dist ./chatter day01/solution.chatter
```

Override the Chatter repo location with `CHATTER_HOME=/path/to/chatter`
if you don't keep it at `~/dev/chatter`.

There is no build step for *this* repo — `.chatter` files are the source
of truth and are executed directly.

## Repository layout

Organize solutions per day. Scaffold with the `./new-day` helper:

```bash
./new-day 1        # creates day01/ with stubs
```

Each day directory contains:

```
dayNN/
  solution.chatter   # the program
  example.txt        # sample input from the problem statement
  input.txt          # your puzzle input (git-ignored, personal)
```

`./new-day` also appends `dayNN/input.txt` to `.gitignore` so puzzle inputs
never get committed (per Eric Wastl's request for the AoC community).

Keep one `.chatter` file per day unless a day genuinely needs shared helpers.
Chatter has no module system yet, so cross-file imports are not an option.

## Working with the Chatter language

Chatter is **changing constantly**. Before writing or editing a `.chatter`
file, verify current syntax against the language source:

- `~/dev/chatter/README.md` — design philosophy and current surface syntax
- `~/dev/chatter/examples/` — canonical, working examples
- `~/dev/chatter/tests/chatter/*.chatter` — golden tests covering every
  supported construct; these are the most reliable spec
- `~/dev/chatter/src/parser.ts` and `lexer.ts` — ground truth when docs
  disagree with reality

If a Chatter feature you need does not exist yet, **do not invent syntax**.
Either:
1. Solve the problem with what's available, or
2. Stop and discuss adding the feature to Chatter itself in `~/dev/chatter`.
   Per Chatter's design rule #8, features are only added after explicit
   conversation and spec.

### Key Chatter conventions to respect when writing solutions

- **Verbs first, named arguments verbatim**: `raise foo to bar`, not
  `raise(foo, bar)`. Argument labels in a call must match parameter names.
- **`set` is immutable**. No reassignment. If a value must change, model it
  as data flowing through expressions / `it`, not as mutation.
- **`it`** holds the result of the previous statement and is function-scoped.
  `say` does **not** update `it` — safe for debug prints.
- **Strict types at runtime**: no truthiness, no implicit number↔string
  coercion, `is` (not `==`) for equality.
- **No mixing `and` / `or`** at the same level without parens — it's a
  compile error.
- **Block closers spell themselves out**: `end if`, `end function`.
- **`number` is a 32-bit integer** today. No floats, no bignum. Plan algorithms
  accordingly; AoC inputs that overflow Int32 will need a Chatter-side fix
  before they can be solved here.
- Comments start with `#`.

## Testing solutions

There is no test harness in this repo. Verify a solution by running it
against `example.txt` and checking the output matches the expected value
from the problem statement, then run against `input.txt` for the real answer.

If a day's solution starts feeling like it needs a test harness, that is a
signal Chatter itself may need a richer test story — raise it upstream
rather than building one here.

## When Chatter blocks progress

Friction encountered while solving puzzles is **valuable signal** for the
language's design. When something feels awkward, missing, or buggy:

1. Note the specific puzzle and the construct you wished existed.
2. Surface it as a discussion item for `~/dev/chatter` rather than working
   around it silently.

The goal of this repo is dual: solve the puzzles, *and* stress-test Chatter.
