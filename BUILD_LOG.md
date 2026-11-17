# Build log — token-radar-01

## 2026-11-17 ~10:45 WIB — setup
- Repo created: https://github.com/feltonmosz/makefun-token-radar-01 (public, master).
- Kit read in full (competition.yaml gate + judge instructions, input/README.md,
  starter main.ts / lib/model.ts / dashboard, vendor/makefun-board.ts).
- Snapshot analysed (758 protocols, 40 watchlist, as_of 2026-11-14):
  global median mcap/TVL 0.214 (n=128 live tokens); per-category medians
  usable at n>=3 (Dexs 0.342, Lending 0.132, RWA 0.275, Basis Trading 0.654,
  Canonical Bridge 0.746, Bridge 0.557, Yield 0.191, Liquid Staking 0.041,
  CDP 0.169, Cross Chain Bridge 1.220, Privacy 0.969, Yield Aggregator 0.151,
  Indexes 0.148, CEX 0.075); single-sample categories are wild (up to 400),
  so the model requires n>=3 and clamps the multiple to [0.05, 1.0].
- Plan: radar-2 model = TVL x categoryMultiple (snapshot-calibrated) x
  growth x maturity x chain-breadth; 200 rows (40 watchlist + 160 early);
  farm plans with per-chain gas costs and concrete steps; dashboard rewritten
  with chain filter, watchlist/early toggle, model panel with the calibrated
  multiples table, detail view with full model breakdown; 3 JPEG screenshots
  at 1440x900 via headless Chrome (playwright-core in shot-tools).

## 2026-11-17 ~10:50 WIB — model + dashboard built, pipeline green
- lib/model.ts (radar-2): calibrateMultiples (median mcap/TVL per category,
  n>=3, clamped [0.05, 1.0], CEX excluded, global fallback 0.25x n=119);
  impliedValue = TVL x catMult x growth x maturity x breadth; 7-step farm
  plans with per-chain gas tiers + TVL-trend step; boardIssue validates the
  gate's board rows. main.ts written fresh, revised 2x (dashboard presence
  check, top-3 summary line, dossier 1.5 MiB cap check).
- Dashboard rewritten: index.html + app.css (warm paper / emerald theme) +
  app.js (search, category + chain filters, watchlist/early scope tabs,
  pre-token/live tabs, 5 sort keys, detail drawer with full model breakdown
  + verbatim farm plan, model panel with calibration table, page recomputes
  every row and exposes window.__modelCheck). Total 33,464 bytes (cap 64K).
- Pipeline: 200 rows, 40/40 watchlist, 160 early, non-increasing implied
  values, ranks 1..200, all farm steps 20..600 chars. Top 3: Steakhouse
  Financial $641.9M, Gauntlet $382.3M, Veda $367.6M. Dossier 580,828 bytes.
- Screenshots (shot-tools/radar-shots.mjs, 1440x900 JPEG q85): 1-board.jpg
  125,365 B, 2-detail.jpg 157,432 B, 3-filtered.jpg 126,677 B. All three
  visually inspected: real data, no empty screens. Page model self-check:
  200/200 rows recompute to the board's numbers, 0 mismatches, no page
  errors (favicon 404 fixed with a data: icon link).

## 2026-11-17 ~10:55 WIB — gates verified, ready for the conductor
- `makefun check` on HEAD b64b6c5: 4 passed (competition_open, snapshot,
  license_osi, entrypoint), 1 failed (transcripts_present — the conductor
  bundles transcripts), 14 skipped, 5 checked at submission (repo public,
  owner, created after open, commit pushed, no duplicate — all satisfied).
- All 7 sealed gate rows verified independently against out/:
  dashboard_files, board_file, sorted_by_value, coverage (40/40),
  farm_steps, screenshots (3 JPEGs, 1440x900, 125–157 KB), dossier_matches
  (byte-identical rebuild, 580,828 bytes). ALL PASS.
- NOT bundled, NOT submitted — conductor's step.
