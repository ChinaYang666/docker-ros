# ROS1-ROS2 Bridge Docker Image

This repository provides instructions to use a Docker image designed for bridging between ROS1 and ROS2. The image is hosted on DockerHub and supports GPU acceleration using NVIDIA Docker.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Pulling the Docker Image](#pulling-the-docker-image)
- [Running the Container](#running-the-container)
- [Container Control Script](#container-control-script)
- [Setting Up the Bridge](#setting-up-the-bridge)
- [Testing the Bridge](#testing-the-bridge)
- [Installing NVIDIA Docker](#installing-nvidia-docker)
- [License](#license)

## Prerequisites

- Docker installed on your system.
- [NVIDIA Docker](#installing-nvidia-docker) if you require GPU support.
- ROS distributions installed in the container:
  - ROS Noetic for ROS1.
  - ROS Foxy for ROS2.
- A Linux environment with an X server if you need graphical support.

## Pulling the Docker Image

To pull the image from DockerHub, run the following command:

```bash
docker pull zrsheep/ros:v1.0
```

## Running the Container

You can run the Docker container using the following command. Here, the container is named `ros_bridge` (you can change this name if desired):

```bash
sudo docker run -dit \
  --name=ros_bridge \
  --privileged \
  -v /dev:/dev \
  -v /home/yang:/home/yang \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -e DISPLAY=unix$DISPLAY \
  -w /home/yang \
  --net=host \
  --gpus all \
  -e NVIDIA_DRIVER_CAPABILITIES=all \
  zrsheep/ros:v1.0
```

## Container Control Script

A shell script (`ros_bridge`) is provided to manage the container. The script is located in `~/docker/bin/` and added to your PATH via your `~/.bashrc` file:

```bash
export PATH=$PATH:/home/yang/docker/bin/
```

The path can be changed to the location of the bin folder where you store `ros_bridge`.

The script offers the following commands:

- **s**: Start the container.
- **r**: Restart the container.
- **e**: Enter the container (exec into a bash shell).
- **c**: Stop the container.
- **d**: Delete the container (stop, remove, and delete the script file).
- **t**: Test command: enter the container, source `/ros_entrypoint.sh`, and start `roscore`.

When you run `ros_bridge`, it prompts you to choose an action. For example, typing `s` starts the container and `e` enters it.

## Setting Up the Bridge

After entering the container, follow these steps to set up the bridge between ROS1 and ROS2:

### 1. ROSCore Start

Enter the container:
```bash
ros_bridge e
```

Source the ROS Noetic setup file:
```bash
source /opt/ros/noetic/setup.bash
```

Start the ROS core:
```bash
roscore
```

### 2. ROS2 Setup

Open a new terminal and enter the container:
```bash
ros_bridge e
```

Source the ROS Foxy setup file:
```bash
source /opt/ros/foxy/setup.bash
```

Update the `LD_LIBRARY_PATH` to include ROS Noetic libraries:
```bash
export LD_LIBRARY_PATH=/opt/ros/noetic/lib:$LD_LIBRARY_PATH
```

Launch the dynamic bridge:
```bash
ros2 run ros1_bridge dynamic_bridge --bridge-all-topics
```

## Testing the Bridge

To verify that the ROS1-ROS2 communication is working:

1. **Publish a ROS2 message:**
   - In a new terminal, enter the container:
     ```bash
     ros_bridge e
     ```
   - Source the ROS Foxy environment:
     ```bash
     source /opt/ros/foxy/setup.bash
     ```
   - Publish a message on the `/chatter` topic:
     ```bash
     ros2 topic pub /chatter std_msgs/String "data: 'Hello, ROS2'"
     ```

2. **List ROS1 topics:**
   - Open another terminal, enter the container:
     ```bash
     ros_bridge e
     ```
   - Source the ROS Noetic environment:
     ```bash
     source /opt/ros/noetic/setup.bash
     ```
   - List the topics to verify that `/chatter` is visible:
     ```bash
     rostopic list
     ```

If the `/chatter` topic appears in ROS1, the bridge is working correctly.

## Installing NVIDIA Docker

For GPU acceleration, you need to install NVIDIA Docker. Follow these steps:

1. **Configure the NVIDIA Docker repository and update:**

   ```bash
   curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
     && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
     sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
     sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
     && sudo apt-get update
   ```

2. **Install NVIDIA Docker2:**

   ```bash
   sudo apt-get install -y nvidia-docker2
   ```

3. **Configure the container runtime:**

   ```bash
   sudo nvidia-ctk runtime configure --runtime=docker
   ```

4. **Restart the Docker service:**

   ```bash
   sudo systemctl restart docker
   ```

> **Note:**
> 
> If you don't need to use the `ros_bridge` script, you can directly create a container using the following command after installing NVIDIA Docker support:
> 
> ```bash
> sudo docker run -dit \
>   --name=[name] \
>   --privileged \
>   -v /dev:/dev \
>   -v /home/yang:/home/yang \
>   -v /tmp/.X11-unix:/tmp/.X11-unix \
>   -e DISPLAY=unix$DISPLAY \
>   -w /home/yang \
>   --net=host \
>   --gpus all \
>   -e NVIDIA_DRIVER_CAPABILITIES=all \
>   [image]:[tag]
> ```

