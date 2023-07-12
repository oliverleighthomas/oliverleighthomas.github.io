---
layout: post
title: 6DOF Navmesh Week 6 - Steering
---

Hello, and welcome to another weekly update on the development of my six degree of freedom navmesh. This week has involved accomplishing several small goals, bringing me closer to the point where I feel confident deploying my navmesh in a production environment.

Initially, my focus for this week was going to be on implementing support for jumping AI. However, after conducting further research, I realized that dedicating time to steering was more important. However, I decided to address a few minor aspects of my behavior tree task before delving into steering.

### Cleanup

I identified two crucial requirements to ensure the versatility of my behavior tree task across various environments: support for a user-defined acceptable radius and for moving actors.

Integrating initial support for a user-defined acceptable radius proved to be fortunately straightforward. By defining the variable as a UPROPERTY, users can modify it on the behavior tree node, similar to adjusting Unreal's default "MoveTo" node. However, incorporating this variable into the agent's logic required a bit more planning.

The acceptable radius needs to accomidate two other values, the agent's own size and, if the agent was moving toward an actor with collision, the target's size.

Given that agents traverse a 3D space on my navmesh, utilizing capsule components' radii wasn't feasible. For example, if an agent was approaching a goal from above or below, using the height of the agent's capsule as the radius would be more appropriate. However, dynamically adjusting the radius based on the agent's approach angle seemed far too computationally complex.

Instead, I opted for a more simplified solution entailing chosing the maximum radius along all three axes and measuring the acceptable radius against that. Although this method could result in oblong agents staying a slightly greater distance from goals than desired, I felt the difference would not be noticable in most scenarios. Beyond that, this solution was significantly more cost-effective than dynamic radius adjustments.

![test](https://i.imgur.com/LBqNl5H.png)

In addition to an acceptable radius variable, I introduced support for following a moving actor. In my capstone game, enemies track the player, who frequently moves around the map in an attempt to evade them. Consequently, enemies needed to effectively pursue the player.

To achieve this, I incorporated a check within the function where an actor is set as the agent's destination. If the actor is movable, the code knows to monitor its location. During each tick, the goal actor's location is checked. If it moves beyond a user-defined tolerance threshold, the agent's path is recalculated, with the goal actor's updated position serving as the new destination.

![test](https://i.imgur.com/5ZMwc3b.png)

While testing this new feature, I noticed that the pathfinding process was considerably slow. Each task resulted in a noticeable delay of approximately one second before a path was found, which was untenable for a production environment. Before proceeding with steering, I prioritized optimizing my pathfinding algorithm.

### Optimization

Upon reviewing my current pathfinding code, I realized that I had not been fully leveraging the computational time users made avalible.

My code allowed users to specify the maximum number of tasks to be completed in a given tick. However, in practice, if fewer tasks were active than the specified limit, the code would iterate through them once and then put the worker thread to sleep before repeating the process. Even if a user intended to process two hundred tasks within a tick, if only two tasks were active, only two iterations were performed.

I made slight adjustments to my pathfinding algorithm, ensuring it fully utilized the iterations specified by the user. Now, if a user allowed two hundred tasks to be iterated in a tick, the updated code would iterate over two tasks one hundred times. As a result, when the active task array contained only a few tasks, they would often be completed before the worker's next tick.

![test](https://i.imgur.com/6y99Zue.png)

Testing revealed this change was incredibly helpful. Whereas it once took half a second to find a path, now it happened almost instantaneously!

![test](https://i.imgur.com/TBAk0kT.png)
![test](https://i.imgur.com/XXE2pmP.png)

I was very excited about this speed improvement, and now users now have greater control over the number of iterations performed per tick. With this optimization complete, I could proceed to the next task—implementing steering.

### Steering

Steering is not intended to be a primary focus of this project, as character movement does not significantly contribute to my personal growth as a programmer. Therefore, I opted to develop simple yet smooth steering that users could override based on their specific requirements.

To allow for easy overriding, I integrated steering into the agent's movement component rather than the behavior tree task itself. Building upon my base movement component, a version of Unreal's "FloatingPawnMovement" component I adjusted to work for characters rather than pawns, I made two minor adjustments:
1. I introduced a function to rotate the agent towards its goal.
2. I modified the velocity input function to utilize linear interpolation instead of simple addition.

![test](https://i.imgur.com/nAYJzRZ.png)
![test](https://i.imgur.com/ROYYzoE.png)

Here is a comparison of the movement controller before and after the adjustments:

![test](https://i.imgur.com/Pyjv2w6.gif)
![test](https://i.imgur.com/ngvHtXV.gif)

The results were perfect for my capstone team's game!

With steering successfully integrated, I could now work on finalizing the remaining features of my navmesh, with dynamic collisions being the main focus.

### Dynamic Collisions

Regrettably, implementing dynamic collisions has proven more challenging than initially anticipated.

While I have already incorporated the process of notifying octants when their collision status changes and have a strong understanding of how to incorporate this into my behavior tree task using events, I encountered an obstacle when identifying which octants need to monitor their collision status.

As mentioned when I initially started working on dynamic collisions, I'd like to check the collisions of octants only if they are currently being used in a path. There is no impact if obstacles block areas that no agent is attempting to navigate through, and it would be computationally inefficient to check the collision status of every octant in the navmesh, especially for large worlds.

However, determining the relevant octants for a task is not as straightforward as I initially thought. While I could initially store the relevant octants of a path during the initial pathfinding calculations, these octants change as the path is smoothed.

Consider the following path of an agent to a goal:

![test](https://i.imgur.com/WBYJIiN.png)

Originally, I would add listeners to the identified octants. However, if the path is smoothed into a straight line, like this:

![test](https://i.imgur.com/rq6xXKk.png)

The octants I initially found are no longer all relevant to the path. Furthermore, new octants now become relevant. The question then becomes, how do I identify which octants to monitor?

My current plan, which is still under development, involves creating an algorithm to identify all octants between two points. However, due to the irregular sizes of the octants in my navmesh, this has proven challenging. Algorithms for finding voxels along a line are not uncommon, as they are useful for techniques such as anti-aliasing. During my research, I encountered algorithms like Bresenham's, which would typically serve this purpose. However, those algorithms rely on the assumption that the grid the line traverses is uniformly sized, meaning they are not fully applicable to my navmesh.

My current approach involves traversing the line in steps proportional to the size of each octant encountered. For instance, if an octant on the line measures five units in length, I move five units along the line to the next octant. However, I encountered a challenge wherein these steps do not always extend beyond the boundary of the current octant, resulting in loops where the same octant is repeatedly found.

I am actively working on refining this algorithm and hope to find a solution by the end of next week. Fortunately, Deckweaver has very few dynamic obstacles, allowing me to proceed with integrating my navmesh into a production environment without fully implementing this feature.

### Conclusion

Despite being unable to solve the dynamic collision problem towards the end of this week, I am pleased with the progress I have made, especially with the speed boost in my pathfinding algorithm! Next week, my focus will be on fully integrating my navmesh into a production environment. I am anticipating additional necessary adjustments and bugs will reveal themselves, but I am eager to face the upcoming challenges and remain committed to delivering AI with intelligent pathfinding capabilities for my capstone team. Thank you for reading, and I look forward to sharing more progress next week!