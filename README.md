# Token Radar — pre-token protocols ranked by implied value

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/feltonmosz/makefun-token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/feltonmosz/makefun-token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Entry for [make.fun's **token-radar-01** bounty](https://make.fun/bounty/token-radar-01): build the best token launch radar.

The program (`main.ts`, Node 22, standard library only) reads the sealed
DefiLlama snapshot, scores every pre-token protocol with the model in
`lib/model.ts`, and writes a ranked board, a static dashboard and a dossier
to `/output`.

## The model (radar-2)

Implied value is derived only from the snapshot's own figures — no protocol
name is ever special-cased:

```
implied = round( tvl_usd x categoryMultiple(category)
               x growthMult x maturityMult x breadthMult )
```

- **categoryMultiple** — the median market-cap-to-TVL ratio of the
  live-token protocols in the same category, computed from this snapshot
  (at least 3 samples, else the global median), clamped to [0.05, 1.0].
  The market's own pricing of each kind of protocol calibrates the model.
- **growthMult** — `1 + clamp(change_7d, -50, 100) / 100 x 0.5`.
- **maturityMult** — `0.6 + min(age_days, 1095) / 1095 x 0.4`, where age is
  the time since DefiLlama first listed the protocol (unknown age: 365 days).
- **breadthMult** — `1 + 0.05 x min(chains - 1, 4)`: a small premium for
  protocols live on more chains.

The board covers all 40 watchlist protocols plus the 160 pre-token protocols
below the watchlist floor with the most TVL (200 rows). Live-token protocols
are set aside in their own view with their market caps shown, never mixed
into the ranking.

## Run it

```
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

## Layout

- `main.ts` — the entry point: snapshot in, `/output` out.
- `lib/model.ts` — the model, the board builder, the farm plans.
- `vendor/makefun-board.ts` — make.fun's dossier library (vendored).
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`.
  It reads `data.js` beside it and renders with no network.
- `screenshots/` — the board, a detail view, a filtered view.
