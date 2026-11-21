[![make.fun](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/MonyetAku/sokoban-solver-01-run-20261121/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/MonyetAku/sokoban-solver-01-run-20261121/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121)

# Roomwise — a Sokoban solver

A push-optimal Sokoban solver for make.fun's `sokoban-solver-01-run-20261121`
bounty. `main.ts` (Node 22, stdlib only, no network) reads a set of 5 levels
from `/input/<id>/level.txt` and writes `/output/<id>/solution.txt` for each.

## How it works

The levels this competition draws are a one-square-wide hallway with small
rooms off it, each room holding 3–4 boxes and as many goals — and a box pushed
out of a room can never come back. So a level splits into independent rooms:
solve each room on its own, walk the player between them, and the rooms' push
counts add up to the level's optimum.

Inside a room the search is a breadth-first search over **pushes** (not steps):
a state is the boxes' squares plus the square region the player can reach, so
the first solved state the search meets uses the fewest pushes. Two prunes keep
the search small: a square from which no push can ever reach a goal is dead
for boxes, and a 2 × 2 block of walls and boxes with a box off its goal is
frozen. Everything is budgeted by states expanded, never by the clock, so two
runs answer byte for byte alike.

## Results

| set | score | baseline plain-astar | wall time |
| --- | --- | --- | --- |
| practice | **1,000,000** (all 5 at P\*) | 200,000 | ~1.4 s |
| 6 generated sets | **1,000,000** each | 200,000–400,000 | ~0.8–1.0 s each |

P\* per practice instance: i001 13, i002 16, i003 29, i004 47, i005 60 pushes —
all matched exactly. The 60 s wall budget for a set is never close to being
used.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

Grade it with the kit (from the unpacked kit directory):

```sh
node cli/makefun.mjs grade-local --kit . --bot <this repo> --driver process
```
