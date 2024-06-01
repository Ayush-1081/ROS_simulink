# Turtlebot3 Setup and Type of Data

#### Installing Turtlebot3
> sudo apt-get install ros-noetic-turtlebot3-*

(I use ros noetic for the purpose, you can write version of ros you're using instead of noetic) <br>
You need to set default TurtleBot3 Mode, it can be done by executing
> echo "export TURTLEBOT3_MODEL=burger" >> ~/.bashrc

here instead of burger you can also use other models of bot like waffle, waffle_pi

#### How to launch Turtlebot3
you can execute your turtle in an empty world using
> roslaunch turtlebot3_gazebo turtlebot3_empty_world.launch
<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/526bea55-f27a-4fd5-bc8e-c48cae46b2b1" alt="rostopics" width="600" height="400">

An alternative is to summon the bot in the standard environment in Gazebo using the command,
> roslaunch turtlebot3_gazebo turtlebot3_world.launch

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/8479546c-9adb-4b39-8a07-f8c9d9ed414b" alt="rostopics" width="600" height="400">

You can move the bot around using the turtlebot3_teleop package

The Turtlebot3 is a differential drive bot and its motion is described by its linear velocity and angular velocity. The ratio of the instantaneous
linear velocity to the instantaneous angular velocity gives the radius of curvature of the arc it traverses at the instant.

On executing the command below,
> rosrun turtlebot3_teleop turtlebot3_teleop_key

using wxasd you can control motion of the bot.

#### Know your Message (Important for using MATLAB)
How do we know the type of message that needs to be published into /cmd_vel ? Well, on launching the bot in Gazebo, execute the following command in a new tab
You can get the name of topics running using the code
> rostopic list
<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/176db555-8778-4f8c-9c7c-a6045d4e9b81" alt="rostopics" width="600" height="400">

To get info about a particular topic, run the following code,
> rostopic info /cmd_vel

Instead of cmd_vel you can add name of topic you want info upon.
<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/ad8cdfa5-5a36-4559-ab1e-053f5a783dbc" alt="rostopics" width="600" height="400">


This data mostly shows subscriber, publisher and Type of data for that particular node, <br>
How ever data being published could be a scalar, a vector or a group of vectors <br>
To know the exact type of data being published, run the command
> rosmsg info geometry_msgs/Twist

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/f5442c73-03c8-474c-a4f0-ed71c0ea0eb8" alt="rostopics" width="600" height="400">

Note that, geometry_msgs/Twist if the type of data being published under topic /cmd_vel, you can get the type
of message on your topic using rostopic info <topic>

For the given topic, two vectors are being published with name Linear Velocity and Angular Velocity,<br>
Both vectors have 3 componentss each being a 64 bit Floating point Number.
