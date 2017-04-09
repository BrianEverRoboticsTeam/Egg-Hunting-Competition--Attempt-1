# Egg Hunting Bot
This documentation described the approaches of our "egg hunting behavior" (target searching behavior) on turtlebot 2 (Kobuki) for the Egg Hunting Competition.  

Contributors:
- Chenrui Lei
- Siqi Yan
- Tiancheng(Tylar) Shen

Date of the Competition: March 30th, 2017

# Overview
This is the first attempt of the competition Egg Hunting within a Known Map. In this competition, our goal is to navigate our robot travel the competition area with a pre-built map, and search for target (AR codes and UA logos). Once a target is found, the robot will dock in front of the target and sound a unique beep.

# Background/Motivation

# Question/Hypothesis

# Materials

# Method/Procedures/Approach
Our program contains the following modules:
- Navigation
- Localization
- Target detector
- Docking controller
- Main controller

As shown by the pervious competition (Race with GMapping and AMCL), GMapping and AMCL are very good for mapping and navigation, so we could just take the previous code as a module for navigation, and use the same map from the previous competition.

# Results

# Analysis/Discussion
In the competition, our robot didn't performed correctly, specifically, it keep going between different checkpoints, and couldn't stop at the target.

The problem for our robot is the communication between the main controller and the the navigation module, either the main controller failed to tell the navigation module to stop when it found a target, or the navigation module reject to stop for some reason (we didn't investigate the details because we plan to use another approach for the navigation module in the next competition). We designed the program in the way that the each module works independently, but we have difficulty to combine then together. Our main controller is written in a procedure way rather than state machine, which causes it becomes more and more complicated and harder to debug as we add more functionalities to it.

# Conclusions
In this competition, our robot is able to detect both the AR codes and UA logos targets, and the docking works independently. And our robot can identify its position when it is placed at a random location within the map.

For the improvement, obviously we will re-write the main controller using state machine.
