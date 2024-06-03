
# Reading Data on cmd_vel Using MATLAB

You can follow any available youtube video to Install MATLAB in your Linux system.<br>
I would suggest to use the following command everytime you wish to run MATLAB,
> sudo matlab

Install **ROS Toolbox** in your MATLAB,<br>
Also use MATLAB2024Ra or any version after that, I'm using 2024Ra because of it being the most recent version <br>
available by the time I'm writing this, version before 2024Ra are not compaitable with PX4 1.14 Toolbox (latest version by tis time)


run turtlebot3, run teleop loop and open Simulink,

run roscore in your Terminal, open another window and type
> hostname -I

It will show IP adress of your system, open MATLAB and in the MATLAB command prompt type,
> rosinit('IP_adress')

Your matlab will create a node while master node being your system.

Open Simulink once global node has been initialised in MATLAB, Open blank model and create the below model,

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/8fca13a0-899f-44bc-bc6a-0dd8bc20ee1f" alt="Bot_Empty" width="500" height="600">

Select cmd_vel in the topic options and hit apply, <br>
**NOTE** Select the topic Once you've connected bus selector, it takes some time for data in bus selector to change.

Open Bus-selector, Remove previously present output elements and copy Linear Velocity and Angular velocity as output elements <br>
Bus selector will seperate both vectors,

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/ac26d994-a07c-4b8c-8fa2-52dbdb0b013c" alt="Bot_Empty" width="500" height="600">

But Bus selector receives and gives output a bus element, which cannot displayed <br>
hence add a **"bus to vector"** block to convert them before displaying. <br>
But bus to vector element can only operate on **virtual bus**. However output from bus selector is a not-virtual bus, <br>
hence add a **To Virtual bus** block before **bus to vector** block (As shown in the block diagram above). <br>
set the **Stop time** to inf (on the top in the Simulation bar of simulink) and Run the program, <br>

You can see the Real time Linear and Angular velocity being printed in your simulink published by a node from your system over the topic /cmd_vel <br>
<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/2c529772-cbdd-4ffa-994d-4ade002e6cc8" alt="Bot_Empty" width="1200" height="600">

You can also see the Information transfer diagram (RQT graph) by running command below in a new window,
> rqt_graph

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/502ceaa8-8d3a-4160-a393-cb4961aad55d" alt="Bot_Empty" width="600" height="400">

here /turtlebot3_teleop is node on which you're giving commands using WASD,<br>
this node has been subscribed by gazebo and an untitled node. <br>
Gazebo taking this input simulates the motion of bot. <br>

This untitled node is our node in Simulink through which we subscribed /cmd_vel.


