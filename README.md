# ROS Workspace to use and control our soft-rigid hybrid composed of a Franka FR3 and a tendon-actuated soft arm

## Overview 

This workspace contains the following key packages:

#### Robot learning and control packages
- [**dynamixel_control**](https://github.com/MRamirez25/dynamixel_control) - Used to communicate and control dynamixel servos
which in our case actuate the soft arm's tendons. Also contains the main script for 
experiments and collected data, and re-targets the source and target keypoints as needed for the hybrid robot to use the transportation algorithm from the package directly below. Go to the [repository](https://github.com/MRamirez25/dynamixel_control) for more details.
- [**gaussian_process_transportation**](https://github.com/franzesegiovanni/gaussian_process_transportation.git) - Provides the transportation algorithm we use to generalize to new task configurations, see [Franzese, Giovanni, et al. "Generalizable motion policies through keypoint parameterization and transportation maps." IEEE Transactions on Robotics (2025).](https://ieeexplore.ieee.org/abstract/document/11049008/)
- [**franka_human_friendly_controllers**](https://github.com/franzesegiovanni/franka_human_friendly_controllers.git) - Impedance controller for the Franka arm.
- [**panda-ros-py**](https://github.com/platonics-delft/panda-ros-py.git) - Franka robot Python interface.
- [**SIMPLe**](https://github.com/franzesegiovanni/SIMPLe.git) - Higher level controller providing attractors to the impedance controller to generate the desired trajectories, and contains scripts to record data from the kinesthetic demonstrations. 
- [**franka_ros**](https://github.com/frankaemika/franka_ros.git) - Franka Emika robot packages 
to communicate and move the rigid Franka arm.

#### Hardware and other helper packages
- [**apriltag**](https://github.com/AprilRobotics/apriltag.git) & [**apriltag_ros**](https://github.com/AprilRobotics/apriltag_ros.git) - AprilTag detection and tracking.
- [**realsense-ros**](https://github.com/IntelRealSense/realsense-ros.git) - Intel packages to use their RealSense cameras, in our case the d405 .
- [**easy_handeye**](https://github.com/IFL-CAMP/easy_handeye.git) - Hand-eye calibration package to find the camera's extrinsic parameters.

## Prerequisites

Note this setup has been built and tested only in Ubuntu 20.04.

- **ROS 1 (Noetic)** - Please follow their [installation instructions](https://wiki.ros.org/noetic/Installation/Ubuntu)

You will also need wstool and catkin-tools, if you don't have them
you can install them with the command below:

```bash
# Install required tools (wstool and catkin_tools are not included with ROS Noetic by default)
sudo apt install python3-wstool python3-catkin-tools
```

## Setup Instructions

### 1. Clone the Workspace Repository

```bash
git clone https://github.com/MRamirez25/hybrid_ws
cd ~/hybrid_ws
```

### 2. Initialize ROS Sources

```bash
wstool init src .rosinstall
wstool update
```

This command reads the `.rosinstall` file and clones all required packages into the `src/` directory.

Note that this will clone the ```realsense_ros``` package into your workspace, but the package requires some additional setup, so please follow [their instructions](https://github.com/IntelRealSense/realsense-ros.git).

### 3. Install System Dependencies

This will try to install some Python dependencies automatically. If you want to use a specific environment, make sure you activate it before running this.

```bash
rosdep install --from-paths src --ignore-src -r -y
```

Additionally, install Python packages from the requirements file:

```bash
pip install -r requirements.txt
```

This installs core packages like numpy, scipy, scikit-learn, and other utilities.

### 4. Build the Workspace

```bash
# Source ROS setup
source /opt/ros/noetic/setup.bash

# Build all packages
catkin build
```

### 5. Source the Workspace

```bash
source devel/setup.bash
```

## Using the code

1. First ensure you have vision system running, which is publishing images and has its extrinsics as a TF so keypoints can be found (can be with AprilTags, but also with DINO, etc.) and transformed to the world frame. We use the realsense d405, and calibrate with ```easy_hand_eye``` (provided in the installation steps).

2. Similarly ensure the dynamixel servos are connected, and the microcontroller (if applicable) is on, and that your computer detects the dynamixels.

3. You can then run the impedance controller from ```franka_human_friendly_controllers``` using the [instructions](https://github.com/franzesegiovanni/franka_human_friendly_controllers.git) on their repository.

4. Finally you can use ```main_tags_tentacle.py``` in the ```dynamixel_control``` package to:
    1. Detect and save source keypoints
    2. Record a kinesthetic demonstration demonstration
    3. Detect target keypoints
    4. Re-target the source and target keypoints, and transport the demonstration to find a new policy
    5. Execute the new trajectory
