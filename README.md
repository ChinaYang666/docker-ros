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
Running the Container
