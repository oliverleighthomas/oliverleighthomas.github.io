---
layout: post
title: 6DOF Navmesh Week 2 - Navmesh Modifiers and Testing
---

Week two of my 6DOF navmesh project has concluded, and I am very excited about my progress! This week, I was finally able to see my work in action.

The objective for this week was to create navmesh modifiers that would mark certain areas as of higher cost, so users could create areas to be avoided in their environments. In addition, I wanted to create a basic pathfinding algorithm that utilized my navmesh to navigate around obstacles in order to reach designated goal points. However, I decided to start this week by implementing one of the optimization strategies I discussed in my previous post.

After consulting with my advisor, I realized that ensuring my navmesh was optomized before testing began in a production environment should be a priority. I was already encountering issues with speed in a small testing space, which could mean the navmesh wasn't yet usuable in a full project. After planning out my tasks for this week, I concluded I had the time to implement one of the three optimization strategies I had identified.

### Optimization

The strategy I decided to implement to start was the check whether octants were entirely occupied and, if so, cease further subdivision. The remaining two strategies—multi-threading and lazy-loading of collision data—required a bit more work to set up, but for this particular strategy, I already had everything I needed.

I began by creating an algorithm to determine what percentage of an octant was occupied. Unreal's collision shapes are binary in nature (either they are occupied or empty) and do not store any information about the extent of their occupancy, so I needed to find my own way of gathering this information.

I began with a naive implementation of the algorithm. If an octant was found to be occupied, I divided that octant into ten divisions along each axis, so one thousand parts total. A collision shape was created for each part, and then checked for intersection with the collision shapes of the overlapping actors within the octant. If an intersection was found, I incremented a tally of all occupied sub-octants. After going through all sub-octants, I divided this value by the total number of sub-octants (in this case, one thousand) and then multiplied by one hundred to determine the percentage of the octant that actually contained overlapping actors. This percentage was then returned. Within my subdivision method, I would check that percentage against a user-defined threshhold (in my testing, I used 80%), and only subdivided further if it was not met.

![Test](https://i.imgur.com/UiEs0TM.png)

After implementing my algorithm, I ran a test to see how much faster it was. First, I created a navmesh without utilizing my new algorithm:

![Test](https://i.imgur.com/zDdcLe4.png)

Then, I repeated the test on my new algorithm:

![Test](https://i.imgur.com/1dh8UXh.png)

As one could imagine, these were not the results I was hoping for. My algorithm significantly slowed down the navmesh generation process.

After evaluating my algorithm, I implemented three main changes to aid in optimization:

1. I realized that dividing the octant into one thousand parts was exessive. I changed this to 125, as I found that was still sufficiently accurate when calculating the percentage of occupancy, but significantly cut down on the number of iterations through the loops.
2. I introduced an early exit that occured if the number of occupied sub-octants reached the user-definied threshold of full ocupancy. The purpose of my algorithm was not to fully calculate the percentage of the octant that was occupied, it was simply to determine if it was fully occupied or not. In this same vein, I removed the final calculation of the percentage, and simply returned true if the octant was fully occupied, and false if it was not.
3. I replaced the FBox::Intersect function provided by Unreal with my own. Comparitive testing revealed that the FBox::Intersect method was slower than manually comparing the bounds of the overlapping actors to each sub-octant. It is worth noting that with my custom method, the sub-octant must be fully encompassed by the overlapping actor. However, the slight loss in accuracy did not impact results.

![Test](https://i.imgur.com/tQuJGIh.png)

I compared the speed of my old algorithm against my new one, and was much happier with the results:

![Test](https://i.imgur.com/QWevt56.png)

The improved algorithm is now notibly faster than letting the octree unnecessarily subdivide itself! Now that this system is implemented, I moved on to the development of a pathfinding algorithm.

