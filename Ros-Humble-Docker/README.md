# ROS 2 Humble Docker Environment

This repository provides a **Docker-based ROS 2 Humble (desktop-full)** environment with GUI support, user permissions, and custom setup scripts (`bashrc`, `entrypoint.sh`).

It is designed to:

* Run ROS 2 GUI tools (Rviz, Gazebo) inside Docker.
* Maintain proper user permissions between host and container.
* Provide a clean, extensible development setup.

## Files in This Setup

### 1. **Dockerfile**

The Dockerfile builds a custom image based on `osrf/ros:humble-desktop-full` and performs the following:

* Installs extra packages (like `nano` and `sudo`).
* Copies project-specific configs into the container.
* Creates a **non-root user** (`ros`) to avoid permission issues when mounting volumes.
* Sets up passwordless sudo for convenience.
* Copies `entrypoint.sh` and a custom `bashrc` into the container.
* Defines entrypoint and command.

### 2. **bashrc**

```bash
source /opt/ros/humble/setup.bash
source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash
```

* **ROS 2 setup**: Sources the ROS 2 Humble environment (this ensures ROS tools are available in the shell).
* **Colcon autocompletion**: Enables shell completion for `colcon` (ROS 2 build tool).

> Even though the base image is Humble, you’ve provided Humble setup commands. Make sure they match your intended ROS distribution.

### 3. **entrypoint.sh**

```bash
#!/bin/bash

set -e

source /opt/ros/humble/setup.bash

echo "Provided arguments: $@"

exec $@
```

* `set -e`: Ensures script exits if any command fails.
* Sources ROS 2 Humble so every container session starts with ROS configured.
* Prints any arguments passed to the container (useful for debugging).
* `exec $@`: Replaces the script with the user’s command (e.g., `bash`, `ros2 run`, etc.).

## Running the Container

To run the container with GUI and networking:

```bash
docker run -it \
    --user ros \
    --network=host \
    --ipc=host \
    -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
    --env=DISPLAY \
    ros-humble
```

### X11 Access for GUI

Before running GUI tools like RViz, allow X11 connections:

```bash
xhost +             # Allow all clients (not secure)
xhost +local:       # Allow local clients
xhost +root:        # Allow root user
```

After use, revoke access:

```bash
xhost -
xhost -local:
```

## Docker Concepts Used

Here are the Docker concepts applied in this setup:

### 1. **Base Image**

```dockerfile
FROM osrf/ros:humble-desktop-full
```

* Starts from an official image with ROS 2 preinstalled.
* Saves time and avoids manual ROS installation.

### 2. **Layers**

Each `RUN`, `COPY`, or `ENV` in the Dockerfile creates a **layer**.
Layers are cached, so rebuilding only re-runs changed layers.

### 3. **Non-root User**

```dockerfile
--user ros
```

* Running as non-root avoids permission issues and improves security.
* UID/GID is matched to host user so mounted files are owned correctly.

### 4. **Volumes**

```bash
-v /tmp/.X11-unix:/tmp/.X11-unix:rw
```

* Mounts host directory into container for sharing files/sockets.
* Here it shares X11 socket to run GUI apps.

### 5. **Networking**

```bash
--network=host
```

* Shares host’s network stack with container.
* Crucial for ROS 2 DDS discovery to work across host and container.

### 6. **IPC Namespace**

```bash
--ipc=host
```

* Shares host’s IPC (inter-process communication).
* Required for some ROS 2 features that exchange data between processes.

### 7. **Entrypoint vs CMD**

* **ENTRYPOINT**: Defines the container’s startup script (`entrypoint.sh`).
* **CMD**: Default arguments passed to entrypoint (`bash` if nothing else is specified).
* Together: `docker run ros-humble ros2 run demo_nodes_cpp talker`
  → runs inside `entrypoint.sh` automatically sourcing ROS.

### 8. **Environment Variables**

```dockerfile
ENV key=value
```

* Defines environment variables for container.
* Useful for ROS settings like `ROS_DOMAIN_ID` or `ROS_LOCALHOST_ONLY`.

## Example Usage

### Run interactive container:

```bash
docker run -it --rm \
    --user ros \
    --network=host \
    --ipc=host \
    -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
    --env=DISPLAY \
    ros-humble
```

### Run Rviz2:

Inside container:

```bash
rviz2
```

### Run a ROS node directly:

```bash
docker run -it --rm \
    --user ros \
    --network=host \
    ros-humble ros2 run demo_nodes_cpp talker
```

## Customization

* Update `bashrc` to include workspace sourcing:

  ```bash
  source ~/ros_ws/install/setup.bash
  ```
* Extend `entrypoint.sh` to automatically launch your robot stack.
* Add more apt packages in Dockerfile as needed.
* Set `ENV ROS_DOMAIN_ID=42` in Dockerfile if using multiple robots.

## Architecture Diagram

Here’s how the container interacts with the host system:

```bash
+-------------------------------------------------------------+
|                         Host Machine                        |
|                                                             |
|  +------------------+         +-------------------------+   |
|  |   X11 Display    |<------->|  /tmp/.X11-unix volume  |   |
|  | (Rviz, Gazebo UI)|         +-------------------------+   |
|  +------------------+                                       |
|  +---------------------+                                    |
|  |   ROS Networking    | <---->  --network=host             |
|  | (DDS Discovery)     |                                    |
|  +---------------------+                                    |
|                                                             |
|  +------------------------------------------------------+   |
|  |                   Docker Container                   |   |
|  |                                                      |   |
|  |  User: ros (UID:GID matches host)                    |   |
|  |                                                      |   |
|  |  +------------------+   +-------------------------+  |   |
|  |  | entrypoint.sh    | → | Sources ROS environment |  |   |
|  |  | bashrc           | → | Setup colcon, aliases   |  |   |
|  |  +------------------+   +-------------------------+  |   |
|  |                                                      |   |
|  |  ROS 2 Humble tools: rviz2, gazebo, ros2 run ...      |   |
|  +------------------------------------------------------+   |
|                                                             |
+-------------------------------------------------------------+
```