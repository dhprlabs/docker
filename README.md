# Docker 

A compact set of Docker examples and notes focused on:

- ROS 2 (Humble) GUI-ready image and helpers
- A minimal example project
- Installation and basic Docker commands

Repository layout

- Ros-Humble-Docker/ — Dockerfile, entrypoint.sh, bashrc and a dedicated README with GUI/X11 tips.
- My-First-Docker-Project/ — minimal image example with config/ and params.yaml.
- Installation/ — notes for installing Docker (Ubuntu).
- Basic-Commands/ — quick reference for common Docker commands.

Quick start

Build the ROS Humble image (from repository root):
```bash
docker build -t ros-humble Ros-Humble-Docker
```

Run (example with X11 GUI support):
```bash
docker run -it --user ros --network=host --ipc=host \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw --env=DISPLAY ros-humble
```

Notes
- See Ros-Humble-Docker/README.md for ROS sourcing, entrypoint behavior and GUI tips.
- See My-First-Docker-Project/README.md for the minimal example and configuration details.
- Installation/ contains OS-specific Docker install instructions.