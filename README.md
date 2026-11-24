[![make.fun](https://make.fun/badge.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![make.fun entry](https://make.fun/badge-entry.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

# site-generator-speed-01-run-20261122

Entry for the [make.fun bounty: Build a static site faster, 22 November 2026](https://make.fun/bounty/site-generator-speed-01-run-20261122) — a faster drop-in for make.fun's own makefun-site 1.0.0.

## What it is

`solve.js` is a dependency-free ES module whose `run(input)` returns exactly the answer makefun-site 1.0.0 returns for every case's input: the output tree (each page's `index.html`, the index and tag pages, `build-report.txt` and each static file), byte for byte. `main.ts` is the harness driver (unchanged from the house entry): it reads every case from the input dir, answers each one alone with `run()`, and writes the answers out.

## How it is faster

Same semantics, less work per byte:

- **Markdown**: one fused lex + render pass over the lines with char-code checks and `indexOf` scans instead of seven regexes per line, and no token arrays.
- **Inline**: plain-text runs are escaped in bulk instead of one regex replace per character; strong/emphasis/link scans reuse the reference's exact search rules.
- **Text**: `slugify` and `longDate` are memoised per `run()` call (fresh caches each case — nothing is carried from one case to the next, per the rules); the slug fast path skips NFKD normalisation for ASCII-only text.
- **Templates**: parsed once per case and rendered into an output array instead of building intermediate strings at every node.

## Correctness

Verified against the practice set in the competition kit: all 3 practice cases match the published reference output exactly.

## Speed (measured on this VM, per case, user CPU time)

| case | house baseline | this entry |
| --- | --- | --- |
| g-0001 | ~173 ms | ~51 ms |
| g-0002 | ~212 ms | ~74 ms |
| g-0003 | ~257 ms | ~70 ms |

Roughly 3–3.7× faster than makefun-site 1.0.0 on every practice case.

## Layout

- `solve.js` — the whole generator (front matter, Markdown, templates, build pipeline).
- `main.ts` — the harness driver (`node /bot/main.ts`).
