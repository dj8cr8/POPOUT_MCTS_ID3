# PopOut — MCTS & ID3 Decision Tree AI

A Python implementation of **PopOut**, a Connect-4 variant, featuring autonomous AI agents powered by **Monte Carlo Tree Search (MCTS)** and a supervised **ID3 Decision Tree** trained to imitate MCTS play.

> Artificial Intelligence course project · 2025/2026  
> Rafael Silva · Guilherme Varandas

---

## What is PopOut?

PopOut is a Connect-4 variant played on a 6×7 board. In addition to dropping pieces from the top, a player may **pop** one of their own pieces from the bottom of any column, causing all pieces above it to fall down.

Three extra rules apply:

| Rule | Description |
|------|-------------|
| **Simultaneous Pop** | If a pop creates a 4-in-a-row for both players, the player who popped wins. |
| **Full Board** | If the board is full, the moving player may pop a piece or declare a draw. |
| **Threefold Repetition** | If the same board state occurs 3 times, either player may declare a draw. |

---

## Project Structure

```
.
├── game/
│   ├── board.py              # 6×7 board, drop and pop operations
│   └── logic.py              # Win detection, special rules, GameState
├── Mcts/
│   ├── mcts.py               # MCTS with UCT selection
│   └── mctsAlternativo.py    # MCTS with ε-greedy selection
├── decision_tree/
│   ├── id3.py                # ID3 decision tree (build, predict, evaluate)
│   └── popout_player.py      # ID3 move chooser for PopOut
├── auxiliares/
│   └── helpers.py            # apply_move, next_player, get_valid_moves
├── main.py                   # Interactive game loop (all modes)
├── popout_dataset.py         # Dataset generator (MCTS-labelled board states)
├── popout_tree_demo.py       # Train and evaluate the ID3 tree
├── iris_demo.py              # Standalone ID3 demo on the Iris dataset
├── iris.csv                  # Iris dataset
├── popout_mcts_dataset.csv   # Pre-generated PopOut dataset
└── note.ipynb                # Full project report with experiments
```

---

## AI Agents

### MCTS with UCT (`Mcts/mcts.py`)
Standard Monte Carlo Tree Search using the **Upper Confidence Bound for Trees** formula for node selection. Balances exploration and exploitation during tree search. Includes a **heuristic rollout** that prioritises immediate wins, blocks, and positional evaluation.

### MCTS with ε-greedy (`Mcts/mctsAlternativo.py`)
An alternative MCTS variant where node selection uses a binary probabilistic policy (ε-greedy) instead of UCT. Simpler to tune but generally weaker — useful as a baseline for comparison.

### ID3 Decision Tree (`decision_tree/id3.py`)
A supervised model trained to **imitate MCTS move choices**. The board state is encoded as 42 numeric features (`cell_row_col` ∈ {-1, 0, 1} from the current player's perspective) and the target label is the move chosen by MCTS. Once trained, the tree selects moves near-instantly.

---

## Game Modes

Run `python main.py` and choose:

```
1 — Human vs Human
2 — Human vs Computer (MCTS UCT)
3 — Computer vs Computer (MCTS UCT vs MCTS ε-greedy)
4 — Human vs Computer (ID3 Decision Tree)
5 — Computer vs Computer (MCTS UCT vs ID3 Decision Tree)
```

---

## Dataset Generation

The dataset is created by having MCTS play games and recording `(board_state → move)` pairs:

```bash
python popout_dataset.py --games 80 --iterations 25 --max-examples 1000 --output popout_mcts_dataset.csv
```

Key options:

| Flag | Default | Description |
|------|---------|-------------|
| `--games` | 80 | Number of games to simulate |
| `--iterations` | 25 | MCTS iterations per move |
| `--max-examples` | 1000 | Cap on total examples |
| `--max-random-opening` | 8 | Random moves played before MCTS takes over |
| `--seed` | — | Random seed for reproducibility |

---

## Training & Evaluating the ID3 Tree

```bash
python popout_tree_demo.py --dataset popout_mcts_dataset.csv --max-depth 15
```

Add `--show-tree` to print the full learned tree. Reported metrics include train/test accuracy and the fraction of predicted moves that are legal.

---

## Iris Demo

A standalone demonstration of the ID3 implementation on the classic Iris dataset, useful for verifying the tree independently of the game logic:

```bash
python iris_demo.py
```

---

## Results

With **1000 MCTS iterations** per move:

- **MCTS UCT won 90% of games** against MCTS ε-greedy, confirming the theoretical advantage of UCT in moderate state-space games with informative rollouts.
- The **ID3 tree** acts as a fast approximation to MCTS — move selection is near-instantaneous after training, but quality depends heavily on dataset size and diversity.

---


