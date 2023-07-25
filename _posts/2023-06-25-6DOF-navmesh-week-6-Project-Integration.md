---
layout: post
title: 6DOF Navmesh Week 6 - Integration
---

Welcome to week 6 of my six degrees of freedom navmesh project! I'm happy to share that I have solved the issues I was having last week and have now integrated my navmesh into my capstone team's game.

### Path Smoothing Revised

To start this week off, I decided to try and resolve the issue I was having last week. Last week, I realized that I would need to devise an algorithm to find which octants a smoothed path traversed through, as the smoothing process caused the path to deviate from the original relevant octants. 

I brought the issue to my professor, who identified an issue in my original logic. Last week, I showed images of potential paths an agent might find and how it would smooth them:

![test](https://i.imgur.com/WBYJIiN.png)
![test](https://i.imgur.com/rq6xXKk.png)

My professor pointed out that the smoothed path I showed here would not be possible. If it were, the agent would have navigated through those octants, as opposed to the octants it choose. A smoothed path should go through the octants it originally found.

I opted to redo the way I smoothed paths. Instead of removing positions from the path, I decided to use each octant center as a control point for a Bezier curve. This ensured the agents wouldn't move in straight lines from octant to octant, and would preserve the relevance of the octants found in the pathfinding algorithm.

The performance between this method and my previous method was negligible, something I was concerned about. I also find these new paths more aesthetically pleasing than the original smoothed paths!

![test](https://i.imgur.com/WSCJ1yF.gif)

With this problem resolved, I was finally able to add proper handling for dynamic collisions along a path:

![test](https://i.imgur.com/FT79XTp.gif)

Now, I was able to move on to integration!

### Initial Integration Attempt

During the initial integration process, I encountered three issues:
1) Actors did not properly ignore themselves when checking for dynamic collisions.
2) Following a goal that moves very rapidly or erratically, such as the player character, proved to be difficult for agents.
3) Initial loading of the navmesh takes a very long time, sometimes several minutes.

These issues needed to be resolved before I could properly integrate the navmesh. I opted to start with the initial loading time of the navmesh.

### Lazy-Loading

Before this point, I had already debated implementing lazy-loading of collision data. However, it had not been a priority before, as the area I was testing in was relatively small. My capstone team's maps, however, were very large and had dozens of obstacles the navmesh needed to account for, and the need for lazy-loading was now abundantly clear.

I removed the function call that subdivided octants when the navmesh was originally generated. Now, a 3D array was filled with octants, but those octants had no sense of their navigability, nor were they subdivided into children. The function call to subdivide an octant was instead moved into the "FindOctantAtLocation" function, thus making it so an octant was only subdivided when it became relevant. This very dramatically increased the speed at which a navmesh was generated.

![test](https://i.imgur.com/0rXEA31.png)

The "FindOctantAtLocation" is primarily utilized by my "CalculatePath" function, where a path is found through the navmesh. After these changes, octants are subdivided as they are found by the pathfinding algorithm. This severely slowed down the pathfinding process. Whereas once paths were near-instantaneous to find, it could now take an agent several seconds to both subdivide all relevant octants and then find a path through them.

To mitigate this slowdown, I parallelized the subdivision algorithm, allowing children to be found and subdivided concurrently. Additionally, I added an event that would be triggered once all the children had properly been initialized to prevent agents from receiving octants that were not yet fully subdivided.

![test](https://i.imgur.com/DX0Dtc7.png)

With this, the speed of my pathfinding algorithm was nearly restored!

![test](https://i.imgur.com/QYRDvpz.png)

The pathfinding algorithm is still slower than before, but the trade-off is worth the major improvement in the navmesh generation speed. In the future, I plan on creating a task that will go through all the octants and subdivide them on my worker thread while the game is running. This way, the onus of subdividing the navmesh is not entirely reliant on pathfinding, and pathfinding requests will be completed faster as the game goes on.

Next, I handled the issue where agents could trigger their own dynamic collision events.

### Dynamic Collision Adjustments

Planning how I wanted to accomplish this was a bit tricky. Agents navigating through a navmesh still needed to affect it, or else agents would potentially collide with one another. However, if an agent marked an octant it was trying to move through as non-navigable, then it would no longer move through it and get itself stuck.

To resolve this problem, I added a check to see if an octant was only overlapping with the agent trying to navigate through it. When checking the collision of an octant, I save the overlapping actors into an array stored on each octant. Then, when pathfinding or checking for dynamic collisions, I check if the octant is marked as non-navigable AND if it is overlapping with more than just the agent on the path.

![test](https://i.imgur.com/nrLfy4y.png)
![test](https://i.imgur.com/3OeY6QE.png)

This successfully fixed the issue, agents now no longer trigger their own dynamic collisions but are still capable of affecting the navmesh! 

Around this time, I also updated the way I handle actors to ignore. Instead of making it so designers need to select objects within a scene and add them to a TArray, I created a tag that can be added to blueprints. Anything with this tag will be ignored by the navmesh.

### Conclusion

Finally, my navmesh is now working within my capstone team's game!

[ VIDEO ]

I'm very excited to see my navmesh in a production environment, but there are still several tweaks I'd like to make, including handling moving destinations. Next week will be focused on ensuring my navmesh continues to work with my team's game as intended, and on fine-tuning. 

Thank you very much for reading, and I'm looking forward to sharing more updates next week!