---
layout: post
title: 6DOF Navmesh Week 3 - Dynamic Obstacles and Pathfinding Updates
---

Hello, and welcome to the latest update on my six-degree-of-freedom navmesh! This week I some significant strides towards getting my navmesh production-ready, despite some minor reworks to my schedule.

### Dynamic Obstacles

The plan for this week was to implement the stretch goal of dynamic obstacle handling. After doing some research and considering my options, I identified three potential strategies:
1 - Collision checks on the octants involved in a pathfinding task: I realized that I did not need to know how the collision of an area far away from an agent was changing, as it would not affect the relevant agent. I only needed to know if the path the agent was trying to navigate was changed.
2 - Collision checks around dynamic obstacles: Instead of querying the entire navmesh for changes, I had the idea of only querying around actors I knew would move. However, the issue I ran into here was how I would know what obstacles I should track. There is a "dynamic" collsion channel for objects in Unreal I could utilize, but I did not want to limit users in what collision channels they used with my navmesh.
3 - Manually flag dynamic obstacles: While this would resolve the issues with the previous option, it required more effort on the user's part. I felt this might be burdonsome, especially for games with many different dynamic meshes, and I wanted to keep my navmesh user-friendly.

Ultimately, I went with option one. I modified my pathfinding algorithm so it would store all the octants an agent would travel through in an array. Within my tick function, I would go through that array and check for any changes in collision of those octants. If any were detected, the octant would be re-subdivided.

![Test](https://i.imgur.com/c1cSXso.png)

I successfully implemented this system and tested it with my temporary pathfinding algorithm. However, the initial tests revealed an issue with my navmesh &mdash; parent octants, after detecting collision but before subdividing themselves further, would mark themselves as "non-navigable." This could make it so agents couldn't navigate to octants with navigable children, as the octants themselves would be considered inaccessible.

To resolve this, I replaced my binary "navigable"/"non-navigable" system with an enum that categorized octants as either navigable, non-navigable, or as a parent. With this issue resolved, my dynamic obstacle handling was working!

![Test](https://i.imgur.com/quK0ZqO.gif)

As of this video, changes in the navmesh did not affect the pathing of agents. Initially my plan for this week would have involved implementing a system to recalculate the path if a new obstacle was detected, however my current pathfinding algorithm was far from complete enough to properly utilize it. If I implemented dynamic updates to pathing now, I would likely need to re-implement once the proper pathfinding algorithm was in place.

Instead, I opted to work on another optimization strategy I considered: multi-threading.

### Multi-threading

I opted to start by utilizing multi-threading not for the generation of my navmesh, but for the dynamic collision updates. Since dynamic collision checks occur on tick, multiple agents navigating a constantly changing navmesh could slow down the game.

As I had never used multi-threading within Unreal before, I followed a [tutorial](https://unrealcommunity.wiki/multithreading-with-frunnable-2a4xuf68) to help me through the set-up process. I provided my secondary thread with a reference to my navmesh and had it call a "TickDynamicCollisionUpdates" function. Now, checks for dynamic collision do not bog down the main game's thread!

![Test](https://i.imgur.com/fW8Dicv.png)

As the tutorial I followed was easy to understand, I implemented multi-threading in far less time than I originally anticipated. This meant I still had plenty of time to make further progress this week. However, as previously mentioned, I feared finalizing dynamic obstacle updates would be a waste of time, as I'd likely have to redo it once the proper pathfinding algorithm was in.

Instead of putting my time towards that, I instead opted to adjust my schedule. Originally, I planned to fully implement dynamic obstacle handling this week, and spend next week working on finalizing the pathfinding algorithm. However, I decided to start finalization of the pathfinding algorithm this week. This would allow me more time next week to implement dynamic obstacle handling properly, and potentially make additional optimization changes before I begin testing my navmesh in a production environment. 

### Updates to Pathfinding

Currently, the algorithm only functioned at the lowest level of subdivision, and had no concept of children within the octree. To properly implement pathfinding on my navmesh, I would need a way to access child octants.

The first change I made was to my "GetOctantAtLocation" function. Originally, this simply calculated an octant's index in the octree using the desired location. I made a minor change for bounds checking, preventing crashes from accessing non-existant octants, and then added an additional check for children. If children were found, I called a recursive function to determine exactly which child within the octant contained the point I was looking for.

![Test](https://i.imgur.com/Yzlq6WU.png)

Now, if a desired point is within a child octant, I access that as opposed to the parent at the highest level!

![Test](https://i.imgur.com/ZAoftu2.png)

The second change I needed to make was to my "GetNeighbors" function. As I originally was only functioning at the lowest level of subdivision, finding the neighbors of an octant was simply a matter of finding the adjacent octants in the octree array. Now, however, it was possible for neighboring octants to be within the same parent, or to be at different levels of subdivision compared to a given octant.

Initially I spent some time attempting to address all the different edge cases one could encounter when trying to find the neighbors of a given octant, before realizing I had already prepared a simplier solution &mdash; finding neighboring octants by the location. I utilized my "GetOctantAtLocation" function, which already found the exact child that contained a point, so I could easily find octants within the same parent, as well as octants from different parents. I calculated an offset of the given octant's extent plus one, and then found the octants positioned at this offset in the six directions around the given octant.

However, this revealed another edge case, which I had read about in the [paper mentioned in my last blog post](http://www.gameaipro.com/GameAIPro3/GameAIPro3_Chapter21_3D_Flight_Navigation_Using_Sparse_Voxel_Octrees.pdf). When an octant's neighbor is at a higher level of subdivision, there are actually several neighboring octants. In the following illistration, the octant "13" has only one neighbor, "2", beneath it, but four neighbors, "6", "10", "12", and a fourth hidden one, to its right.

![Test](https://i.imgur.com/9E1fzUr.png)
[Image credit](https://www.semanticscholar.org/paper/Neighbor-finding-in-images-represented-by-octrees-Samet/d14d6d92d44929386f0c6dc1e710d0cbdccf564e)

To account for this, I added another recursive function to get all the children neighbors from a specific direction. As the children are all created in the same order, the indices of specific children within a parent are the same. For example, the bottom-right child is always index 0 within the "children" array of a parent. I iterated through the children at these indicies, check if they themselves had children, called the function again on those if so, and then added the highest-level children to the neighbors array.

After all that, I was able to accurately reteive the neighbors of a given octant!

![Test](https://i.imgur.com/NVgpuYW.png)

Finally, I wanted my pathfinding calculations to also be managed by my secondary thread. To do that, I would need a system of keeping track of active pathfinding tasks.

I created a "PathfindingTask" struct that encapsulated all relevant information of a given pathfinding task, such as its origin, destination, and an enum representing the result of the task (e.g., success, timed-out, failed, etc.). When a pathfinding request is scheduled, I create a task and add it to an "active pathfinding tasks" array. My secondary thread then iterates through all active tasks and ticks them. I also included a UPROPERTY a user can change to limit the number of tasks that can be ticked any given run of the secondary thread.

![Test](https://i.imgur.com/txmDvc1.png)

The actual pathfinding algorithm itself was switched from a greedy best-first to an A*. 

![Test](https://i.imgur.com/AKVJPTh.png)

Now, navmesh agents can to have their paths calculated by a secondary thread! I then built failure states ontop of this system.

I've started with two failure states: one for being unable to find a path, and another for a time-out.

![Test](https://i.imgur.com/EVJSDRc.png)
![Test](https://i.imgur.com/8hORWAV.png)

The time-out limit is another UPROPERTY users can adjust.

Although this week was primarily dedicated to backend changes to my algorithms, and thus visual demonstrations are limited, I am now fully able to navigate through my navmesh. Next week will be focused on finalizing this pathfinding algorithm and the handling of dynamic obstacles. I also hope to have my own behavior tree node users can use to have an agent fly to different locations within the navmesh.

Thank you for reading, and I'll see you next week!