# pioneer_mrs [![Build Status](https://travis-ci.com/hanzheteng/pioneer_mrs.svg?branch=master)](https://travis-ci.com/hanzheteng/pioneer_mrs)
## 1. Introduction
A ROS package for Pioneer 3-AT Multi-Robot Systems.

This is a flexible experimental platform for multi-robot systems (MRS). Should there be any problem, please feel free to contact maintainer [@hanzheteng](https://github.com/hanzheteng) or open a new issue in this repository.

<table>
  <tr>
    <th> ROS package </th>
    <th> Description (<I>Features</I>) </th>
    <th> Platform </th>
  </tr>
  <tr>
    <td> <a href="https://github.com/hanzheteng/pioneer_mrs">
      pioneer_mrs </a> </td>
    <td> <ul>
      <li> Support scientific computing library (numpy, scipy, etc.) for your algorithms </li>
      <li> Support two kinds of localization methods (odometry, VICON) </li>
      <li> Support two kinds of robot-level simulators (MobileSim, Gazebo) </li>
      <li> Visualization is under developing </li> </ul> </td>
    <td> <ul>
      <li> <a href="http://robots.ros.org/pioneer-3-at/"> Pioneer 3-AT </a> </li>
      <li> Ubuntu 16.04.3 LTS </li>
      <li> <a href="http://www.ros.org/about-ros/"> Robot Operating System </a> (kinetic) </li>
      <li> <a href="http://wiki.ros.org/ROSARIA"> ROSARIA </a>  package (git-commit-aa8d5f7) </li>
      <li> <a href="http://robots.mobilerobots.com/wiki/ARIA"> ARIA </a> lib (2.9.1a-ubuntu16-i386) </li> </ul> </td>
  </tr>
</table>

## 2. Usage
### 2.1 Running on Robots
#### System Configuration
Before using this package to drive your robots, you may configure your platform following the steps:
1. All machines (5 robots + 1 laptop) are installed Ubuntu 16.04 and ROS Kinetic
2. All robots (onboard computer, actually) are installed and *catkin_make*-ed ARIA lib and ROSARIA package
3. All machines need a static IP address and are able to automatically connect to your local WiFi
4. Write the IP and hostname information into */etc/hosts* file on all the machines
5. Your commander (laptop) is able to SSH to all robots via public key (instead of password)
6. Onboard computers are able to get access to the microcontroller via serial post (add user to *dialout* group)

***For detailed information regarding how to set up the whole system, please refer to [the wiki page](https://github.com/hanzheteng/pioneer_mrs/wiki).***

Moreover, there are two suggestions which is not necessary for this system but may be useful for your development.
- You may install a [teamviewer](https://www.teamviewer.us/) software on your laptop and robots so that you can remote login into your onboard computers.
- For IDE, I recommend [RoboWare Studio](http://www.roboware.me/#/home), which is especially designed for ROS developers. Another alternative is [KDevelop](https://www.kdevelop.org/), which is a cross-platform IDE.

#### Package Dependencies
Before `git clone` this repository, you need to install [ARIA](http://robots.mobilerobots.com/wiki/ARIA) lib and `git clone` [ROSARIA](https://github.com/amor-ros-pkg/rosaria) package first. After that, `catkin_make` your ROSARIA package with ARIA library. Then, you can clone this repository by `git clone https://github.com/hanzheteng/pioneer_mrs.git` and `catkin_make` this package in your workspace.

Notice:
1. You must `catkin_make` the ROSARIA package first before you clone and make this package, otherwise it may report errors.
2. Sometimes you need to `catkin_make pioneer_mrs_generate_messages` first before `catkin_make` this package, because the compiler may not be able to find the header file of our messages.

Optional package:
- If you want to use [VICON](https://www.vicon.com/) motion capture system, you also need to `git clone` and `catkin_make` [vicon_bridge](http://wiki.ros.org/vicon_bridge) package.

#### Sample Usage
Basically there are several steps:
1. launch nodes on all five robots </br>
`roslaunch pioneer_mrs multi-robot.launch`
2. launch nodes on commander </br>
`roslaunch pioneer_mrs commander.launch`

If using Vicon:
1. launch nodes on all five robots </br>
`roslaunch pioneer_mrs multi-robot.launch pose:=vicon`
2. launch nodes on commander </br>
`roslaunch pioneer_mrs commander.launch`

### 2.2 Running on MobileSim Simulator
You also need [ROSARIA](https://github.com/amor-ros-pkg/rosaria) package to connect to MobileSim simulator. Moreover, you need to install [MobileSim](http://robots.mobilerobots.com/wiki/MobileSim) on your laptop.

Basically there are several steps:
1. run a MobileSim simulator and spawn five robots </br>
`roslaunch pioneer_mrs mobilesim.launch`
2. launch nodes on all five robots </br>
`roslaunch pioneer_mrs multi-robot.launch machine:=mobilesim`
3. launch nodes on commander </br>
`roslaunch pioneer_mrs commander.launch`

### 2.3 Running on Gazebo Simulator
You do not need ROSARIA package here, since Gazebo will take charge of publishing `odom` information and dealing with `cmd_vel` commands. Gazebo is installed by default along with ROS if choosing `desktop-full` install. Moreover, for simulations of Pioneer 3-AT robots, you also need models and URDF files supplied by Mobile Robots Inc. by `git clone` [amr-ros-config](https://github.com/MobileRobots/amr-ros-config) meta-package.

Basically there are several steps:
1. run a Gazebo simulator and spawn five robots </br>
`roslaunch pioneer_mrs gazebo.launch`
2. launch nodes on all five robots </br>
`roslaunch pioneer_mrs multi-robot.launch pose:=gazebo machine:=gazebo`
3. launch nodes on commander </br>
`roslaunch pioneer_mrs commander.launch`

Notice: On real robots, movement commands only execute for 600ms because of WatchDog timeout mechanism; but in gazebo simulator, robots will keep moving towards the last direction. You may press space bar (stop command) to stop it.

### 2.4 Single Robot Recoverary
Optional launch file (e.g. robot1):
- launch one robot </br>
`roslaunch pioneer_mrs single-robot.launch hostname:=robot1`
- launch one robot with Vicon </br>
`roslaunch pioneer_mrs single-robot.launch hostname:=robot1 pose:=vicon`
- launch one robot on MobileSim simulator </br>
`roslaunch pioneer_mrs single-robot.launch hostname:=robot1 machine:=mobilesim`
- launch one robot on Gazebo simulator </br>
`roslaunch pioneer_mrs single-robot.launch hostname:=robot1 pose:=gazebo machine:=gazebo`

## 3. Robot Operating System
### 3.1 ROS Node Info
Note: `robot#` represents any robot label from 1 to 5.
<table>
  <tr>
    <th> Node Name </th>
    <th> Sub Topic / Srv Client </th>
    <th> Pub Topic / Srv Server </th>
    <th> Description </th>
  </tr>
  <tr>
    <td> /vicon
      <br> (<a href="http://wiki.ros.org/vicon_bridge">vicon_bridge </a> package) </td>
    <td> --- </td>
    <td> /vicon/robot#/robot# </td>
    <td> publish translation and rotation info </td>
  </tr>
  <tr>
    <td> /robot#/RosAria
      <br> (<a href="http://wiki.ros.org/ROSARIA">rosaria</a> package) </td>
    <td> /robot#/RosAria/cmd_vel </td>
    <td> /robot#/RosAria/pose </td>
    <td> SDK bridge to communicate with microcontrollers </td>
  </tr>
  <tr>
    <td> /robot#/pioneer_server </td>
    <td> /robot#/cmd_vel_hp
      <br> /robot#/RosAria/pose
      <br> /robot#/gazebo/odom
      <br> /vicon/robot#/robot# </td>
    <td> /robot#/RosAria/cmd_vel
      <br> /robot#/get_pose </td>
    <td> pose server for pioneer robots </td>
  </tr>
  <tr>
    <td> /robot#/algorithm_node </td>
    <td> /robot#/comm_state
      <br> /robot#/get_pose </td>
    <td> /robot#/cmd_vel_hp
      <br> /robot#/Formation </td>
    <td> execute pre-designed algorithm </td>
  </tr>
  <tr>
    <td> /commander/teleop </td>
    <td> /robot#/Formation </td>
    <td> /robot#/mission_state
      <br> /robot#/cmd_vel_hp </td>
    <td> commander & teleoperation node </td>
  </tr>
  <tr>
    <td> /commander/comm_state_cmd </td>
    <td> --- </td>
    <td> /robot#/comm_state </td>
    <td> publish communication graph info </td>
  </tr>
</table>

### 3.2 Message Definition
<table>
  <tr>
    <th width=20%> Topic Name </th>
    <th width=45%> Format </th>
    <th width=20%> Message File </th>
    <th width=15%> Description </th>
  </tr>
  <tr>
    <td> vicon/robot#/robot# </td>
    <td> std_msgs/Header header
      <br> string child_frame_id
      <br> geometry_msgs/Transform transform </td>
    <td> <a href="http://docs.ros.org/api/geometry_msgs/html/msg/TransformStamped.html"> TransformStamped.msg </a> </td>
    <td> see wiki </td>
  </tr>
  <tr>
    <td> /robot#/RosAria/pose </td>
    <td> std_msgs/Header header
      <br> string child_frame_id
      <br> geometry_msgs/PoseWithCovariance pose
      <br> geometry_msgs/TwistWithCovariance twist </td>
    <td> <a href="http://docs.ros.org/api/nav_msgs/html/msg/Odometry.html"> Odometry.msg </a> </td>
    <td> see wiki </td>
  </tr>
  <tr>
    <td> /robot#/RosAria/cmd_vel </td>
    <td> geometry_msgs/Vector3 linear
      <br> geometry_msgs/Vector3 angular </td>
    <td> <a href="http://docs.ros.org/api/geometry_msgs/html/msg/Twist.html"> Twist.msg </a> </td>
    <td> see wiki </td>
  </tr>
  <tr>
    <td> /robot#/cmd_vel_hp </td>
    <td> float64 x
      <br> float64 y
      <br> float64 z </td>
    <td> <a href="http://docs.ros.org/api/geometry_msgs/html/msg/Vector3.html"> Vector3.msg </a> </td>
    <td> only use 2D velocity (z=0) </td>
  </tr>
  <tr>
    <td> /robot#/comm_state </td>
    <td> bool[5] state </td>
    <td> CommunicationState.msg </td>
    <td> one row of the communication graph </td>
  </tr>
</table>

### 3.3 Service Definition
<table>
  <tr>
    <th> Graph Resource Name </th>
    <th> Format </th>
    <th> Srv File Name </th>
    <th> Description </th>
  </tr>
  <tr>
    <td> /robot#/get_pose </td>
    <td> ---
      <br> bool success
      <br> float64 x
      <br> float64 y
      <br> float64 theta </td>
    <td> Pose2D.srv </td>
    <td> same format to geometry_msgs::Pose2D.msg </td>
  </tr>
</table>


### 3.4 Action Definition
<table>
  <tr>
    <th> Graph Resource Name </th>
    <th> Format </th>
    <th> Action File Name </th>
    <th> Description </th>
  </tr>
  <tr>
    <td> /robot#/Formation </td>
    <td> uint32 order
      <br> ---
      <br> string name
      <br> float32 x
      <br> float32 y
      <br> ---
      <br> string name
      <br> float32 x
      <br> float32 y </td>
    <td> Formation.action </td>
    <td> set a goal of total steps
      <br> get feedbacks of positions in each step </td>
  </tr>
</table>

## 4. Update Log
- V1.0 (Jan 4, 2018) Basic multi-robot control framework
- V1.1 (Jan 15, 2018) The MRS control framework works well with new features, but the algorithm node need to be updated.
- V1.1.4 (Feb 5, 2018) The algorithm node works well on both MobileSim simulator and empirical experiments. The Gazebo simulator still has problems to be resolved.
- V1.2 (Feb 18, 2018) The multi-robot system works well on MobileSim simulator, Gazebo simulator and empirical experiments (localization done by either odometry, Vicon system, or Gazebo simulator.)
- V1.3 (Mar 17, 2018) Add offset adjestment to the existing formation based on optimal-point algorithm; Launch files may have some problems because of improper launch sequence. (Senior Design project done in this version)
- V1.4 (Apr 4, 2018) Make this framework more concise and efficient. (Rewrite communication mechanism by client-server RPC; rewrite algorithm node by python and support scientific computing library; apply actionlib for desired missions; add dynamic reconfigure)

## 5. Debug FAQ
Most of the time, you can use `roswtf` command to diagnose your problem.
1. If `roslaunch` or `rosrun` cannot autocomplete by `Tab` key, first check if your search path covers the package by `env | grep ROS`. If it already exist in the path, try `rospack profile` or reboot your system.
2. If prompt `process has died, exit code -11`, you may access data in the code which was not initialized.
3. If prompt `The following roslaunch remote processes failed to register: * xxxx (timeout 10.0s)`, your network may have a problem, including hosts file, ROS_MASTER_URI, env loader, etc. (e.g. the hostname of your laptop was not exactly the same as you set in /etc/hosts file)
4. Launch sequence problem: service servers need to start earlier than clients. (C++ only, python solved this problem by `wait_for_service` function)
5. If prompt `cannot launch node of type [some python file]`, you need to add execute permission to that python file.
6. Getting input and printing output in the same command line window will result in a kind of shifting of output lines.
7. If prompt `Unable to establish ssh connection to [user@robot:22]: Server u'robot' not found in known_hosts`, make sure you do ssh connection to your robot via RSA key. Then just remove the `~/.ssh/known_hosts` file and ssh again to your robot.
8. If Gazebo crashed with `segmentation fault` right after `waitForService: Service [/gazebo/set_physics_properties] has not been advertised, waiting... `, turn off `3D Accleration` in VirtualBox display settings.
