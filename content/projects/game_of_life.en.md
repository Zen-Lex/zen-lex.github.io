---
title: "Game of Life CLI"
summary: "A fast, portable, C++ command‑line implementation of Conway's Game of Life with RLE pattern loading and a minimal TUI."
date: 2025-08-20T15:22:57+00:00
draft: true

showToc: true
TocOpen: false
UseHugoToc: true

comments: false
hideSummary: false

hidemeta: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowWordCount: false
ShowAuthor: false
ShowTranslationList: false

ShowCodeCopyButtons: true
ShowPostNavLinks: true
ShowRssButtonInSectionTermList: false

disableHLJS: true
disableShare: true

disableSpecial1stPost: false
disableScrollToTop: false

description: "A C++17+ terminal app to explore Conway's Game of Life, featuring a menu‑driven CLI, RLE pattern support, and a performant simulation core."

cover:
  image: "images/gof_gosperglidergun_run.gif"
  alt: "Main menu of the Game of Life CLI showing the currently selected RLE file"
  caption: "Game of Life CLI — minimal TUI with RLE pattern selection"
  hidden: false

editPost:
  URL: "https://github.com/Zen-Lex/game-of-life-cli"
  Text: "Source Code"
  appendFilePath: false
---

<!-- #### A tiny but capable terminal app to play with Conway's Game of Life in C++ -->

The project is a zero‑GUI, keyboard‑driven experience focused on speed, portability, and good terminal ergonomics. It loads classic patterns from RLE files, lets you tweak simulation options, and renders crisp ASCII frames in your shell.

## Conway's Game of Life

Conway's Life is a cellular automaton on a 2D grid with simple birth/survival rules that produce complex behavior. Common phenomena include oscillators (e.g., Pulsar), spaceships (Glider), and guns (Gosper) that emit gliders indefinitely.

## Building and running

#### Requirements
- A C++20+ compiler (GCC, Clang, or MSVC)
- A standard terminal

#### Build
```bash
git clone https://github.com/Zen-Lex/game-of-life-cli.git
cd game-of-life-cli
g++-13 -std=c++20 project.cpp gameoflife.cpp grid.cpp menu.cpp parser.cpp -fsanitize=address -lncurses -pthread -o build/project
```

#### Run
```bash
./build/project
```

## Instructions
- Navigate the menu with arrow keys or j/k; confirm with Enter
- Choose "Run" to start the simulation using the currently selected RLE
- While running:
  - Space: pause/resume
  - n: advance one generation
  - r: reset to the initial state
  - +/-: change speed
  - w: toggle wrap mode
  - q: back to menu

## Features
- Pure modern C++ implementation, portable across Linux/macOS/Windows terminals
- Menu‑driven CLI with keyboard controls: run/pause, step, reset, speed, wrap, etc.
- RLE pattern loader supporting headers (`x=`, `y=`, `rule=`) and run‑length encoded bodies
- Fast simulation core with double‑buffering and optional toroidal wrap
- Efficient ASCII rendering with minimal flicker and stable frame pacing
- Ships with a small library of well‑known patterns (Gosper glider gun, Pulsar, Infinite Growth, …)

<!-- ![Yellow Duck](/img/gof_menus.gif  'Yellow Duck') -->

### Board Game
Most of the time in the game of life, the grid representing a game after x iterations will contain more dead than live cells.
Therefore using a 2D array of bool values is very useful for computing the next state of the game but will result in storing a lot of dead cells, i.e. data without useful information.

To avoid this, the state of the game is stored in a `vector<cell>`, an array of `Cell`, simple `struct` storing the position `x,y` of the cell. With this approach only live cells and there position are stored.

### Pattern loading (RLE parser)
The RLE (short for Run Length Encoded) is a file format used to efficiently store game of life's patterns.
Each RLE file is composed of two parts: a header and the pattern on itself. The parttern is itself composed of 4 data symbols:
  - o (live cell)
  - b (dead cell)
  - $ (new line)
  - ! (end of file)

The symbols for the cell (o or b) can be preceded by a run count (e.g., `5o` for five live cells).

The program is parsing RLE file line by line. For each line the parser will look each character sequencially
In case of a number, the program 

### Terminal UI
3) Terminal UI (menu + controls)
- Startup menu
  - "Run (current file: …)"
  - "Instructions"
  - "Options"
  - "About The Game of Life"
  - "Exit"
- During simulation
  - Keyboard shortcuts (example set):
    - Space: pause/resume
    - n: single step
    - r: reset to initial pattern
    - +/-: adjust ticks per second
    - w: toggle wrap mode
    - q: quit to menu

## How it works

The app is split into small, focused modules. Below is an overview of the architecture and the key responsibilities of each part.

1) Core domain (Board, Rules, Simulation)


- Board/grid
  - Represents cells as a 2D array (dense) for cache‑friendly neighbor scans.
  - Two buffers: one for reading the current generation, one for writing the next ("double‑buffering").
  - Boundary modes:
    - Bounded edges (outside treated as dead)
    - Toroidal wrap (edges connect to opposite sides)
- Cell state
  - Binary: alive (1) or dead (0), stored compactly.
- Rules
  - Default Conway B3/S23 (born on 3, survive on 2 or 3).
  - Rule evaluation counts 8 neighbors and applies the rule set to compute the next state.
- Simulation engine
  - Drives step():
    - For each cell, compute neighbor count from the read buffer.
    - Apply rules; write results into the write buffer.
    - Swap buffers after a full pass.
  - Exposes controls: run/pause, single step, reset, tick rate.

## Extensibility points
- Rules: accept generic "B…/S…" rule strings
- Storage: dense vs sparse grids for huge, sparse universes
- Rendering: color themes, Unicode tiles, or ncurses
- Export: dump generations to RLE or snapshots
- Inputs: random seeding, brush mode, or mouse via advanced TUI