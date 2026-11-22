# site-generator-speed — make.fun bounty entry

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/emiliotecnologias/site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/emiliotecnologias/site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

Entry for the make.fun bounty **"Build a static site faster"**
(`site-generator-speed-01-run-20261122`): a faster drop-in for make.fun's own
makefun-site 1.0.0 that answers exactly as it does on every case.
$100 USDC pool, 10 places × $10, deadline **Tue 24 Nov 2026, 20:00 UTC**.
A reward needs a CPU time faster than makefun-site 1.0.0 (the house baseline)
in the final heat at 95% confidence.

## Files

| File | Purpose |
|---|---|
| `solve.js` | **The generator.** Dependency-free ES module; `run(input)` returns the output tree `{path: text}`. |
| `main.ts` | **The driver the harness runs.** Reads `/input/cases.json`, answers each case with `run`, writes `/output/answers.json`. |

## How to run

- Entry (as the harness does): `node main.ts` — reads `/input/cases.json`,
  writes `/output/answers.json`. Outside a container, set
  `MAKEFUN_INPUT_DIR` / `MAKEFUN_OUTPUT_DIR` to stand in for `/input` and
  `/output`. Node 22+.

## Contract

- Input: `{ site: {title, base_url, per_page, recent}, templates: {name: source}, files: [{path, content}] }`.
- Output: a JSON object of every path the build writes (each page's
  `index.html`, the index and tag pages, `build-report.txt` and each static
  file) and that file's text — JSON-identical to makefun-site 1.0.0's.
- Pure function: no caching between calls, no I/O, no dependencies.

## Approach (solve.js)

Written from the kit's spec (front matter, template dialect, Markdown subset,
slug rule and report — makefun-site's own, not Hugo's/Zola's/Eleventy's):

- **Slugify / long dates** cached per build; ASCII fast path for slugify
  (NFKD only when a non-ASCII character appears).
- **Front matter** parsed by line offsets with manual key/value scanners;
  the body is one slice of the source, never split and re-joined.
- **Markdown** lexing and rendering fused into a single pass with manual
  line classifiers (no per-line regex battery); inline rendering escapes
  plain runs in bulk instead of character by character.
- **Templates**: includes expanded at parse time (depth-capped as the spec
  says), then each template compiled to a JS function with loop bindings
  resolved at compile time; an interpreter fallback is kept if compilation
  is unavailable.
- **Cards, neighbour links and link resolutions** computed once per
  source/build and reused across every list that shows them.
- `main.ts` turns V8's incremental marking off before importing solve.js,
  the same tuning the house baseline runs with.

## License

MIT — see `LICENSE`.
