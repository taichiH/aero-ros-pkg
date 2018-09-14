# aero-ros-pkg

[![Build Status](https://travis-ci.org/seed-solutions/aero-ros-pkg.svg?branch=master)](https://travis-ci.org/seed-solutions/aero-ros-pkg)

## Pre-requirement

### Install ROS

[Install ROS kinetic]( http://wiki.ros.org/kinetic/Installation/Ubuntu ) [Recommended]

or

[Install ROS indigo]( http://wiki.ros.org/indigo/Installation/Ubuntu )

```
sudo apt-get install ros-${ROS_DISTRO}-desktop-full
```

### Setup catkin workspace

```
sudo apt-get install python-wstool python-catkin-tools
mkdir -p ~/ros/${ROS_DISTRO}
cd ~/ros/${ROS_DISTRO}
wstool init src
catkin init
catkin build
source ~/ros/${ROS_DISTRO}/devel/setup.bash
```

### source setup.bash in ~/.bashrc

Please add the following line to ~/.bashrc
```
source ~/ros/indigo/devel/setup.bash
```

## Install

### Clone repository and Setup ROS binary packages

```
cd ~/ros/${ROS_DISTRO}/src
wstool set aero-ros-pkg https://github.com/seed-solutions/aero-ros-pkg.git --git
wstool up aero-ros-pkg
cd ~/ros/${ROS_DISTRO}
rosdep install -y -r --from-paths src --ignore-src
```
You may need ``sudo rosdep init`` and ``rosdep update`` before
``rosdep install``. Please follow the terminal message.

### Build packge

#### Build aero_description

```
cd aero-ros-pkg
catkin build aero_description
soure ~/.bashrc
```

#### Setup aero_startup

In the initial stage, `aero_startup` is not ros package, it has no `CMakeLists.txt`.
`CMakeLists.txt` and some controller modules are generated by `aero_description/setup.sh`.
`setup.sh` requires robot directory as an argument e.g. `setup.sh typeF`.

```
roscd aero_description
./setup.sh typeF  # including catkin build aero_startup
source ~/.bashrc
```

If you need clean up `aero_startup`, please use `aero_description/clean.sh`.

## Control AERO

### Setup udev

T.B.D.

### Run controllers

```
roslaunch aero_startup aero_bringup.launch
```

### View Robot Model

```
rosrun rviz rviz
```

then add RobotModel

## Simulation

See [aero_gazebo](https://github.com/seed-solutions/aero-ros-pkg/blob/master/aero_gazebo/README.md)


## Samples

### Build Samples

```
catkin build aero_samples
```

### Run Minimum Sample

```
rosrun aero_samples minimum_sample_node
```

### Control from Command Line

#### Move Torso

```
rostopic pub /lifter_controller/command trajectory_msgs/JointTrajectory "header:
  seq: 0
  stamp:
    secs: 0
    nsecs: 0
  frame_id: ''
joint_names:
- 'ankle_joint'
- 'knee_joint'
points:
- positions: [0.5, -0.5]
  velocities: [0, 0]
  accelerations: [0, 0]
  effort: [0, 0]
  time_from_start: {secs: 3, nsecs: 0}"
```

#### Move hands
~~~
## Open
rosservice call /aero_hand_controller "{hand: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.HAND_RIGHT'), command: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.COMMAND_UNGRASP'), power: 0, time_sec: 0}"

## Grasp
rosservice call /aero_hand_controller "{hand: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.HAND_RIGHT'), command: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.COMMAND_GRASP'), power: 0, time_sec: 1.0}"

## Angle
rosservice call /aero_hand_controller "{hand: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.HAND_RIGHT'), command: $(python -c 'import aero_startup.srv; print aero_startup.srv.HandControlRequest.COMMAND_GRASP_ANGLE'), power: 0, time_sec: 1.0, larm_angle: 0.5, rarm_angle: 0.5}"
~~~

### Writing Codes

see `aero_samples` and `aero_std` documentation

### Build check and test by Docker

install docker https://docs.docker.com/install/linux/docker-ce/ubuntu/

build docker image under aero-ros-pkg directory
~~~
docker build -f Dockerfile.kinetic --tag ros:aero_kinetic --build-arg RUN_TESTS=true .
~~~
### PS3 Joy Stick Teleop

Launch move base
~~~
roslaunch aero_startup wheel_with_static_map.launch
~~~

![ps4_1](https://user-images.githubusercontent.com/22497720/45566446-a9013f00-b891-11e8-99fa-3c03e266df1c.jpg)
![ps4_2](https://user-images.githubusercontent.com/22497720/45566454-abfc2f80-b891-11e8-95a0-c5083aed59c4.jpg)

## Usage

Teleoperate aero using ps3 controller!

### Basic Mode
|Button|Function                                                            |
|:-----|:------------------------------------------------------------------ |
|0     |Change to Joint Mode                                                |
|1     |Forward/Backward move base (with 10), Up/Down lifter (with 11)      |
|2     |Control forward/backward driving                                    |
|3     |Change to Basic Mode                                                |
|4     |Not used                                                            |
|5     |Not used                                                            |
|6     |Arm tuck                                                            |
|7     |Not used                                                            |
|8     |Not used                                                            |
|9     |Not used                                                            |
|10    |Move Teleop                                                         |
|11    |Lifter Teleop                                                       |
|12    |Not used                                                            |
|13    |Close Right Hand                                                    |
|14    |Open Hand                                                           |
|15    |Close Left Hand                                                     |
|16    |Reset Pose                                                          |

### Joint Mode
|Button|Function                        |
|:-----|:-------------------------------|
|0     |Change to Joint Mode            |
|1     |Control Joint Angle             |
|2     |Not userd                       |
|3     |Change to Basic Mode            |
|4     |Control Joint Angle             |
|5     |Control Joint Angle             |
|6     |Control Joint Angle             |
|7     |Control Joint Angle             |
|8     |Control Left Forearm            |
|9     |Control Right Forearm           |
|10    |Control Left Upper Arm          |
|11    |Control Right Upper Arm         |
|12    |Control Right Upper Arm         |
|13    |Control Right Wrist             |
|14    |Control Torso                   |
|15    |Control Left Wrist              |
|16    |Pair/unpair with robot          |