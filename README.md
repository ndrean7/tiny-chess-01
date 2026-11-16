# Tiny Chess Engine (4 KB)

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/ndrean7/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/ndrean7/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in a single packed `main.py` (4,045 bytes) for the
[make.fun](https://make.fun/bounty/tiny-chess-01) bounty
"Build the strongest chess engine in 4 KB of Python".

## How it works

- **Board:** 0x88, with make/unmake and incremental Zobrist keys.
- **Search:** negamax alpha-beta with iterative deepening to a **fixed
  node budget** (100,000 nodes/move — deterministic, no clock, no
  randomness), transposition table, killer moves, MVV-LVA capture
  ordering, and a quiescence search over captures, promotions and
  replies to check. Mate scores are never stored in the TT, so
  mate-distance ranking stays correct across plies.
- **Eval:** material + piece-square tables (incremental), with
  endgame king centralization (king moves toward the centre once
  material drops below 1300), game-history repetition detection
  and an insufficient-material draw check.
- **Opening book:** 8 compact theory lines, keyed by a 5-digit
  position hash (Slav e3, Najdorf Bb5+ Nc6 / Nc3 a6 / Bg5 e6,
  Classical Nxc6 bxc6, English Reversed Sicilian e4 Nf6 / Bg2,
  Semi-Slav Nxe4 Ncxe4). Verified to fire correctly in real games;
  the book hash is the set-FEN key, because the server's FEN only
  names an en passant square when a capture is legal.
- **Protocol:** talks to the make.fun env over HTTP (`POST /game`,
  `/move`, `/resign`, UCI moves), exactly like the kit's starter.

`main.py` is the output of the kit's `makefun compress` applied to the
readable source `engine.py` (kept beside it, uncommitted). The engine
uses only the Python 3.13 standard library.

## Run locally

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 main.py
```

## Local results

Practice trials (6 games each vs the gauntlet's first six engines),
rebuilt 100k-node build: **trial 0 Elo 1,279, trial 1 Elo 1,099,
trial 2 Elo 1,099 — mean 1,159**, the same mean as the submitted
build (1,159). The rebuild does **not** raise practice Elo: the
opening book fires correctly (e.g. Najdorf Bb5+ Nc6 then Nxc6 bxc6
won game 2 of trial 0 in 52 plies vs 136 before; English Reversed
Sicilian e4 Nf6 now fires after the en-passant hash fix) and two
trial-0 losses became draws, but one trial-0 win became a loss and
trial 1 is unchanged. Perft vs python-chess passes on all 6 test
positions; board consistency bad = 0. Caveat: trial 2 had 2 losses
on time (moves over the 10 s limit in complex positions); the
submitted build had none. See `BUILD_NOTES.md` for the full log.

## License

MIT. See `LICENSE`.
