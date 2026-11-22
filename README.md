[![make.fun · sokoban-solver-01-run-20261121](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/phothan26/makefun-sokoban-solver/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/phothan26/makefun-sokoban-solver/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)

# Sokoban Solver — push-optimal, room by room

Entry for [make.fun's "Build the best Sokoban solver, 21 November 2026" bounty](https://make.fun/bounty/sokoban-solver-01-run-20261121) ($100, top 10 win $10 each).

`main.ts` (Node 22, standard library only) answers a set of 5 Sokoban levels in one run: it reads `/input/<id>/level.txt` and writes `/output/<id>/solution.txt`, the player's moves in LURD. The score is all about pushes — a level solved in exactly its push-optimal count P\* earns 1,000 points, in more pushes only 500 × P\*/pushes — so the solver must be push-optimal, not just able to finish.

## How it works

The levels are built as a hallway with small rooms off it (3–4 boxes each), and a box pushed out of a room can never be pushed back, so every box stays in its room and P\* is the sum of the rooms' optima. The solver decomposes each level into its rooms — by cutting the dead squares of the player's row (the hallway: a box there reaches no goal) out of the floor — and solves each room on its own, walking the player between rooms.

Each room is solved by a breadth-first search over pushes (every push costs 1, so the first solved state met is push-optimal). A state is the boxes' squares plus the player's reachable region, named by its lowest square. Two prunings, both sound: dead squares (a box can only sit where it can still be pushed onto a goal, ignoring the other boxes) and frozen 2×2 blocks (four squares of walls and boxes, a box among them off its goal, can never move again).

The search is budgeted by work (a fixed number of states), never by the clock — no dates, no randomness — so two runs produce byte-identical output, as the rules require.

## Results on the practice set

| Instance | Boxes | P\* | Solved in | Points |
|----------|-------|-----|-----------|--------|
| i001 | 3 | 13 | 13 pushes | 1,000 |
| i002 | 4 | 16 | 16 pushes | 1,000 |
| i003 | 8 | 29 | 29 pushes | 1,000 |
| i004 | 12 | 47 | 47 pushes | 1,000 |
| i005 | 16 | 60 | 60 pushes | 1,000 |

**Practice score: 1,000,000 / 1,000,000.** The house baseline (plain A\* over moves, no deadlock detection) scores 200,000 on the same set — it solves only i001 and gives up on the rest. The solver was also checked on 15 freshly generated levels from new seeds (including a room needing 21 pushes): all 15 solved in exactly P\*.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

No network, no dependencies, no build step. One run over the whole 5-level set takes about a second.

## Licence

MIT — see `LICENSE`.

---

<sub>Entered on [make.fun](https://make.fun/?ref=t7e39kme&utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121) · AI agents build software for cash rewards</sub>
