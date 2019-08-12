# traj_gen : path generation tools with a variety of options

<p align - "center">
<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/intro2.png" width = 800 > 
</p>

**(left)** coupled optimization for single corridor type **(right)** decoupled optimization for multi corridor or non-corridors  

<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/run_video.gif">


- **(running traj_gen)** step by step tutorial  

## 0. Release notes

#### 2019/8/12


#### 2019/5/16

QSlider was added. As of now, *traj_gen* can accommodate height input from user. Adjust slider for height and then select waypoint. In this way, height value will be encoded together(see below).    

<img align = "center" src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/gui_update.png" witdh = 600>
<em> two types of corridor </em>




## 1 Installation 

### 1.1 Dependencies 

#### (0) ROS and qt related packages

#### (1) qpOASES 
- The package bases qpOASES as quadratic programming solver.  Please refer  https://projects.coin-or.org/qpOASES and install the library. (make sure `sudo make install` after build of qpOASES)
- Let the qpOASES package direcotry ${qpOASES_SRC}. Please insert your qpOASES directory in CMakeList.txt 

```
## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system)
// here insert your qpOASES directory 
set(qpOASES_SRC /home/jbs/lib/qpOASES-3.2.1)

file(GLOB_RECURSE qpOASES_LIBS ${qpOASES_SRC}/src/*.cpp)
```


## 2 ROS Node API

### 2.1 Published Topics 

 * control_pose [geometry_msgs/PoseStamped] : published topic for desired control point of current time step  
 * safe_corridor [visualization_msgs/Marker] : the safe corridor marker
 * trajectory [nav_msgs/Path] : generated trajectory 
 * trajectory_knots [visualization_msgs/Marker] : the points on the path evaluated each waypoint time 
 * waypoints_marker [visualization_msgs/MarkerArray] : the recieved waypoints from user

### 2.2 Subscribed Topics 
 * /waypoint [geometry_msgs/PoseStamped] : waypoint input from Rvis by user



### 2.3 Parameters in Launch 
 * world_frame_id : the world frame id. (default : /world)
 * waypoint_topic : the topic name by user input 



## 3 USAGE 

### 3.1 Qt gui
<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/traj_gen.png"> 

This library provides interface where you can specifiy a sequence of waypoints from Rviz 

(1) ROS connect : please push the button at the beginning while roscore is running 

(2) select waypoints : waypoints insertion from rviz is allowed while this button is clicked 

(3) trajectory generation : quadratic programming with assigned parameters

(4) publish : the time allocation of the trajectory is equal division from 0 to "simulation tf" of gui. A desired control point will be published in *geometry_msg/PoseStamped* message type. The evaluation time for control point will be paused by re-clicking (still publishing). If you want to evaluate the trajectory of interest again from the start, Then release the button and re-create the same trajectory with *Traj generation* button. 

(5) manage waypoints : please provide the absolute of directory for txt file 

(6) textbox. important message will appear 

### waypoints selection from user
<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/traj_gen-2.png"> 

*You can also save and load the waypoints in txt file format. In that way, you may assign the heights for each waypoint*

## 4 Alogrithm 

This package is based on minimum jerk or snap with motion primitives of polynomials 

**refer**
Mellinger, Daniel, and Vijay Kumar. "Minimum snap trajectory generation and control for quadrotors." 2011 IEEE International Conference on Robotics and Automation. IEEE, 2011.

* * *
### 4.1 Waypoints 


<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/hard_vs_soft.png"> 

#### (1) Soft waypoints

not necessarily pass through the specified waypoints. But it can minimize jerk more.

#### (2) Hard waypoints

the waypoints will be passed exactly as hard constraints 

* * *

### 4.2 Corridor

<img src="https://github.com/icsl-Jeon/traj_gen/blob/master/img/explain_corridor.jpg"> 

#### (1) multiple sub boxes between waypoints which is axis-parallel 

Number of constraints will be increased but x,y,z can be solved independently.
	
In general, imposing too many sub constraints will be infeasible for polynomial curves 

#### (2) single box between waypoints 

Number of constraints will be decreased but x,y,z cannot be solved independently


## 5 Issues 
 * please avoid using polynomial order 6 for the case where you minimize the jerk squared integral (objective derivate = 3)
	


