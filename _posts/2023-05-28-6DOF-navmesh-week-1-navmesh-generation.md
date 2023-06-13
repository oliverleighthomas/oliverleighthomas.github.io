---
layout: post
title: 6DOF Navmesh Week 1 - Navmesh Generation
---

I have successfully completed my first week of work on the 6DOF navmesh project! I am pleased to share that I have accomplished all the objectives outlined in my week one schedule, as well as some supplementary research. This involved generating the navmesh and subdividing it into octrees as necessary. 

### Initial Generation

The week, I began by setting up my Unreal project. As the focus was solely on navmesh generation, I decided to utilize the basic first-person map provided by Unreal, which already contains plenty of static obstacles.

My initial task was to define the spatial boundaries of the navmesh. I decided to make an actor with a box bounds object. The box bounds would represent the bounds of the navmesh.

![Test](https://i.imgur.com/roq2LLW.png "The outline of a cube in an empty game level.")

I now needed to populate this space with voxels. Initially, I ignored collision and focused solely on developing the algorithm for generating voxel locations and sizes.

The initial attempts... weren't entirely correct.

![Test](https://i.imgur.com/VnGimfU.png "Several small, yellow cubes floating in the sky.")

The above image was the result of improper loop incrementation. Initially, my algorithm assumed the loop should iterate up to the size of the volume, rather than through the number of voxels that could fit in each dimension. I incremented the loop variables by the desired voxel size factor.

![Test](https://i.imgur.com/S5IrCgC.png "Incorrect code for a navmesh generation function.")

Additionally, I had a misconception regarding the extent of a box, assuming it was the full size of the box. In reality, the extent refers to only half of the size.

I spent some time visualizing the issue and adjusted my algorithm accordingly.

![Test](https://i.imgur.com/W6cC288.png "Corrected code for a navmesh generation function.")

This time, the result was perfect!

![Test](https://i.imgur.com/fYtHXRj.png "A single cube divided into 27 parts.")

It is important to note that if the size of the navmesh volume is not perfectly divisible by the voxel size, I chose to round up instead of rounding down. Potential users agreed that it was preferable for the navmesh to extend slightly beyond the desired area rather than falling short of fully encapsulating it.

Now that I was able to generate the voxels, the next step was to detect obstacles.

### Detecting Collision

After conducting research, I discovered two potential methods for collision detection: sweeping for hits and checking for overlaps within each voxel. According to the information I gathered, sweeping for hits is more computationally expensive than checking for overlap events. However, overlap events also flag objects like trigger boxes as non-navigable. I decided to explore the efficiency of both methods.

I significantly increased the size of the navmesh to obtain a large sample size and proceeded to set up the necessary components in my code for collision sweeping.

![Test](https://i.imgur.com/vpl85yb.png "Variables for collision query parameters.")
![Test](https://i.imgur.com/ryQKoSw.png "A variable for an array of collision channels.")

I populated the object query parameters with the collision channels specified by the user and performed both hit and overlap sweeps by object. This preliminary test confirmed sweeping for overlaps was faster than hits. 

It took approximately 15 seconds to sweep for hits:
![Test](https://i.imgur.com/hkYeSxe.png "A log of the time it took to sweep for overlaps. 32768000 octants were generated in 15.212 seconds.")

And thirteen seconds to sweep for overlaps:
![Test](https://i.imgur.com/3Y22D58.png "A log of the time it took to sweep for overlaps. 32768000 octants were generated in 13.117 seconds.")

Not only that, but the test demonstrated that sweeping for hits also identified trigger boxes as collidable objects.

![Test](https://i.imgur.com/YkPXnWj.png "A cube made up of several smaller green cubes. Several cubes in the center are colored red, indicating they are being collided with.")

Considering these findings, I decided to employ overlap detection rather than hitscan. However, I needed a mechanism to exclude trigger boxes from being marked as collidable. I added another TArray UPROPERTY to the code so users could specify actors to ignore during sweeping, and then added these actors into the query parameters.

I can now identify whether octants are navigable based on the presence of overlapping objects.

![Test](https://i.imgur.com/Fjyw89f.png "A voxel grid covers part of the game scene. The majority of the grid is green, but areas where the grid collides with obstacles are red. The grid is colliding with a wall and a gun.")

This looks especially cool when the navmesh encompasses the entire map!

![Test](https://i.imgur.com/cpQrWxU.png "A voxel grid covers the whole of the game scene. The majority of the grid is green, but areas where the grid collides with the floors and walls are red.")

### Octree Optimization

Finally, it was time to implement octree optimization to enhance the accuracy of the navmesh. As shown in earlier images, even the slightest overlap marked entire octants as non-navigable, which could make it so NPCs move in unnecessary ways.

To accomplish this, I introduced a recursive "SubdivideOctree" function, which I invoked on each octant as it was created.

![Test](https://i.imgur.com/8LqqVoV.png "An algorithm for subdividing voxels into eight parts.")

In this implementation, I utilized bitwise operators to determine if a number was even or odd, as they offer better performance compared to modulos. I also included early exit conditions for octants that do not collide with any objects, as breaking down empty spaces is unnecessary.

With these modifications, I can now subdivide my navmesh to ensure better accuracy! Here is a real-time demonstration:

![Test](https://i.imgur.com/s0tB6CX.gif "A gif showcasing a grid being subdivided as it collides with objects in the scene.")

However, I have already identified some issues with my algorithm. The initial load and subdivision operations are incredibly slow. To address these concerns, I have the following ideas:
1. In addition to checking if an octant has no collisions, I will check if it is entirely occupied. If an octant is completely filled, further subdivision becomes unnecessary since no space within the octant should be accessible.
2. I intend to explore the possibility of lazy-loading collision data as needed, instead of attempting to load the entire grid on game startup. This approach should lessen the heavy loading times at the beginning.
3. The octant generation and subdivision could utilize multi-threading to improve performance.

Although I have allocated a week dedicated to optimization in my schedule, it is scheduled after testing in projects. However, I believe it is crucial to address these issues before testing the navmesh in a production environment. I plan to incorporate optimization research and implementation whenever time allows over the next few weeks.

### Functions for Toggling Visualizations

With these plans finalized, I began work on the debug visualization of the navmesh. Since I had already been utilizing several functions to draw octants for my own testing, I simply consolidated them into a single UFUNCTION flagged as blueprint callable. This enables users to define their own key bindings. However, drawing the entire navmesh places a significant burden on the GPU, particularly for larger navmeshes. In the future, I may rework this function to draw only sections of the navmesh near the player character, or around user-defined obstacles.

### Conclusion

For now, I am extremely satisfied with the progress of my navmesh. I eagerly look forward to starting next week's tasks, which involve implementing modifiers and conducting tests with behavior trees.

Thank you for taking the time to read this update!