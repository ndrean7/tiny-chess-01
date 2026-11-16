# Tiny Chess Engine (4 KB)

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/ndrean7/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/ndrean7/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in a single packed `main.py` (4,032 bytes) for the
[make.fun](https://make.fun/bounty/tiny-chess-01) bounty
"Build the strongest chess engine in 4 KB of Python".

## How it works

- **Board:** 0x88, with make/unmake and incremental Zobrist keys.
- **Search:** negamax alpha-beta, iterative deepening to a **fixed
  node budget** (100,000 nodes/move, deterministic, no clock),
  transposition table, killer moves, MVV-LVA capture ordering, and
  quiescence over captures, promotions and replies to check. Mate
  scores are never stored in the TT.
- **Eval:** material + piece-square tables (incremental), endgame
  king centralization (king heads for the centre once material
  drops below 1300), a passed-pawn bonus in the endgame
  (up to +100 cp for a 7th-rank passer), game-history repetition
  detection and an insufficient-material draw check.
- **Protocol:** talks to the make.fun env over HTTP (`POST /game`,
  `/move`, `/resign`, UCI moves), exactly like the kit's starter.

`main.py` is the output of the kit's `makefun compress` applied to
the readable source `engine.py` (kept beside it, uncommitted).
Python 3.13 standard library only.

## Run locally

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 main.py
```

## Local results

Practice trials (6 games each vs the gauntlet's first six engines):
**trial 0 Elo 1,279, trial 1 Elo 1,279, trial 2 Elo 1,099 — mean
1,219**, vs 1,159 for the previous build. No losses on time in
any trial (max think 2.8 s vs the 10 s limit). Perft vs
python-chess passes on all 6 test positions; board consistency
bad = 0.

## License

MIT. See `LICENSE`.
