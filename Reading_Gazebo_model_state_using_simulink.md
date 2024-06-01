# Discussing the Problem with ROS Subscribe
Standard way of ROS subscribe where we directly subscribe a topic and get the data doesn't work for data published by gazebo. <br>
I don't know the exact reason. I've tried all different ways but still due to data being published as group of group of element buses, <br>
for some reason bus selector is unable to select an individual bus element from the group.<br>

From the Repositories I've visited I've noticed them using **Gazebo Subscribe** block insted of **ROS subscribe** block while <br>
while reading from a topic on which gazebo publishes.

I tried using Gazebo subscriber but when I tried opening the block, it shows an error:

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/14463c5b-f30d-4d73-b6e5-4ae5f1df8075" alt="Bot_Empty" width="1200" height="600">

Error says, Gazebo Subscribe block failed to connect to the Gazebo plugin because  the plugin is not set up correctly, or there is <br>
an issue with the specified port number 14581.

You need to setup co-simulation plugin between Gazebo and Simulink, I've tried doing that but didn't work for me,

[I'm providing link for MATLAB documentation of setting up co-simulation plugin](https://in.mathworks.com/help/robotics/ug/perform-co-simulation-between-simulink-and-gazebo.html)

It did not workout for me so I had to design an alternative,

# Connection via Nodes
**Problem :** Simulink cannot read data published by gazebo by can read data published by an individual ROS node running in the system <br>

**Idea :** We'll create a node in our system subscribed to gazebo, that node will receive data from the Gazebo and publish it on Simulink <br>
           Because Simulink and interact with ros nodes, there won't be an issue with the communication. Similarly Simulink will publish data <br>
           on an independent node in our system and that node will pass the data to the simulink


**In this Framework we can acheive flow of Data without any direct interaction of Gazebo with Simulink.**

# Gazebo Simulink Indirect bridge


