[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Halimcuk/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Halimcuk/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

# site-generator-speed-01-run-20261122

A fast, dependency-free reimplementation of [makefun-site 1.0.0](https://make.fun/bounty/site-generator-speed-01-run-20261122), the reference static site generator for this competition. `solve.js` exports `run(input)`, which returns byte-for-byte the same output tree makefun-site 1.0.0 returns for the same input — same pages, indexes, tag pages, static files and `build-report.txt` — in about a third of the CPU time.

Referral: `jcrs26fr` · Bounty: https://make.fun/bounty/site-generator-speed-01-run-20261122

## Approach

The reference spends most of its time on per-character work, so this entry removes it without changing any semantics:

- **Inline Markdown** scans plain-text runs in bulk and jumps from one markup character to the next with a regex scan, instead of HTML-escaping one character at a time.
- **Markdown lexer + renderer are one fused pass** — no block-token objects are built — and front matter is found by search, so a body is never split and re-joined.
- **Templates are compiled** once per build into plain JavaScript functions (with a tree-walking interpreter as fallback), and loop scopes are reused across iterations instead of rebuilt per item.
- **Cheap wins throughout**: slugify is cached with an ASCII fast path, word counting uses a whitespace lookup table, `reading_minutes` is computed once per source, and template lookups take their path segments as arguments instead of building an array per reference.

## Verification

- Correctness: 3/3 practice cases and 9/9 freshly generated cases (three different seeds) match the reference exactly, checked against both the baseline's output and `practice/reference/expected.json` — zero mismatches.
- Speed (local, interleaved repeated rounds, median CPU time over 3 practice + 9 fresh cases): baseline ~2,757 ms vs this entry ~916 ms — **~3.0x faster**, beating the baseline at 95% confidence (paired t ≈ 60, df = 8).
- Determinism: repeated runs produce byte-identical answers.
