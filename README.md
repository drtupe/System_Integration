# System Integration Project

## Description:

This project is to implement a self-driving car system with ROS to run in both simulator and Udacity’s self-driving car - Carla.

## Architecture:

Implemented ROS nodes for core functionality of the autonomous vehicle system:

* waypoints following,
* control,
* traffic light detection.

<img src = "result/vid1.gif">

<img src = "result/traffic_stop.gif">

## ROS Nodes:

* waypoint_updater: This node subscribes to the /base_waypoints, /current_pose, /obstacle_waypoint, and /traffic_waypoint topics, and publish a list of waypoints ahead of the car with target velocities to the /final_waypoints topic.

* tl_detector: This node takes in data from the /image_color, /current_pose, and /base_waypoints topics and publishes the locations to stop for red traffic lights to the /traffic_waypoint topic

* dbw_node: subscribes to the /current_velocity topic along with the /twist_cmd topic to receive target linear and angular velocities. Additionally, this node subscribes to /vehicle/dbw_enabled, which indicates if the car is under dbw or driver control. This node publishes throttle, brake, and steering commands to the /vehicle/throttle_cmd, /vehicle/brake_cmd, and /vehicle/steering_cmd topics.

## How to launch:

There are two tracks in simulator.

### Highway track:

roslaunch launch/styx.launch

### Test site:

For this we first have to modify waypoints_loader/launch/waypoint_loader.launch to:

'<param name="path" value="$(find styx)../../../data/churchlot_with_cars.csv"/>'

and then launch:

roslaunch launch/site.launch

## Working Environment:

In the Udacity workspace simulator, I had experienced obvious lagging when the camera is on. This seems to be due to workspace resource constraints. The solution was to set up a local simulator environment. There are several ways to go. One can set up a native Linux installation with both, simulator and ROS environment, running on the same machine. But luckly I had an option as I was already using Ubuntu 18.04 on my personal system, I was able to setup and the simulator on my system. Hence, eliminating the lag.

## General Tweaks:

For me, it was necessary to change some timing relevant variables in the original codebase. First, the variable LOOKAHEAD_WPS must be reduced below a value of 200. A Value of 100 was satisfactory for not running into lag issues while computing enough waypoints ahead. In addition, I had to change LOOP_RATE up to 50 Hz to increase the performance. In the original Udacity code the PurePursuit::calcTwist() function calculates new values in dependence of the PurePursuit::verifyFollowing() function. To avoid strange effects of "lane wandering" in combination with a LOOP_RATE value of 30 Hz we altered it to basically update at every step regardless of the lane following verification.


Please use **one** of the two installation options, either native **or** docker installation.

### Native Installation

* Be sure that your workstation is running Ubuntu 16.04 Xenial Xerus or Ubuntu 14.04 Trusty Tahir. [Ubuntu downloads can be found here](https://www.ubuntu.com/download/desktop).
* If using a Virtual Machine to install Ubuntu, use the following configuration as minimum:
  * 2 CPU
  * 2 GB system memory
  * 25 GB of free hard drive space

  The Udacity provided virtual machine has ROS and Dataspeed DBW already installed, so you can skip the next two steps if you are using this.

* Follow these instructions to install ROS
  * [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) if you have Ubuntu 16.04.
  * [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) if you have Ubuntu 14.04.
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Docker Installation
[Install Docker](https://docs.docker.com/engine/installation/)

Build the docker container
```bash
docker build . -t capstone
```

Run the docker file
```bash
docker run -p 4567:4567 -v $PWD:/capstone -v /tmp/log:/root/.ros/ --rm -it capstone
```

### Port Forwarding
To set up port forwarding, please refer to the "uWebSocketIO Starter Guide" found in the classroom (see Extended Kalman Filter Project lesson).

### Usage

1. Clone the project repository
```bash
git clone https://github.com/udacity/CarND-Capstone.git
```

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
roslaunch launch/styx.launch
```
4. Run the simulator

### Real world testing
1. Download [training bag](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/traffic_light_bag_file.zip) that was recorded on the Udacity self-driving car.
2. Unzip the file
```bash
unzip traffic_light_bag_file.zip
```
3. Play the bag file
```bash
rosbag play -l traffic_light_bag_file/traffic_light_training.bag
```
4. Launch your project in site mode
```bash
cd CarND-Capstone/ros
roslaunch launch/site.launch
```
5. Confirm that traffic light detection works on real life images

### Other library/driver information
Outside of `requirements.txt`, here is information on other driver/library versions used in the simulator and Carla:

Specific to these libraries, the simulator grader and Carla use the following:

|        | Simulator | Carla  |
| :-----------: |:-------------:| :-----:|
| Nvidia driver | 384.130 | 384.130 |
| CUDA | 8.0.61 | 8.0.61 |
| cuDNN | 6.0.21 | 6.0.21 |
| TensorRT | N/A | N/A |
| OpenCV | 3.2.0-dev | 2.4.8 |
| OpenMP | N/A | N/A |

We are working on a fix to line up the OpenCV versions between the two.
