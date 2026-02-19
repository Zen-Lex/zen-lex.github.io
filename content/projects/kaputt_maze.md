---
title: "KaputtMaze: A Java Maze Runner Game"
summary: "A Java 2D maze game with enemies, traps and power-ups"
date: 2024-02-19T16:30:00+01:00
draft: false

tags: ["Java", "Object-oriented Programming", "Game Development"]

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
ShowReadingTime: false

ShowCodeCopyButtons: true
ShowPostNavLinks: true
ShowRssButtonInSectionTermList: false

disableHLJS: true
disableShare: true

disableSpecial1stPost: false
disableScrollToTop: false

description: "A Java/libGDX maze game made for Fundamentals of Programming, with focus on my gameplay systems work."

editPost:
  URL: "https://github.com/Zen-Lex/KaputtMaze"
  Text: "Source Code"
  appendFilePath: false
---

## Project context
`KaputtMaze` is a team project developed for the **Fundamentals of Programming** course at TUM Campus Heilbronn (WS 23/24).

The idea was to build a full 2D maze game with the Java library `libGDX`. All students started from the same template and a list of minimum requirements but were free to implement additional features and polish the game as much as they wanted. This assignment was done in teams of two, and we had around 6 weeks to complete the project.

The minimum requirements included:

- Loading and playing maze levels from text (`.properties`) files
- Controlling the player in 4 directions
- Managing lives and key collection (to unlock the exit) logic
- Handling static traps and dynamic enemies
- Displaying a permanent HUD
- Adding a menu (`Esc` pause + start menu),
- Implementing victory and game over screens,
- Integrating music/sound effects.

## Team work
The project was developed in pair programming/collaboration mode, and both of us contributed across the codebase.

### Peer programming
We iterated quickly with many short commits, merges and reworks, especially in the last sprint before submission.

I mainly focused on gameplay systems and object behaviors (collisions, enemies, collectibles, power-ups). While my teammate contributed significantly to menu/UI flows, audio and sound design, as well as core integration and testing/debbuging work.

In practice, we often worked in a classic peer-programming setup: one person was writing the code while the other reviewed in real time, challenged decisions, checked logic and edge cases.

### Object-oriented design
Another important part of the project was to apply a lot of core Java/OOP concepts in a practical way.

Most of the classes are implemented with inheritance in mind. This give us a clear hierarchy of objects, from less to more specific (`GameObject` -> `Character` -> `Player` / `Enemy`). Some of the classes are `Abstract` (like `Enemy`) to implement common behavior that can be further defined. 

We also used `Enums` and `Interfaces` to ensure consistency and operability across different objects type. 

This object-oriented structure allowed us to extract/reuse methods and avoid duplicated code. Making the code easier to extend when we added enemy variants, power-ups and bonus mechanics.

## My contribution
My main contribution was on the gameplay core and technical game systems.

I worked mostly on the classes driving the game logic (`MazeMap`, `Player`, `Enemy`, `Ghost`, `Bat`, `Warrior`, `Character`, `GameObject`, `GameScreen`) and on final documentation/polish.

### Movement and collision systems (including the light mechanics)
I also spent a lot of time on collisions and movement precision, including multiple commits only for hitbox fixes and offsets.

The key points were:

- map loading/parsing and playable map build through `MazeUtilities` and `MazeMap`,
- automatic wall texturing/rotation and spawn handling for traps, enemies and collectibles,
- collision points with float precision for smoother movement,
- separate collision handling for static map elements and dynamic entities,
- life loss handling when touching traps/enemies,
- key collection + exit opening logic,
- and invincibility delay after getting hit, to avoid unfair instant life loss loops.
- `LightBeam` polygon logic to compute visibility/light collisions against maze walls.

This system is what made the game feel playable and consistent, especially when multiple enemies and objects interact in the same area.

### Enemy behavior
A big part of my work was implementing the enemy side and turning enemies into more than random moving sprites.

We implemented three main different type of enemies, `Ghost`, `Bat` and `Warrior`, and those three types have different ways to move and interact with the player.

I implemented and integrated:

- `Ghost`: moves randomly, avoids entering the light, and stops when illuminated.
- `Bat`: flies over walls in a predefined area, starts chasing if the player enters this area, then relocates after a hit.
- `Warrior`: patrols, detects the player with eyesight, chases to the last seen position, then searches again in multiple directions.
- shared enemy collision logic so enemies avoid stacking each other,
- and the `LightBeam` polygon system used for both player light and enemy vision checks.

This gave us a game where enemy types feel different and create different pressure while navigating the maze.

### Collectibles & Power-Ups
Beyond minimal requirements, I also implemented several bonus gameplay features:

- collectible extra lives,
- two power-ups (`freeze` and `shrink`) with timer-based effects,
- power-up inventory/use from keyboard shortcuts (`1` and `2`),
- score calculation tied to time and remaining lives,
- and multi-level progression from the menu and victory screen.

The power-ups can be used by pressing the keys `1` (freeze) and `2` (shrink), both of them last for `20s`, and starting to use one power-up cancels the previous one if another one is already active.

The freeze power-up allows the player to freeze an enemy for a limited period, while the shrinking power-up reduces enemy size and hitbox for a limited period.

This is where the game moved from a simple course prototype to something closer to a complete small arcade project.

## References
- Repository: [Zen-Lex/KaputtMaze](https://github.com/Zen-Lex/KaputtMaze)
