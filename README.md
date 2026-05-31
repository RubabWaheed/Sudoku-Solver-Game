# Sudoku-Solver-Game

# 🧩 Sudoku System

> A dual-mode console-based Sudoku game in C++. Either enter your own puzzle and let the computer solve it, or pick a difficulty and solve it yourself — with hints, undo, and a timer.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🤖 Auto Solver | Enter any valid puzzle, computer solves it via backtracking |
| 🎮 Play Mode | Choose a difficulty and solve the puzzle yourself |
| 🔢 3 Difficulty Levels | Easy (40 clues), Medium (30 clues), Hard (25 clues) |
| ↩️ Undo | Undo your last move at any time |
| 💡 Hint System | Get a random empty cell filled in for you |
| ⏱️ Timer | Tracks how long you take to solve the puzzle |
| ✅ Input Validation | Rejects moves that violate Sudoku rules |
| 👀 Show Solution | Reveal the answer and end the game |

---

## 🛠️ Tech Stack

- **Language:** C++
- **Standard:** C++98 or later
- **Libraries:** `iostream`, `vector`, `stack`, `ctime`, `cstdlib`, `sstream`
- **Algorithm:** Recursive backtracking solver

---

## 🚀 Getting Started

### Compilation

```bash
g++ -o sudoku sudoku.cpp
```

### Run

```bash
./sudoku
```

---

## 📋 Main Menu

```
========================================
         SUDOKU SYSTEM
========================================
  1. Enter Puzzle - Computer Solves It
  2. Play Game - You Solve the Puzzle
  3. How to Use
  0. Exit
```

---

## 🎮 Game Commands

| Command | Action |
|---|---|
| `M` | Make a move (enter row, col, value) |
| `U` | Undo last move |
| `H` | Get a hint |
| `S` | Show solution & end game |
| `Q` | Quit current game |

---

## 🎯 Difficulty Levels

| Level | Clues Given | Empty Cells |
|---|---|---|
| Easy | 40 | ~41 |
| Medium | 30 | ~51 |
| Hard | 25 | ~56 |

---

## 💡 How It Works

**Mode 1 — Auto Solver:**
1. Enter your puzzle row by row (9 numbers per row, `0` for empty)
2. System validates for duplicate conflicts
3. Backtracking algorithm fills in all empty cells

**Mode 2 — Play Mode:**
1. Select difficulty → puzzle loads
2. Enter moves with row, column, and value
3. Every move is validated against Sudoku rules
4. Timer runs until the board is complete or you quit

**Undo System:** Uses a `stack<Move>` — every move (including hints) is pushed onto the stack and can be popped with `U`.

---

## 🔢 Board Display

```
    1   2   3   4   5   6   7   8   9
  +-------+-------+-------+
1 | . . . | 2 6 . | 7 . 1 |
2 | 6 8 . | . 7 . | . 9 . |
  +-------+-------+-------+
  ...
```

---

## ⚠️ Known Limitations

- Game mode uses a single hardcoded solution — puzzle variety is limited
- No save/load functionality for in-progress games
- Solver has no uniqueness check (accepts puzzles with multiple solutions)

---

