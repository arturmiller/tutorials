ROS Noetic Tutorial
===================

This tutorial aims to provide an introduction to the Robotics Operating System (ROS) Noetic and its key concepts and features. The tutorial is designed for users who are new to ROS and want to learn how to use it to control robots and other devices.
Prerequisites

- Ubuntu 20.04
- Basic knowledge of Linux command line
- Basic knowledge of programming in Python

# 1. Installation and setup of ROS Noetic on Ubuntu 20.04

Before you can start using ROS, you'll need to install it on your system. The recommended way to install ROS Noetic on Ubuntu 20.04 is by using the ROS package repository. To add the repository to your system, open a terminal and run the following commands:

```python
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
sudo apt-get update
```

To install ROS Noetic, you can use the following command:

```bash
sudo apt-get install ros-noetic-desktop-full
```

Once the installation is complete, you'll need to initialize the ROS environment by running the following command:

```bash
source /opt/ros/noetic/setup.bash
```

You can add this command to your `.bashrc` file to make sure that the ROS environment is automatically initialized every time you open a terminal.
# 2. Understanding the ROS architecture and its core concepts

ROS is based on a distributed architecture, where multiple processes (called nodes) communicate with each other using topics, messages, services, and actions.

- **Nodes** are individual processes that perform a specific task, such as sensing or controlling a robot.
- **Topics** are channels for publishing and subscribing to data streams. Nodes can publish data to a topic, and other nodes can subscribe to that topic to receive the data.
- **Messages** are the data that is transmitted over topics. They are defined by message definition files, which specify the structure of the data.
- **Services** allow nodes to request a specific action or information from another node, and receive a response.
- **Actions** are similar to services, but they are used for more complex control tasks and involve feedback and goals.

# 3. Creating and building a ROS package

A ROS package is a collection of files that define a specific functionality, such as a driver for a specific sensor or a control algorithm. To create a new package, you can use the catkin_create_pkg command, like this:

```bash
catkin_create_pkg my_package std_msgs rospy
```

This command will create a new directory called `my_package` in the `src` directory of your catkin workspace, and it will also add the `std_msgs` and `rospy` packages as dependencies.

If you are using catkin_tools (which is the recommended way after ROS Kinetic) you can create a new package with the following command:

```bash
catkin create pkg my_package
```

This command will create a new package in the src directory of your catkin workspace, and it will also create a `CMakeLists.txt` and `package.xml` file

Once you have created your package, you can build it using the following command:

```bash
catkin build my_package
```

This command will compile and link all the packages in your workspace, including your new package.

You can also use the following command to build all packages within your workspace

```bash
catkin build
```

It is important to note that you need to be in the root of the catkin workspace to run this command, otherwise you will get an error.

After building the package, you can use the command

```bash
source devel/setup.bash
```

to make sure your new package is in the environment and can be used.

You can add this command to your `.bashrc` file to make sure that the package is automatically added to the environment every time you open a terminal.

# 4. Understanding and using the ROS command-line tools

ROS provides a number of command-line tools that can be used to interact with the running system and debug it. Some of the most commonly used tools are:

- **roscore**: This is the master process that runs on the same machine as the other nodes. It is responsible for maintaining a list of active nodes and topics, and it also provides other services such as parameter server and time synchronization.
- **rosnode**: This tool can be used to interact with individual nodes, such as listing all active nodes or getting information about a specific node.
- **rostopic**: This tool can be used to interact with topics, such as listing all active topics, displaying the data on a specific topic, or publishing data to a topic.
- **rosparam**: This tool can be used to interact with the parameter server, which is used to store and retrieve configuration parameters for the nodes.
- **rosservice**: This tool can be used to interact with services, such as listing all active services, calling a specific service, or getting information about a service.

For example, to list all the active nodes, you can use the following command:

```bash
rosnode list
```

To see the published data of a specific topic, you can use the following command:

```bash
rostopic echo /topic_name
```

# 5. Creating and utilizing ROS messages and services

ROS messages are the data structures used to send data between nodes. They are defined in `.msg` files, which specify the structure of the data. For example, a simple message for a 2D point might look like this:

```
float32 x
float32 y
```

To create a new message, you can create a new `.msg` file in the `msg` directory of your package.

ROS services are used to request a specific action or information from another node, and receive a response. Services are defined in `.srv` files, which specify the request and response data structures. For example, a simple service for moving a robot might look like this:

```
float32 x
float32 y
---
bool success
```

To create a new service, you can create a new `.srv` file in the srv directory of your package.
# 6. Working with the ROS parameter server

The ROS parameter server is a shared, multi-variate dictionary that allows nodes to store and retrieve configuration parameters. The parameters are stored in the `/param` namespace, and they can be accessed and modified using the rosparam command-line tool or the `ros::NodeHandle` class in C++ or `rospy` package in python.

For example, to set a parameter called `my_param` to the value `123`, you can use the following command:

```bash
rosparam set my_param 123
```

To retrieve the value of a parameter, you can use the following command:

```bash
rosparam get my_param
```

# 7. Understanding and utilizing the ROS transform library (tf)

tf is a ROS package that provides a library for maintaining a tree of coordinate transforms. It is used to represent the position and orientation of objects in 3D space, such as a robot's base link or a sensor. The main purpose of tf is to keep track of the relationship of different coordinate frames in the system.

tf maintains a tree of transforms, with a root node called the "world frame" and other nodes representing different parts of the robot or sensor. Each node has a parent and one or more children, and the transforms between them can be queried using the tf library.

The tf library uses a time-dependent approach, meaning that each transform has a timestamp associated with it, and when querying for the transform, the library will interpolate the transform between the two most recent transforms that are before the queried timestamp.

To use tf in your code, you can create a `tf::TransformListener` object and use it to query the transforms between different frames. You can also create a `tf::TransformBroadcaster` object and use it to publish the transforms of your robot or sensor.

For example, if you have a sensor that is mounted on a robot and you want to know the sensor's position and orientation in the world frame, you can use the following code snippet in C++:

```cpp
tf::TransformListener listener;
tf::StampedTransform transform;
try{
  listener.lookupTransform("world", "sensor", ros::Time(0), transform);
}
catch (tf::TransformException ex){
  ROS_ERROR("%s",ex.what());
}
```

Here, the lookupTransform method is used to query the transform between the "world" frame and the "sensor" frame, at the timestamp of `ros::Time(0)` which means the latest available transform.

Additionally, you can use the `tf::TransformBroadcaster` to publish the transform of your robot. For example, the following code snippet in C++ will publish the transform of the robot's base_link frame to the world frame every 100ms:

```cpp
tf::TransformBroadcaster br;
tf::Transform transform;
transform.setOrigin( tf::Vector3(0.0, 0.0, 0.0) );
transform.setRotation( tf::Quaternion(0, 0, 0, 1) );

ros::Rate rate(10.0);
while (node.ok()){
  br.sendTransform(tf::StampedTransform(transform, ros::Time::now(), "world", "base_link"));
  rate.sleep();
}
```

It's also worth mentioning that the tf2 package is the recommended version to use, it's a more efficient and modern version of tf.

In summary, tf is a powerful library that allows you to keep track of the relationship between different coordinate frames in your system. It provides a simple and consistent interface for querying and publishing transforms, and it supports interpolation for time-dependent transforms. Understanding and utilizing tf is crucial for working with robots and devices that have multiple coordinate frames.
