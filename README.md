# Hybrid Workspace

A ROS workspace integrating multiple packages for robotic manipulation and perception research, including Franka manipulators, tentacle control, dynamixel motors, and vision systems.

## Overview

This workspace contains the following key packages:

- **tentacle_control** - Tentacle manipulation control
- **dynamixel_control** - Dynamixel servo motor control
- **franka_*** - Franka Emika robot packages and controllers
- **SIMPLe** - Soft material interaction learning and perception
- **ILoSA** - Interactive learning for object state adaptation
- **gaussian_process_transportation** - GP-based motion planning
- **apriltag & apriltag_ros** - AprilTag detection and tracking
- **realsense-ros** - Intel RealSense camera integration
- **easy_handeye** - Hand-eye calibration utilities
- **jsk_common** - JSK common utilities
- **panda-ros-py** - Panda robot Python interface

## Prerequisites

- **ROS 2 (Humble)** or **ROS 1 (Noetic)** - Check individual packages for compatibility
- **Ubuntu 22.04 LTS** (for ROS 2 Humble) or **Ubuntu 20.04 LTS** (for ROS 1 Noetic)
- **wstool** - for managing workspace sources
- **rosdep** - for installing system dependencies
- **catkin_tools** - recommended build tool

### Install Prerequisites

```bash
# Install ROS 2 Humble (if not already installed)
# See: https://docs.ros.org/en/humble/Installation.html

# Install wstool and rosdep
sudo apt install python3-wstool python3-rosdep2 python3-catkin-tools

# Update rosdep
sudo rosdep init
rosdep update
```

## Setup Instructions

### 1. Clone the Workspace Repository

```bash
git clone <this-repo-url> ~/hybrid_ws
cd ~/hybrid_ws
```

### 2. Initialize ROS Sources

```bash
wstool init src .rosinstall
wstool update src
```

This command reads the `.rosinstall` file and clones all required packages into the `src/` directory.

### 3. Install System Dependencies

```bash
rosdep install --from-paths src --ignore-src -r -y
```

### 4. Build the Workspace

```bash
# Source ROS setup (adjust for your ROS distro)
source /opt/ros/humble/setup.bash

# Build all packages
catkin build
```

Or with specific packages:
```bash
catkin build tentacle_control dynamixel_control
```

### 5. Source the Workspace

```bash
source devel/setup.bash
```

Add this to your `~/.bashrc` to auto-source on new terminals:
```bash
echo "source ~/hybrid_ws/devel/setup.bash" >> ~/.bashrc
```

## Troubleshooting

### Missing Dependencies
If `rosdep install` fails to resolve dependencies, check that:
- You've run `rosdep update`
- Individual package `package.xml` files are present and valid

### Build Failures
- Try building individual packages: `catkin build --verbose <package-name>`
- Check package-specific READMEs in `src/<package>/`
- Some packages may have additional setup requirements (e.g., Franka requires libfranka)

### Git Clone Failures
If `wstool update` fails for certain packages:
```bash
# Manually clone problematic packages
cd src
git clone <package-url> <package-name>
cd ..
catkin build
```

## Updating Packages

To update all packages to their latest versions:

```bash
wstool update src
```

To update a specific package:
```bash
cd src/<package-name>
git pull
cd ../..
catkin build
```

## Adding New Packages

To add a new package to the workspace:

1. Edit `.rosinstall` and add an entry:
```yaml
- git:
    local-name: src/new_package
    uri: https://github.com/user/new_package.git
    version: main
```

2. Run wstool to fetch it:
```bash
wstool update src
```

3. Rebuild:
```bash
catkin build
```

## Contributing

When making changes:

1. Each package maintains its own git history
2. Commit changes to individual package repositories
3. Update `.rosinstall` if adding/removing packages
4. Ensure your changes don't break the build: `catkin build`

## License

See individual package repositories for license information.

## References

- [ROS 2 Documentation](https://docs.ros.org/)
- [catkin Documentation](http://docs.ros.org/en/api/catkin/html/)
- [wstool Documentation](http://docs.ros.org/en/independent/api/wstool/html/)
