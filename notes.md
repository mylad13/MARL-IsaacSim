Helpful links:
https://forums.developer.nvidia.com/t/ros2-humble-requesting-update-on-source-code-for-7-multiple-robot-ros2-navigation-tutorial-that-is-compatible-with-ros2-humble/255166/5
https://robotics.stackexchange.com/questions/103075/ros2-nav2-not-starting-with-namespace
[Using Isaac Sim with ROS2: A Step-by-Step Guide](https://www.youtube.com/watch?v=L1rpxRm0Q1w)

---

I have installed Isaac Sim's Workstation version.
Going to run ROS2 Humble in a docker container following [these instructions](https://docs.omniverse.nvidia.com/isaacsim/latest/installation/install_ros.html#isaac-ros-docker).
```bash
pip install rocker
sudo rocker --nvidia --x11 --privileged --network host  --name rocker-humble osrf/ros:humble-desktop-full-jammy
```
Then I copied the Isaac-Sim's humble_ws to the running ROS container. Went into the cloned IsaacSim-ros_workspaces directory, and:
```bash
docker cp humble_ws rocker-humble:/root/humble_ws
```
Proceed to [enable the ROS 2 Bridge](https://docs.omniverse.nvidia.com/isaacsim/latest/installation/install_ros.html#isaac-sim-app-enable-ros). Inside the container, run:
```bash
export FASTRTPS_DEFAULT_PROFILES_FILE=~/humble_ws/fastdds.xml
```
It says you must set the environment variable by typing export FASTRTPS_DEFAULT_PROFILES_FILE=<path_to_ros2_ws>/fastdds.xml in all the terminals that will use ROS 2 functions. You must also set it under “Extra Args” when launching Isaac Sim from the NVIDIA Omniverse™ Launcher. In that case, I guess I have to put this in extra args:
```bash
export FASTRTPS_DEFAULT_PROFILES_FILE=/home/farjadnm/IsaacSim-ros_workspaces/humble_ws/fastdds.xml
``` 
I then sourced the Docker's ROS 2:
```bash
source /opt/ros/humble/setup.sh
```
I then set up a simple clock node using action graphs in Isaac sim, and I was able to echo the /clock topic in ROS 2 inside the container! Moving onto [Multiple Robot ROS2 Navigation](https://docs.omniverse.nvidia.com/isaacsim/latest/ros2_tutorials/tutorial_ros2_multi_navigation.html).
