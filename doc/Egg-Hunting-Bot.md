# Egg Hunting Bot
This documentation described the approaches of our "egg hunting behavior" (target searching behavior) on turtlebot 2 (Kobuki) for the Egg Hunting Competition.  

**Contributors:**
- Chenrui Lei
- Siqi Yan
- Tiancheng(Tyler) Shen

**Date of the Competition:** March 30th, 2017

# Overview
This is the first attempt of the competition Egg Hunting within a Known Map. In this competition, our robot's general goal is to find virtual targets as many as it can during the competition. The virtual targets or eggs are represented by a certain type of AR Tag combination (Graph 1) or a University of Alberta emblem (Graph 2).

A competing robot will gain 5 points for finding AR Tag target, and 10 points for finding University of Alberta emblem. After the robot finds a target, it could gain 1 to 3 points additional if it successfully docked into the 50 cm by 50 cm area in front of the target: 3 points for clear sides docking, 2 points for touching one side docking, and 1 points for touching two sides docking (Graph 3).

There are 5 targets in total in each round of competition. For a certain target, only the first time of finding and/or docking procedure will be counted towards into the final competition grade. That being said, a robot won't get any points for docking or finding the same target more than once.

The competition has two rounds in total for each competing robot. Each round will last 5 minutes or until all the targets be found. Points that gain in two rounds will be cumulated into the final competition grade. The team with highest final competition grade wins the competition. Tie-breaking based on time of completion if all targets are found within five minutes in both runs.

# Background/Motivation
In order to win the competition, our robot will need to navigate itself to travel in the competition area with a pre-built map, and search for targets (AR codes and UA logos). Once a target is found, the robot will dock in front of the target and sound a unique beep to indicate it finds a target.


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
