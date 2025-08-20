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
  image: "images/image1.png"
  alt: "Main menu of the Game of Life CLI showing the currently selected RLE file"
  caption: "Game of Life CLI — minimal TUI with RLE pattern selection"
  hidden: false

editPost:
  URL: "https://github.com/Zen-Lex/game-of-life-cli"
  Text: "Source Code"
  appendFilePath: false
---

#### A tiny but capable terminal app to play with Conway's Game of Life in C++

The project is a zero‑GUI, keyboard‑driven experience focused on speed, portability, and good terminal ergonomics. It loads classic patterns from RLE files, lets you tweak simulation options, and renders crisp ASCII frames in your shell.

### Two ways to try it

1) Run one of the provided demo patterns  
   - Launch the app and select a file from the bundled `rle/` directory (e.g., `rle/gosperglidergun.rle`).

2) Load your own pattern  
   - Pass a valid `.rle` path from the CLI or set it as the "current file" in the menu.

### Features at a glance

- Pure C++17+ implementation, portable across Linux/macOS/Windows terminals
- Menu‑driven CLI with keyboard controls: run/pause, step, reset, speed, wrap, etc.
- RLE pattern loader supporting headers (`x=`, `y=`, `rule=`) and run‑length encoded bodies
- Fast simulation core with double‑buffering and optional toroidal wrap
- Efficient ASCII rendering with minimal flicker and stable frame pacing
- Ships with a small library of well‑known patterns (Gosper glider gun, Pulsar, Infinite Growth, …)
- Deterministic stepping for reproducible runs

<div align="center">
  <img class="inbuilt-img" alt="Main menu with a Gosper Glider Gun selected" src="/images/image1.png" width="1280" height="500">
  <p class="text-muted caption">Main menu — current file: <code>rle/gosperglidergun.rle</code>.</p>
</div>

<div align="center">
  <img class="inbuilt-img" alt="Menu with the Infinite Growth pattern selected" src="/images/image3.png" width="1280" height="500">
  <p class="text-muted caption">Selecting <code>rle/infinitegrowth.rle</code> before launching the simulation.</p>
</div>

<div align="center">
  <img class="inbuilt-img" alt="Menu with the Pulsar pattern selected" src="/images/image4.png" width="1280" height="500">
  <p class="text-muted caption">Choosing <code>rle/pulsar.rle</code> for a classic oscillator showcase.</p>
</div>

### How it works

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

2) Pattern loading (RLE parser)
- Understands standard RLE headers:
  - x=<width>, y=<height>, rule=<ruleString>
- Data symbols:
  - o (alive), b (dead), $ (new line), ! (end)
  - Optional run counts, e.g., 23o = "23 alive cells"
- Workflow:
  - Parse header (and use rule override if present).
  - Decode body into alive coordinates.
  - Place the pattern onto the board (top‑left or centered).

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
- Rendering
  - ANSI/ASCII characters for live cells vs empty space.
  - Tight redraws to minimize flicker and maximize throughput.
  - Frame pacing to respect the chosen tick rate.

4) Configuration and options
- Pattern file path: any `.rle`
- Tick rate (TPS/FPS): simulation speed
- Wrap mode: bounded edges vs toroidal
- Initial placement: top‑left or centered
- Theme: characters used to render alive/dead cells (e.g., "█" vs " ")

5) File layout (representative)
```
.
├─ rle/                     # Sample patterns
│  ├─ gosperglidergun.rle
│  ├─ pulsar.rle
│  └─ infinitegrowth.rle
├─ src/
│  ├─ board.hpp/.cpp        # Grid, buffers, neighbor counting
│  ├─ rules.hpp/.cpp        # B3/S23 rule application
│  ├─ rle_parser.hpp/.cpp   # Header + body decoder
│  ├─ engine.hpp/.cpp       # Game loop, timing, input dispatch
│  ├─ renderer.hpp/.cpp     # Terminal drawing helpers
│  └─ cli.hpp/.cpp          # Menu, options, instructions
├─ include/                 # Public headers (if split)
├─ CMakeLists.txt
└─ README.md
```

6) Performance notes
- Double‑buffering avoids mid‑frame reads of freshly written states.
- Dense array layout enables tight inner loops and predictable neighbor scans.
- Build with optimizations (`-O2/-O3`) for smooth large‑grid runs.

7) Extensibility points
- Rules: accept generic "B…/S…" rule strings
- Storage: dense vs sparse grids for huge, sparse universes
- Rendering: color themes, Unicode tiles, or ncurses
- Export: dump generations to RLE or snapshots
- Inputs: random seeding, brush mode, or mouse via advanced TUI

### Instructions

- Navigate the menu with arrow keys or j/k; confirm with Enter
- Choose "Run" to start the simulation using the currently selected RLE
- While running:
  - Space: pause/resume
  - n: advance one generation
  - r: reset to the initial state
  - +/-: change speed
  - w: toggle wrap mode
  - q: back to menu

<div align="center">
  <img class="inbuilt-img" alt="Menu showing the main entries" src="/images/image2.png" width="1280" height="500">
  <p class="text-muted caption">Minimal keyboard‑driven TUI.</p>
</div>

### About Conway's Game of Life

Conway's Life is a cellular automaton on a 2D grid with simple birth/survival rules that produce complex behavior. Common phenomena include oscillators (e.g., Pulsar), spaceships (Glider), and guns (Gosper) that emit gliders indefinitely.

### Building and running

Requirements
- A C++17+ compiler (GCC, Clang, or MSVC)
- CMake (if the project uses CMake)
- A standard terminal

Build (typical CMake flow)
```bash
git clone https://github.com/Zen-Lex/game-of-life-cli.git
cd game-of-life-cli
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release
./build/game-of-life-cli  # or the platform's output path
```

Run with a specific pattern
```bash
./game-of-life-cli --file rle/gosperglidergun.rle
```

### Why I built it

- Practice modern, portable C++ in a terminal setting
- Explore algorithmic performance trade‑offs (dense vs sparse grids, buffer strategies)
- Implement the RLE format end‑to‑end
- Create a small, ergonomic tool to "feel" emergent complexity in real time

Have fun <3 😊