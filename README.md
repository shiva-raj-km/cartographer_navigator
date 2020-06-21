# cartographer_navigator
Cartographer with ROS integration is used for real time SLAM
## Prerequisities
Cartographer with ROS Building & installation. [Follow this](https://google-cartographer-ros.readthedocs.io/en/latest/compilation.html#building-installation)

``` bash
# Update & upgrade the system
sudo apt-get update
sudo apt-get install -y python-wstool python-rosdep ninja-build

mkdir cartographer_navigator # Workspace
cd cartographer_navigator

wstool init src
wstool merge -t src https://raw.githubusercontent.com/googlecartographer/cartographer_ros/master/cartographer_ros.rosinstall
wstool update -t src
src/cartographer/scripts/install_proto3.sh
# The command ‘sudo rosdep init’ will print an error if you have already executed it since installing ROS. This error can be ignored.
sudo rosdep init
rosdep update

rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
# Build and install
catkin_make_isolated --install --use-ninja
# This creates a cartographer_ros packages and dependencies required.
```
## Setup
``` bash
git clone https://github.com/shiva-raj-km/cartographer_navigator.git
# Cut & copy the cartographer_navigator to your workspace.
catkin_make_isolated --install --use-ninja # build the workspace once again
# Copy the params folder in cartographer_navigator to install_isolated/share/cartographer_navigator
```
## Usage
```
# Refer to Smart_Navigator/navigator_bringup and launch robot_standalone.launch & 3dsensor.launch files in two separate terminals
# New terminal 
source install_isolated/setup.bash
roslaunch cartographer_navigator cartographer_demo.launch # launches cartographer_node & move_base node
# This for initial step explore the environment using teleopration refer navigator_bringup
# To vizuliaze the map, refer to navigator_bringup and run view_navigation.launch
# Save the map when it is completed using ros_service
rosservice call /write_state "filename: '~/<path>/name.pbstream'"
# The map is saved in serialiazed format. Map_server can also be used but some tweak in source files is required.


# Since the map is saved, once again it is not necessary to build the map again, so run cartographer in pure_localization mode.
# Now instead of cartographer_demo.launch, run 
roslaunch cartographer_navigator cartographer_localization.launch load_state_filename:=~/<path>/name.pbstream


