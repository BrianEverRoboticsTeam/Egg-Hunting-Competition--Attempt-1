# Egg Hunting Bot
This documentation described the approaches of our "egg hunting behavior" (target searching behavior) on turtlebot 2 (Kobuki) for the Egg Hunting Competition.  

**Contributors:**
- Chenrui Lei
- Siqi Yan
- Tiancheng(Tyler) Shen

**Date of the Competition:**
  March 30th, 2017

# Overview
This is the first attempt of the competition Egg Hunting within a Known Map. In this competition, our robot's general goal is to find visual targets as many as it can during the competition. The visual targets or eggs are represented by a certain type of AR Tag combination (Graph 1) or a University of Alberta emblem (Graph 2).

A competing robot will gain 5 points for finding AR Tag target, and 10 points for finding University of Alberta emblem. After the robot finds a target, it could gain 1 to 3 points additional if it successfully docked into the 50 cm by 50 cm area in front of the target: 3 points for clear sides docking, 2 points for touching one side docking, and 1 points for touching two sides docking (Graph 3).

There are 5 targets in total in each round of competition. For a certain target, only the first time of finding and/or docking procedure will be counted towards into the final competition grade. That being said, a robot won't get any points for docking or finding the same target more than once.

The competition has two rounds in total for each competing robot. Each round will last 5 minutes or until all the targets be found. Points that gain in two rounds will be cumulated into the final competition grade. The team with highest final competition grade wins the competition. Tie-breaking based on time of completion if all targets are found within five minutes in both runs.

The initial position and orientation of robots will be random pick by the judges at the beginning of each competition round. A pre-built map of competition environment will be allowed to use for robot's navigation.

# Background/Motivation
In order to make our robot as a competitive one during the competition, our robot will need to be able to perform in 4 separated tasks: localization, navigation, target searching, and docking.

Localization will be critical for target searching especially in a non-closed environment and a unknown initial pose. A good localization could prevent a robot goes outside of competition area, and help robot program to record correct location information of each visited target. Both of those features will definitely help a robot saving a lot of time during the competition.

Navigation gave us a chance to guide our robot in a high efficient target searching route specifically for this competition. It is guarantee that all the targets will be pasted at a certain height on a wall and within a certain area (four walls in the competition hall way). So, if we could keep a fixed distance from camera sensor to the target (or the wall) throughout the competition, the size of target detected by a camera frame will be fixed as well. If this is possible, we are able to improve our target searching algorithm by getting rid of iterations for searching multiple possible target size in each image frame that captured by camera sensor. That means, there exist a high efficient distance between camera sensor and the walls for target searching. The navigation is the key to guide our robot traveling in this high efficient route.

Successful target searching algorithm will help robots to collect points in the competition. Mostly, a robot won't have a second chance to find the same target within 5 minutes due to the size of the competition area. Therefore, good target searching algorithm should be able to prevent missing targets, meanwhile, helps a robot to gain more points within the same time duration. The higher points a robot gains, the higher chance it could win the game.

Docking procedure will make our robot gains 1 to 3 points depends on how the quality of docking. This points seem like not so important compare to the points for successfully find a target. However, in a very close game, those few points could make different between winning or losing.

To add up to those four skills of our robot, it should also able to perform as a finite state machine. By doing this, those four different skills and/or algorithms are able to coordinate with each other by translating between those four major states when the conditions of a certain state met. There are some other minor components, like the map for navigation, the control program for docking and undocking, etc, will be include in our approach.

To sum up, a good localization makes navigation possible, then makes target searching high efficiently. And, docking makes higher chance for winning. At the end, finite state machine makes those functions works all together.

# Question/Hypothesis
As shown by the pervious competition (Race with GMapping and AMCL) which took place at the same environment as this competition, GMapping and AMCL are very good for mapping and navigation, so we could just take the previous code as a module for navigation, and use the same map from the previous competition. And, the localization could be done by AMCL as well. And, we are trying to prove that AMCL is an proper solution to localize the robot itself.

For target searching, we planned to use template matching technic since it's able to find a target far away (works within about 7 meters during our tests) from the target. The problem is the efficiency of template matching algorithm. This algorithm could only detect a certain size of template in a single iteration. Therefore, if the size of template on an image frame is unknown, this template matching algorithm need multiple iterations to result a good detection in terms of accuracy. Then, the algorithm couldn't be robust enough to be used in a real time target searching. This problem won't exist if we can keep a fixed distance between our target searching camera and the visual target, since the target size on camera frame will keep the same as well in this situation which could prevent inefficient multiple iterations. But this ideally assumption is actually impossible during the competition. There are errors in the sensors of robot which makes it impossible to control a robot perfectly keeping a certain distance to the wall for all times. In order to overcome those problems and maintain a reasonable accuracy at the same time, we decided to add a ultrasonic range sensor. This sensor will provide the distance information to the template matching algorithm, and the algorithm could calculate the proportional size of target template on the camera frame base on this distance data. Then, the template matching algorithm won't need a lot of iterations to achieve a reasonable high accuracy since the size of target template on a frame becomes known. Besides, this solution could works adaptively for different distance between camera and target without any affects to the accuracy. It should turns out to be an robust solution for target searching.

Also, a finite state machine will be implemented by us as main control of the robot. This main control will subscribe all the topics from the four separated components and make decisions and/or sending command to the robot based on data that receives from components. Four modes or states be defined in the main control that will execute in a circle are Navigation and searching, Found, Docking, Undocking. There is also a localization state, but it will only execute once at the beginning and translate to Navigation and searching state once it done. We would like to prove that this kind of state machine is suit to be used in the competition like this one.

# Materials

# Method/Procedures/Approach
We planned to start with four separated components since most implementations of the components are available online or is completed in prior course demonstration and/or competition. For example, the navigation by Gmapping and AMCL.

Our program contains the following modules:
- Localization
- Navigation
- Target detector
- Docking controller
- Main controller

For docking procedure, we will need to obtain a pose of target when the robot is close enough to it. Then, a path of docking will be calculated based on this pose data. We used two algorithms for two different visual target pose detection, one for AR Tag and another for University of Alberta emblem.

We use the [ar_track_alvar](http://wiki.ros.org/ar_track_alvar) package, which available from ROS, to do the AR Tag pose detection. And for the University of Alberta emblem, we implemented a features matching algorithms to match key points between the model emblem and an image frame. Once we got those matched key points, we calculate the pose data by using [solvePnP](http://docs.opencv.org/3.0-beta/modules/calib3d/doc/camera_calibration_and_3d_reconstruction.html#solvepnp) function in openCV.

# Results

# Analysis/Discussion
In the competition, our robot didn't performed correctly, specifically, it keep going between different checkpoints, and couldn't stop at the target.

The problem for our robot is the communication between the main controller and the the navigation module, either the main controller failed to tell the navigation module to stop when it found a target, or the navigation module reject to stop for some reason (we didn't investigate the details because we plan to use another approach for the navigation module in the next competition). We designed the program in the way that the each module works independently, but we have difficulty to combine then together. Our main controller is written in a procedure way rather than state machine, which causes it becomes more and more complicated and harder to debug as we add more functionalities to it.

# Conclusions
In this competition, our robot is able to detect both the AR codes and UA logos targets, and the docking works independently. And our robot can identify its position when it is placed at a random location within the map.

For the improvement, obviously we will re-write the main controller using state machine.
