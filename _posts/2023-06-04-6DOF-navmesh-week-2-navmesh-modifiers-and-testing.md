---
layout: post
title: 6DOF Navmesh Week 2 - Navmesh Modifiers and Testing
---

Week two of my 6DOF navmesh project has concluded, and I am very excited about my progress! This week, I was finally able to see my work in action.

The objective for this week was to create navmesh modifiers that would mark certain areas as of higher cost, so users could create areas to be avoided in their environments. In addition, I wanted to create a basic pathfinding algorithm that utilized my navmesh to navigate around obstacles in order to reach designated goal points. However, I decided to start this week by implementing one of the optimization strategies I discussed in my previous post.

After consulting with my advisor, I realized that ensuring my navmesh was optomized before testing began in a production environment should be a priority. I was already encountering issues with speed in a small testing space, which could mean the navmesh wasn't yet usuable in a full project. After planning out my tasks for this week, I concluded I had the time to implement one of the three optimization strategies I had identified.

### Optimization

The strategy I decided to implement to start was the check whether octants were entirely occupied and, if so, cease further subdivision. The remaining two strategies &mdash; multi-threading and lazy-loading of collision data
&mdash;
required a bit more work to set up, but for this particular strategy, I already had everything I needed.

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

### Initial Pathfinding Algorithm

Now that I can generate the navmesh more efficiently, it was time to test pathfinding through it!

As the initial algorithm would be for testing purposes, I opted to go for a simpler approach that I could ideally get working quickly. However, as I'd be testing navmesh modifiers, the algorithm still needed to account for heuristics. Therefore, I opted to begin with a naive best-first search.

First, I to find which octant was associated with the FVector location a user would provide as the destination. To accomplish this, I needed to make a few minor adjustments to the navmesh generation process.

Originally, my navmesh was a 1D TArray of octants, which did not allow for easy access of specific octants. I instead switched to a 3D array of octants, and restructured my GenerateNavmesh function to utilize nested loops to ensure each octant was placed in a location in the array that corresponded to its location in the navmesh.

![Test](https://i.imgur.com/284zPcA.png)

After that, I created a function to calculate the x, y, and z indices of a desired octant based on a provided location.

![Test](https://i.imgur.com/jMZOZ66.png)

I was then able to accurately determine which octants my starting and ending points existed within. The center points of these octants is shown in red in the following image:

![Test](https://i.imgur.com/pIYnx5G.png)

To proceed further, I needed to set up a prioritiy queue class, as Unreal does not have one built-in. I created a wrapper to a TArray that used Heapify, HeapPush, HeapPop, and HeapTop to access and modify data. This way, I would be able to organize octants properly by cost. Initially I debated the time versus value ratio of setting up my own priority queue class, but chose to do so in the end as I knew it would prove useful later, when I created a proper algorithm.

Finally, I needed a method to find the neighbors of a given octant. 

At this time, I did some research on determining neighbors within octrees, as not all octants are of the same size. The paper I read, ["3D Flight Navigation Using Sparse Voxel Octrees,"](http://www.gameaipro.com/GameAIPro3/GameAIPro3_Chapter21_3D_Flight_Navigation_Using_Sparse_Voxel_Octrees.pdf) provided an outline of how this might be acheived. However, due to the limited amount of time I had remaining for the week, I implemented a much more simple solution that only returned neighbors at the lowest level of the octree. Although this is sufficient for testing purposes, I will need to rework this function to be more efficient and accuratein the future.

![Test](https://i.imgur.com/Z0u9izi.png)

With the all prerequisities complete, I began working on the algorithm itself. The best-first algorithm looks at all neighboring octants and determines which is the most promising based on its cost. For my naive implementation, I assigned each octant a cost based on their distance from the destination, with greater distances being a greater cost.

Currently, this algorithm does not handle several edge cases a proper algorithm would need to account for. However, at this point I was ready to begin testing!

### Testing

Before creating and testing with modifiers, I decided to do an initial test to see if I could calculate a path from an actor to an arbitrary point in front of it. 

My initial run didn't look exactly as planned:

![Test](https://i.imgur.com/mi2mVkR.gif)

It seemed like each octant's cost was not being properly factored into the pathfinding algorithm. I evaluated my prioritiy queue and found this was caused by calling "Top" as opposed to "HeapTop" within my "Top" function. I corrected this error and tried again.

Fortunately, it worked!

![Test](https://i.imgur.com/DTUOMKj.gif)

Assured that my navmesh could be utilize for pathfinding, I began work on the modifiers.

### Modifiers

As I mentioned in my first blog post regarding this project, my capstone team utilizes navmesh modifiers to ensure enemies avoid hazards such as lava, fire, and poison pools. Flying type enemies utilizing the navmesh I was creating would also need to navigate around these hazards, and so it was important to set up my own system of modifying the navmesh.

I initially attempted to utilize Unreal's built-in modifiers. Users would already be familiar with these tools, and I imagined that having to place two modifiers in an area, one for walking enemies and another for flying enemies, would be tedious. However, my research revealed it was not possible to simply retrieve all navmesh modifiers in a level. I will continue to research this further, but it seemed like what I would need to do was query a default navmesh for modifiers, a solution that would be computationally complex and would require tedious set up for the user. Not all users would be utilizing one of Unreal's default navmeshes while using mine.

Instead, I decided to create my own navmesh modifier actor, with a UPROPERTY to indicate the cost modifier to be applied to points within it. This actor was also given a bounds volume, similar to my navmesh, enabling users to visualize the space that would be affected. Next, I needed to apply the modified cost to my navmesh.

I did a bit of research on the topic, and opted to modify an octant's cost during path calculation rather than during an octant's creation. While this makes the pathfinding algorithm computationally more expensive, it would make it easier to implement dynamic modifiers. I plan on adding support for dynamic obstacles next week, and this implementation allows for easier integration.

When generating my navmesh, I query for all navmesh modifiers in the scene and store them in a TArray. Then, when calculating a path, I check to see if the octants fall within any modifiers and, if so, apply the cost modification.

![Test](https://i.imgur.com/jHtyuZk.png)
![Test](https://i.imgur.com/zyEPSdH.png)

I placed a modifier into my scene, and functions perfectly!

![Test](https://i.imgur.com/Xy5CFvb.gif)

### Conclusion

I'm very exicted to see how my navmesh is coming along. Being able to see actors utilizing it to calculate paths is a big step forward, but I know that my pathfinding algorithm is not quite where it needs to be. Nevertheless, it is sufficient for testing, so next week I'll be moving forward with accomidating dynamic obstacles. Thank you for reading, and I am eager to share further progress next week!