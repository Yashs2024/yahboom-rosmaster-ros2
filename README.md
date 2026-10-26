# yahboom_rosmaster #
Yahboom Rosmaster X3 — ROS 2 Simulation and Mecanum Control
A ROS 2 development project for the Yahboom Rosmaster X3 platform, focused on the robot description, simulation workflow, mecanum-drive command interface, and custom action-server integration.
Project status: The simulation and drive-control workflow has been explored in the development workspace. Integration of the custom TimedRotation action server is still in progress; this README does not claim that feature is complete.

Overview
The repository brings together ROS 2 packages for the robot description, bringup, Gazebo simulation, message/action definitions, and related control components.
Project goals
- Load and inspect the robot description.
- Run the robot in the available Gazebo simulation workflow.
- Send velocity commands to the mecanum-drive controller.
- Define a custom TimedRotation action and integrate an action server.
- Build a maintainable ROS 2 workspace with clearly separated packages.
Technology stack
- ROS 2 Jazzy
- URDF/Xacro
- RViz 2
- Gazebo / Gazebo Sim
- ros2_control
- geometry_msgs
- rclcpp and rclcpp_action
- Custom ROS 2 interface: TimedRotation.action
Package overview
The development workspace has included these packages/directories:
- yahboom_rosmaster_description — robot description and visualization resources
- yahboom_rosmaster_bringup — launch and bringup scripts
- yahboom_rosmaster_gazebo — Gazebo-related resources
- yahboom_rosmaster_messages — custom action/message interfaces
- yahboom_rosmaster_system_tests — system-test and action-server development
- yahboom_rosmaster — robot-specific package/resources
- macanum_drive_controller — controller-related package directory (the directory name in the workspace uses this spelling)
Use colcon list after cloning to confirm the packages present in the current revision.
Requirements
- A ROS 2 Jazzy installation
- colcon and ROS 2 development tools
- Gazebo and RViz packages required by the launch files
- Dependencies declared by the packages in this repository
Clone and build
Clone into the src directory of a ROS 2 workspace:
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
git clone https://github.com/Yashs2024/yahboom-rosmaster-ros2.git yahboom_rosmaster
Build from the workspace root:
cd ~/ros2_ws
source /opt/ros/jazzy/setup.bash
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install
source install/setup.bash
If rosdep cannot resolve a dependency, check the dependency's package name and upstream installation instructions. Some simulation packages may require additional Gazebo integration packages.
Start the Gazebo workflow
The development workspace used this bringup script:
cd ~/ros2_ws
source /opt/ros/jazzy/setup.bash
source install/setup.bash
bash src/yahboom_rosmaster/yahboom_rosmaster_bringup/scripts/rosmaster_x3_gazebo.sh
If the script path differs in your checkout, locate the script first:
find ~/ros2_ws/src -name 'rosmaster_x3_gazebo.sh'
Follow any prerequisites or arguments documented inside the script. Launch names and simulator requirements may change as the project evolves.
Mecanum-drive command interface
The drive controller in the development setup subscribes to:
/mecanum_drive_controller/cmd_vel
Message type:
geometry_msgs/msg/TwistStamped
For a mecanum base, twist.linear.x typically represents forward/backward velocity, twist.linear.y represents lateral velocity, and twist.angular.z represents yaw rate. Confirm the controller configuration and sign conventions before testing movement.

Inspect the topic and type:
ros2 topic info /mecanum_drive_controller/cmd_vel
Example: publish a small lateral command for a short, controlled test while the simulation is running:
ros2 topic pub --rate 10 /mecanum_drive_controller/cmd_vel geometry_msgs/msg/TwistStamped "{header: {stamp: {sec: 0, nanosec: 0}, frame_id: ''}, twist: {linear: {x: 0.0, y: 0.1, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}}"
Stop publishing with Ctrl+C. Use conservative speeds and ensure the robot is in a safe simulation state before trying other commands.
Custom TimedRotation action
The repository's custom action definition is:
yahboom_rosmaster_messages/action/TimedRotation.action
Its interface package is yahboom_rosmaster_messages. The action-server integration lives in yahboom_rosmaster_system_tests and is under development.

After the action server has been built and launched successfully, inspect available actions with:
ros2 action list -t
Do not assume the action server is available merely because the interface package builds. The server executable must also compile, be launched, and advertise its action endpoint. Add a verified example ros2 action send_goal command here when the final action definition and server are confirmed.
Useful debugging commands
# List all packages in the workspace
cd ~/ros2_ws && colcon list

# Rebuild the system tests and their dependencies
cd ~/ros2_ws
source /opt/ros/jazzy/setup.bash
colcon build --packages-up-to yahboom_rosmaster_system_tests --symlink-install

# Inspect active nodes, topics, and action interfaces
ros2 node list
ros2 topic list -t
ros2 action list -t

# Inspect active controllers (when ros2_control is running)
ros2 control list_controllers
If your workspace contains overlapping packages from /opt/ros/jazzy and your local src directory, read any colcon override warning carefully and make sure the intended overlay is sourced.
Development status
Area	Status
Robot description / package structure	Present in the development workspace
Gazebo bringup workflow	Script available and used during development; validate on a clean install
Mecanum-drive topic	Identified as /mecanum_drive_controller/cmd_vel (TwistStamped)
TimedRotation.action interface	Definition and interface package added to the workspace
TimedRotation action-server executable	In progress; verify compile and runtime before marking complete
Physical robot validation	Not claimed here


Update this table as the implementation and testing progress.
Roadmap
- Finish and verify the TimedRotation action-server build.
- Document a verified action goal and result example.
- Add reproducible launch instructions for a clean Jazzy workspace.
- Document controller configuration and mecanum-drive sign conventions.
- Add Gazebo/RViz screenshots, a short demo video, and test results.
Notes on third-party assets
This repository may include robot meshes, simulation models, or other upstream resources. Review their license and attribution requirements before redistributing them publicly.
Author
Yash Surve
- GitHub: @Yashs2024
- Project repository: yahboom-rosmaster-ros2
For bug reports, include the ROS 2 distribution, the launch command, the relevant package name, and the terminal output from the failing command.
