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

A middle man ros node will subscirbe to topic /gazebo/ModelStates (Gazebo publishes state of your bot in this topic) <br>
This node reads the state and out of it publishes only the x and y coordinates on two different topics with name /idhar_hai_x <br>
and idhar_hai_y <br>

Data Published on /idhar_hai_x and /idhar_hai_y is received by Simulink block and hence further displays it, <br>
It is also possible to design a controller in MATLAB and return control output after performing computations in Simulink.

# Gazebo Simulink Indirect bridge

Create a catkin_ws, src folder and inside it create a package with name my_robot_controller <br>
create a folder with name scripts, inside the folder create a python file using,
> touch middle_man.py

Copy the below code in it,
```python

#!/usr/bin/env python3

import rospy
from gazebo_msgs.msg import ModelStates
from std_msgs.msg import Float32

def model_states_callback(msg):
    global position_publisher_x, position_publisher_y
    for i in range(len(msg.name)):
        model_name = msg.name[i]
        position_x = msg.pose[i].position.x
        position_y = msg.pose[i].position.y
        rospy.loginfo(f"Model: {model_name}, Position X: {position_x}, Position Y: {position_y}")
        # Publish the position X to the /idhar_hai_x topic
        position_publisher_x.publish(position_x)
        # Publish the position Y to the /idhar_hai_y topic
        position_publisher_y.publish(position_y)

def main():
    global position_publisher_x, position_publisher_y

    # Initialize the ROS node
    rospy.init_node('model_states_subscriber', anonymous=True)
    
    # Define the publishers for the /idhar_hai_x and /idhar_hai_y topics with a smaller queue size
    position_publisher_x = rospy.Publisher('/idhar_hai_x', Float32, queue_size=1)
    position_publisher_y = rospy.Publisher('/idhar_hai_y', Float32, queue_size=1)
    
    # Subscribe to the /gazebo/model_states topic with a smaller queue size
    rospy.Subscriber('/gazebo/model_states', ModelStates, model_states_callback, queue_size=1)
    
    # Keep the node running
    rospy.spin()

if __name__ == '__main__':
    main()


```

I've created two different topics for x and y coordinate, However it is possible to publish both <br>
on the same node.<br>

Add the following snippet in your CMakeLists.txt file in your workspace
``` python
catkin_install_python(PROGRAMS scripts/middle_man.py 
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)	

```

You can update your package.xml if some dependencies are missing 

Now, Open matlab using,
> sudo matlab

Open a blank model in Simulink and create the below diagram,

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/dbf03d9c-8355-4d49-9923-712e34b4a932" alt="Bot_Empty" width="1000" height="400">

MATLAB function used in the block is for holding the non zero previous value in output <br>
to maintain consistency (otherwise Output will keep fluctuating between output and zero).

I'll upload all the relevant files, Still here's a look of what goes inside the function:

```MATLAB

function y = fcn(u)
    persistent last_value
    if isempty(last_value)
        last_value = u; % Initialize with the same type as input
    end
    if u ~= 0 % Update condition, adjust as needed
        last_value = u;
    end
    y = last_value;

```

set the Stop time to inf,
#### Final Steps
- Run Roscore
- Open turtle bot3
- Connect Teleop control
- Connect MATLAB with your master node using rosinit(' ')
- In your subscibe block, subscribe to /idhar_hai_x for your x-coordinates block
- Subscribe to /idhar_hai_y for y coordinates subscribe block
- Execute Simulink
- Execute middle_man node, using
  > rosrun my_robot_controller middle_man.py

You'll be able to move your Turtlebot3 using your teleop control and corresponding coordinates will <br>
be displayed on your Simulink display.

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/5b5d4d00-2e29-4532-a8c8-b1ab645db9d0" alt="Bot_Empty" width="1000" height="400">

You can also see the information flow by executing,
>rqt_graph

<img src="https://github.com/Ayush-1081/ROS_simulink/assets/132341349/d3f22ae6-92a1-4547-bb6f-93ba9b7b269d" alt="Bot_Empty" width="1000" height="400">

Line_coordinates.slx is the name of my simulink file, <br>
Hence, Gazebo is publishing states on /gazebo/ModelStates to a node, <br>
node further publishes the Position on Simulink <br>

By editing the code in middle_man.py, Instead of coordinates we can get any state of the system.
