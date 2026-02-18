---
title: "ConnecTUM: An Automated Connect 4"
summary: "A fully automated Connect 4 Game"
date: 2025-09-25T16:30:00+01:00
draft: false

tags: ["Python", "OpenCV"]

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

description: "A fully automated Connect 4 game with emphasis on my work on computer vision and more."

editPost:
  URL: "https://github.com/CPSCourse-TUM-HN/connecTUM"
  Text: "Source Code"
  appendFilePath: false
---

<video width="100%" controls autoplay muted loop>
  <source src="/videos/connectum_short.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
<figcaption>Final assembly of the first version</figcaption>

## Project context
`connecTUM` is a team project developed at TUM for the Cyber-Physical System course:

It's a 3D printed & automated version of the popular game Connect 4 that can:

- detect the current board state from a camera,
- compute the next (best) move with an algorithm,
- and execute the move through the hardware.


## My contribution
My main contribution was on the software side, especially on the computer vision, the main game loop and the CLI (report sections **5 - Computer Vision**, **6.1 - File Hierarchy**, and **6.2 - Command Line Interface**).

### Computer Vision
The main task I had on the project was to implement the camera pipeline that transforms live frames into a reliable `7x6` game state array. I describe here the most important features and underlying implementations.

<div class="side-by-side">
  <div class="side-by-side__text">
    <ul>
      <li>OpenCV-based processing of each frame (<code>camera.py</code>)</li>
      <li>color-based coin segmentation in HSV for red/yellow detection</li>
      <li>support for two color strategies:
        <ul>
          <li><code>FIX_RANGE</code>: static HSV thresholds</li>
          <li><code>DYNAMIC_RANGE</code>: thresholds recomputed from a calibration zone each frame</li>
        </ul>
      </li>
      <li>optional preprocessing steps for robustness in real lighting conditions:
        <ul>
          <li>white balance</li>
          <li>gray-world normalization</li>
          <li>histogram-based global normalization</li>
          <li>blur and red-noise reduction</li>
        </ul>
      </li>
      <li>grid computation and validation before exposing a move to the game loop</li>
    </ul>
    <p>A numerous number of options can be choosen in preprocessing to improve the coins recognation. But using the <code>DYNAMIC_RANGE</code> strategy is overall more reliable in most light conditions.</p>
    <p>The general workflow, from the raw input picture to the final game board array is furthermore describe here in the picture.</p>
    <p>To improve the robustness of the coin detection and minimize errors due to noise, occlusions or vibrations I implemented a grid accumulator approach in the <code>grid_accumulator()</code> method.</p>
    <p>Instead of relying on a single frame to determine the board state, the program processes multiple consecutive frames and keeps an ongoing count for each cell in the grid.</p>
  </div>
  <figure class="side-by-side__media">
    <img src="/images/camera_vision_pipeline.jpg" alt="Schema of the camera vision pipeline">
    <figcaption>Schema of the camera vision pipeline</figcaption>
  </figure>
</div>

### File Hierarchy and Process Split
I also worked on the project structure to keep complex behavior maintainable.

The game logic (handled in `main.py`) runs independantly from the camera. Both parts communicate trought multiprocessing shared state.
The motors are only called when needed and run throught their dedicated file.

This separation makes debugging and testing much easier. For example, the camera process can be runned alone (via `camera.py`) to test the camera setup and calibrate it if needed. Conversely, the main game logic can be launched without the camera which was helpful to improve and debug the Connect4 algorithm.

This logic is also true for the motors. In short, every important part can be isolated (or combined) for testing, debugging or calibrating.

This process separation is also essential to insure a smooth and seamless experience. Indeed, this allows the camera to work "in the background",ensuring a reliable lecture of the game board without blocking the main loop (while the algorithm is computing or the motors are moving for example).

### Command Line Interface
Finally I also developped a small but very handy CLI for a better development workflow and user experience.

The program has a single entry point (`python3 main.py`) with multiple options:

```text
usage: main.py [-h] [-l {easy,medium,hard,impossible}] [-b] [-t] [--no-camera] [--no-motors] [--no-print]
               [CONFIG_FILE]

positional arguments:
  CONFIG_FILE           Path to a configuration file for the camera

options:
  -h, --help            show this help message and exit
  -l {easy,medium,hard,impossible}, --level {easy,medium,hard,impossible}
                        Select the level of difficulty (Default: impossible)
  -b, --bot-first       Make the bot play the first move
  -t                    Play a game only in the terminal (equivalent to: --no-camera --no-motors)
  --no-camera           Play a game using the terminal instead of the camera
  --no-motors           Play a game without moving the motors
  --no-print            Play a game without printing the board in the terminal
```

By default, the program is launched with the camera and the motors running.
This CLI is really what enables the isolation of the different parts (game logic; camera; motors) with ease.

It also come with a user friendly rendering of the current game board in the terminal.

<div class="side-by-side" style="justify-content: center;">
  <figure class="side-by-side__media">
    <img src="/images/terminal_input.png" alt="Terminal input">
    <figcaption>Terminal output during a game</figcaption>
  </figure>
  <figure class="side-by-side__media">
    <img src="/images/terminal_final.png" alt="Terminal final">
    <figcaption>Terminal output after a game</figcaption>
  </figure>
</div>

## References
- Repository: [CPSCourse-TUM-HN/connecTUM](https://github.com/CPSCourse-TUM-HN/connecTUM)
- Final report and presentation: linked in the repository `presentation/` folder
