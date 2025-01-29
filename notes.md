Helpful links:
https://forums.developer.nvidia.com/t/ros2-humble-requesting-update-on-source-code-for-7-multiple-robot-ros2-navigation-tutorial-that-is-compatible-with-ros2-humble/255166/5
https://robotics.stackexchange.com/questions/103075/ros2-nav2-not-starting-with-namespace
[Using Isaac Sim with ROS2: A Step-by-Step Guide](https://www.youtube.com/watch?v=L1rpxRm0Q1w)

---
## Installation
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

---

Scene generation: [Replicator](https://docs.omniverse.nvidia.com/isaacsim/latest/replicator_tutorials/tutorial_replicator_getting_started.html)

---

## Standalone Python Script
Trying to run a standalone python script (carter_multiple_robot_navigation.py). You have to be in the directory "/home/farjadnm/.local/share/ov/pkg/isaac-sim-4.2.0" and run
```bash
./python.sh /home/farjadnm/.local/share/ov/pkg/isaac-sim-4.2.0/standalone_examples/api/omni.isaac.ros2_bridge/carter_multiple_robot_navigation.py
```
or replace the script path with whatever you want to run. I got the error "ROS2 Bridge Startup failed". Run this in the same terminal to fix:
```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/farjadnm/.local/share/ov/pkg/isaac-sim-4.2.0/exts/omni.isaac.ros2_bridge/humble/lib
```
You also probably need to run this in the same terminal too:
```bash
export FASTRTPS_DEFAULT_PROFILES_FILE=/home/farjadnm/IsaacSim-ros_workspaces/humble_ws/fastdds.xml
```

---
Scene generation with SceneBlox:

Made different rules by editing and making usd files and then using this script:
```bash
./python.sh tools/scene_blox/src/scene_blox/rules_builder.py omniverse://localhost/Projects/multi_room/room_assets/wall_rules.usd /home/farjadnm/isaac_multi_robot/parameters/rules_wall_new.yaml 5
```
then combined them with this script:
```bash
./python.sh tools/scene_blox/src/scene_blox/rules_combiner.py /home/farjadnm/isaac_multi_robot/parameters/combined_rules_new.yaml --config_files /home/farjadnm/isaac_multi_robot/parameters/rules_cross_new.yaml /home/farjadnm/isaac_multi_robot/parameters/rules_corridor_new.yaml /home/farjadnm/isaac_multi_robot/parameters/rules_corner_new.yaml /home/farjadnm/isaac_multi_robot/parameters/rules_dead_end_new.yaml /home/farjadnm/isaac_multi_robot/parameters/rules_wall_new.yaml
```
Finally, run this script to generate the environments.
```bash
./python.sh tools/scene_blox/src/scene_blox/generate_scene.py \
    omniverse://localhost/Projects/multi_room/rooms \
    --grid_config /home/farjadnm/isaac_multi_robot/parameters/combined_rules_new.yaml \
    --generation_config /home/farjadnm/isaac_multi_robot/parameters/multi_room/generation.yaml \
    --rows 9 --cols 9 --variants 5 \
    --constraints_config /home/farjadnm/isaac_multi_robot/parameters/multi_room/constraints.yaml \
    --collisions
```