# cadrl_ros simple demo with Gazebo

**Please clone the repo with** 
> git clone --recursive

### Dependencies:

* [TensorFlow](https://www.tensorflow.org/) is required (tested with version 1.4.0)

* numpy is required

* [ford_msgs](https://bitbucket.org/acl-swarm/ford_msgs/src/master/) if you're using ROS. (included in this repo)

### ROS Notes:

We also provide a ROS implementation that we tested on a Clearpath Jackal ground robot.
This node is just one module of the software required for autonomous navigation among dynamic obstacles, and much of it is written as to integrate with our system.
The ROS node as written may not be particularly useful for other systems, but should provide an example of how one might connect the modules to test our learned collision avoidance policy on hardware.
For example, other systems likely have different representation formats for dynamic obstacles as extracted from their perception system, but it should be straightforward to just replace our `cbClusters` method with another one, as long as the same information makes it into the state vector when the policy is queried.

The algorithm was trained with goals set to be <10m from the agent's start position, so it would be necessary to provide this system with **local subgoals** if it were to be tested in a long journey.
For short distances, say in an open atrium, this is probably not necessary.

### Modifications have been made to allow this demo to works in gazebo:
- cadrl_node.py: cbPeds is used instead of cbClusters (details please refer to commits records)
- added a node (gazebo_link_pose.py) to extract agent pose from gazebo
- added a node (gazebo_state.py) to convert agents link states from gazebo to pedtraj messages

### To Run ROS version:
Clone and build this repo and its dependency

Connect inputs/outputs of your system to `launch/cadrl_node.launch`

##### Subscribed Topics:
* ~pose [[geometry_msgs/PoseStamped]](http://docs.ros.org/api/geometry_msgs/html/msg/PoseStamped.html)
	Robot's pose in the global frame

* ~velocity [[geometry_msgs/Vector3]](http://docs.ros.org/kinetic/api/geometry_msgs/html/msg/Vector3.html)
	Robot's linear velocity in the global frame (vx, vy)

* ~goal [[geometry_msgs/PoseStamped]](http://docs.ros.org/api/geometry_msgs/html/msg/PoseStamped.html)
	Robot's goal position in the global frame

* ~peds [[ford_msgs/PedTrajVec]]()
	Positions, velocities, sizes of other agents in vicinity

##### Published Topics:
* ~nn_cmd_vel [[geometry_msgs/Twist]](http://docs.ros.org/api/geometry_msgs/html/msg/Twist.html)
	Robot's commanded twist (linear, angular speed) according to network's output

##### Parameters:
* ~jackal_speed
	Robot's preferred speed (m/s) - tested below 1.2m/s (and trained to be optimized near this speed)


### Setup Gazebo demo:
- **everything is in global frame**
- use any suitable environment
- spawn a vehicle (the agent)
- spawn multiple vehicles (other agent) (refer gazebo_state.py)
- let the vehicles move with some arbitrary policy (constant velocity, running in circles etc)
- run gazebo_link_pose.py and gazebo_state.py
- run cadrl_ros (cadrl_node.launch)
- goal is given through rviz

Example video:
https://youtu.be/0JQ6TBKFXhQ
[![cadrl_ros_gazebo](https://img.youtube.com/vi/0JQ6TBKFXhQ/0.jpg)](https://www.youtube.com/watch?v=0JQ6TBKFXhQ)
