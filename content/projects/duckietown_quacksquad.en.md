---
title: "Duckietown: QuackSquad"
summary: "University project on autonomous driving in Duckietown, with emphasis on lane following and a custom web dashboard."
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

description: "A Duckietown stack focused on reliable lane following and a web dashboard for live monitoring and control."

editPost:
  URL: "https://github.com/DuckietownTUM/QuackSquad"
  Text: "Source Code"
  appendFilePath: false
---

#### Teaching a Duckiebot to drive itself — robust lane following with a live web dashboard

This project assembles and customizes core Duckietown components into a cohesive system. The codebase features a lane‑following stack, additional perception modules, and a web interface to monitor and control the robot. The following summary highlights the lane following pipeline and the dashboard, based on the project repository and its accompanying report.

### Key goals

- Robust lane keeping on standard Duckietown maps
- Smooth, reactive control in presence of curves and intersections
- Operator visibility and tunability via a web dashboard
- Containerized deployment for reproducibility

### Architecture overview

- Containers
  - dt-core: core behavior and perception nodes (e.g., lane_control, stop_line_filter, FSM)
  - dt-car-interface: low‑level actuation and teleoperation modules (e.g., motor driver, joystick mapper)
- Perception and control
  - Camera stream → lane feature extraction → lateral/heading error estimation → control
  - Additional modules for stop line detection, AprilTag‑based localization, and dead reckoning
- Coordination
  - A finite state machine (FSM) supervises behaviors like lane following, stopping, and intersections
- Web layer
  - duckie‑web: a web dashboard that visualizes telemetry, streams video, and exposes controls

Repository structure (excerpt)
```
duckietown/
├── containers/
│   ├── dt-car-interface/
│   └── dt-core/
│       ├── launchers/
│       └── packages/
│           ├── lane_control/        # Lane following controller and helpers
│           ├── stop_line_filter/    # Stop line perception
│           ├── fsm/                 # State machine orchestrating behaviors
│           └── ...                  # AprilTags, vehicle detection, etc.
├── duckie-web/                       # Web dashboard
└── calibrations/                     # Camera and wheel calibration data
```

### Lane following

- Input
  - Calibrated camera feed (rectified) from the front‑facing sensor.
- Feature extraction
  - Color‑space filtering for Duckietown lane colors (e.g., yellow/white), combined with ROI selection to focus on the roadway.
  - Optional perspective handling to stabilize geometry across frames.
- Lane geometry estimation
  - Extraction of lane markings and computation of lateral offset and heading error relative to the lane centerline.
- Control
  - A feedback controller (e.g., PID on lateral/heading error) maps the estimated errors to angular velocity while maintaining forward speed.
  - Dynamic constraints to ensure smoothness and respect the platform limits.
- Behavior integration
  - The FSM can switch between lane following, stopping at stop lines, and other maneuvers based on perception events.

Why it works well
- Separation of concerns: perception → estimation → control, each tunable independently.
- Clear interfaces (ROS topics) for debugging and swapping components.
- Calibration data tracked in the repo to keep perception numerically stable.

### Web dashboard (duckie‑web)

- Live monitoring
  - Real‑time telemetry: speed, steering, state (FSM mode), and health.
  - Video streaming to visualize the current route and debug perception artifacts.
- Control and tuning
  - Buttons/toggles to enable/disable behaviors (e.g., start/stop lane following).
  - Live parameter adjustments for the controller and perception thresholds.
- Developer ergonomics
  - Works alongside the containerized stack.
  - Designed to be opened from a laptop on the same network as the robot.

### FSM and safety overlays

- The FSM node orchestrates transitions between behaviors (e.g., start, lane follow, stop at line, handle intersection).
- Safety hooks (e.g., stop line filter events) pause or modify control outputs.

### Build and run (high level)

- Requirements
  - Docker and Duckietown base images.
  - A calibrated Duckiebot (camera + wheels).
- Typical workflow
  - Build or pull the containers (dt-core, dt-car-interface, duckie‑web).
  - Launch core nodes, then start lane_control and the dashboard.
  - Adjust parameters from the web UI while observing closed‑loop behavior.

### What we learned

- Reliable lane following depends on good calibration and careful signal filtering.
- A web dashboard drastically shortens the debug loop by exposing live telemetry and parameters.
- Modular containers make it easy to iterate on perception or control without destabilizing the system.

🌟 Happy Navigation! Quack! 🦆🚗✨