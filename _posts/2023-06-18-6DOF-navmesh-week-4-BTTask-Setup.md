---
layout: post
title: 6DOF Navmesh Week 4 - Path Smoothing and BTTask Set-Up
---

Welcome to the Week 4 update on the progress of my six-degree-of-freedom navmesh progress! This week, I've begun transitioning from using blueprint callable functions for testing to setting up and utilizing a proper behavior tree node. However, I had to make adjustments to my schedule, which I will elaborate on in this post.

### Path Smoothing

My first focus for this week was on path smoothing. Initially, I aimed to create a path that allows agents to move naturally without requiring steering. To achieve this, I planned to refine the path by removing unnecessary waypoints, and then generating a bezier curve from the remaining points.

When utilizing my navmesh for pathfinding, agents traverse through the centers of a series of octants, resulting in jagged and inefficient paths:

![test](https://i.imgur.com/9KykKSt.png)

It was evident that the agent, represented by the white sphere, could navigate directly to the goal, represented by the blue sphere. However, unless the navmesh was generated using exessively small octants, a perfectly straight path wasn't possible. To address this issue, I devised a simple algorithm. I performed a linetrace between a given waypoint and another waypoint two indices away on the path. If the linetrace found no obstacles between the points, the intermediary waypoint was removed, and the path would instead have a direct line between the two original waypoints.

Implementing this algorithm significantly improved the efficiency of the generated paths!:

![test](https://i.imgur.com/jsUjAkr.png)

Nevertheless, paths could still appear jagged when obstacles were present, and the abrupt turns along straight lines weren't as realistic as I desired.

Originally, I planned to resolve these issues by calculating bezier curves between the remaining waypoints of a path. However, during the implementation, a new problem arose:

![test](https://i.imgur.com/1UtYbje.png)

Bezier curves often intersected with obstacles in the scene. To address this issue, I initially considered checking all points on a curve for collisions and making adjustments as necessary. However, I found this solution to be computationally intensive, as it required iterating over the entire path three times (initial smoothing, bezier calculations, and adjustments). This approach could significantly slow down pathfinding, particularly for agents with long paths.

Instead, I dedicated to researching different steering methods. During this exploration, I discovered that steering and pathfinding are distinct problems, and I wouldn't be able to properly resolve both within a week. As a result, I have decided to replace the week dedicated to my "jumping AI" stretch goal with a week instead dedicated to implementation of steering. Currently, I plan to handle steering within the behavior tree node, aligning with Unreal's "MoveTo" behavior tree node structure. I am excited to delve into steering methods soon!

That meant I could proceed with my current smoothing algorithm, and so I moved on to develop a behavior tree node that users can utilize to interact with my navmesh.

### Behavior Tree Node Set-Up

The process of creating my own behavior tree node proved to be an engaging and informative experience. I experimented with the virtual functions provided by Unreal, particularly exploring the concept of "NodeMemory" and its utilization in guiding agents along a path. Unfortunately, due to the extended research required, I was unable to complete the behavior tree node this week as I originally hoped.

Currently, my behavior tree incorporates NodeMemory, which is cast to a custom struct containing information about the path, the current index for traversing the path, and a variable defining an acceptable radius around each waypoint. Upon executing the task, I schedule a pathfinding operation and await its completion using a dynamic delegate. Once the pathfinding process concludes, I update a switch in the NodeMemory to indicate readiness for moving the agent along the path.

All the aforementioned work culminates in the creation of a path through the behavior tree node, as opposed to manually-called functions in blueprints:

![test](https://i.imgur.com/BVhSmqR.png)

To provide users with greater control over their agents' behavior, I opted to utilize the built-in movement component functions of the agent to navigate along the path. This way, users can create their own MovementComponents if they desired more customized behavior. However, this approach necessitates the creation of my own custom "FlyingCharacterMovement" component to physically move the agent along the path. 

Unfortunately, the time invested in bezier curve exploration and the extensive research for setting up the behavior tree node led to slightly less progress than anticipated this week. Nonetheless, I now possess a strong understanding of how behavior tree nodes operate within Unreal and am excited about dedicating the upcoming week to learning more about steering behaviors! I still plan to commence user testing in Week 6, ensuring that my capstone team can begin implementing a properly functioning flying enemy.

Thank you for taking the time to read this week's update, and I look forward to sharing further progress next week. Stay tuned!