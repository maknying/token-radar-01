# Token Radar — a launch-potential radar for pre-token protocols

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/maknying/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/maknying/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Entry for make.fun bounty **token-radar-01** ("Build the best token launch radar").
Free entry, no entry fee. Deadline 14 Dec 2026 17:00 WIB.

## What it is

A web dashboard that ranks the 197 pre-token protocols in the sealed DefiLlama
snapshot (2026-11-14, 758 protocols) by what a token launch would be worth —
the **radar-2** model — and gives each one a concrete 7-step farm plan with
costs and times.

- `main.ts` — entrypoint (Node 22+, stdlib only). Reads `/input/snapshot.json`
  (or `--input <snapshot.json|dir>`), scores the board with `lib/model.ts`,
  writes `board.json` + `data.js`, copies `dashboard/` and `screenshots/`,
  and writes `dossier.json` with the vendored make.fun library.
- `lib/model.ts` — the radar-2 model. Implied value = TVL × category multiple
  × growth × maturity × breadth × plausibility. The category multiple is the
  median market-cap/TVL of the snapshot's own launched protocols in that
  category (≥3 peers, else the global median 0.2138×), clamped to [0.05, 2.00].
  Plausibility is a **gate, not a dial**: wrapped/staked/bridged products, CEXs
  and versioned pools are set aside with their reason and never ranked as
  launches (145 pre-token protocols set aside; 24 live-token rows set aside).
  No protocol name is special-cased anywhere.
- `dashboard/` — the page: dark theme, sortable/filterable table, watchlist
  filter, per-protocol detail drawer with the full model breakdown and farm
  plan, live-token and set-aside tabs, model panel. Hash-driven state
  (`#p=<slug>`, `#category=`, `#chain=`, `#tab=`, `#wl=1`, `#q=`) so every
  view is one deterministic URL. No network, system fonts only.
- `screenshots/` — the three hand-in screenshots (1440×900 JPEG):
  `1-board.jpg` (default board), `2-detail.jpg` (`#p=steakhouse-financial`,
  rank 1), `3-filtered.jpg` (`#category=Dexs`, 21 rows).
- `vendor/makefun-board.ts` — the kit's dossier builder, copied byte-identical.

## Results (verified locally)

- Board: **197 rows**, ranks 1..197, implied values strictly decreasing, all
  **40 watchlist slugs covered**. Top: Steakhouse Financial $464.9M,
  Sentora Curator $437.7M, Gauntlet $276.9M. Total implied value $3.47B.
- Farm plans: 7 steps per row, 103–219 chars each, generated from category +
  chains + TVL + age (never a hand-written name list).
- `scripts/validate.mjs` mirrors the sealed scorer's 7 gate rows
  (dashboard_files, board_file, sorted_by_value, coverage, farm_steps,
  screenshots, dossier_matches): **all 7 PASS**, plus an extra check that
  `board.json` matches a fresh `buildBoard` run row-for-row.
- Determinism: two runs into separate dirs are **byte-identical**
  (`diff -r` clean).

## Run it

```bash
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input token-radar-01-kit/input/snapshot.json
node scripts/validate.mjs out
```

Then open `out/dashboard/index.html` in a browser (it loads `../data.js`).

## Re-take the screenshots

The Meta Chromium build on this VM refuses to start without a WebGL domain
policy, and its one-shot `--screenshot` / `--dump-dom` flags produce nothing.
The working route is CDP (see `scripts/shoot-cdp.sh`):

```bash
bash scripts/shoot-cdp.sh   # needs out/ built first; writes screenshots/*.jpg
```

It launches `/opt/meta-chromium/chrome` headless with the empty v1 allowlist
in `scripts/webgl-policy.txt` (the page uses no WebGL and loads only local
files), drives it over its DevTools protocol with `scripts/cdp-shoot.mjs`
(Node's built-in WebSocket client), and converts the captures to JPEG
(quality 82, ~150 KB each). Hash-only navigations are same-document, so the
script goes through `about:blank` first to force a full load per shot.

## License

MIT (see `LICENSE`, copied from the kit starter).

---

<sub>Entered on [make.fun](https://make.fun/?ref=e46qnd9x&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
