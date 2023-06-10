---
layout: post
title: 6DOF Navmesh Week 0 - Initial Planning
---

Hello, and welcome to my blog! Over the next eleven weeks, I will be utilizing this space to share development diaries about my upcoming project: a 6DOF navmesh for the Unreal Engine.

### The Goal

The primary goal for this project is to satisfy a need for my capstone team. In our upcoming game, Project Phoenix, we have several different enemy types, including floating enemies. Initial discussions on the AI's movement suggested we simplify the enemy's movement, but that would create an enemy players found less engaging. Our game environments contain ruined walls and scattered debris a floating-type AI should be able to traverse. To ensure the quality of our game, I've decided to take on the challenge of creating a 6DOF navmesh to allow for complex enemy movement.
 
To ensure this system functions in a production environment, I am adhering to a few constraints. Firstly, my capstone team has many enemy types with diverse movement behaviors, such as walking and jumping. My navmesh system will either need to accommodate these types of movements, or integrate with Unreal’s built-in navmesh. Additionally, the game also contains environmental hazards, such as fire and steam, that modify the navmesh. My navmesh should be modified in the same way by these hazards. As a stretch goal, I aim to accommodate jumping AI without the need for nav links. Project Phoenix includes very few dynamic obstacles, but as a secondary stretch goal, I would like to include support for them.

Beyond being a useful system for my capstone team, I hope for this project to aid in my own personal development. I plan on investing extra effort into optimizations by looking into octrees and multithreading. Additionally, I will be improving my 3D math skills by creating the final pathfinding algorithm my navmesh will utilize.

### Schedule

This project will span eleven weeks, which I intend to break down as follows:

**Week 1 (05/22 - 05/28): Navmesh Generation and Representation**
* For the first week of the project, I will begin by creating the voxel grid that will be used by AI to navigate in a 3D space with 6DOF. This will include utilizing octree optimization for static obstacles.
* Additionally, I will implement visual representations of the navmesh, and debug keys to toggle these visualizations.

**Week 2 (05/29 - 06/03): Navmesh Modifiers and Minor Testing**
* This week will focus on modifying my navmesh generation to account for navmesh modifiers in the environment.
* This phase involves creating a very basic pathfinding algorithm to ensure AI moves around static obstacles and avoid navmesh modifiers where possible.

**Week 3 (06/05 - 06/11): Dynamic Obstacle Handling**
* The focus of this week will be to create and implement dynamically moving obstacles that alter the navmesh.
* To ensure this project can work in different settings, I may create both a navmesh that dynamically updates, and a navmesh that must be manually updated.
* The AI created in the previous week will also be utilized here for additional testing.
* As dynamic obstacle handling is a stretch goal, this week may be replaced with further iteration of navmesh generation as necessary.

**Week 4 (06/12 - 06/18): Pathfinding Algorithm**
* I will begin work on a more robust pathfinding algorithm this week.
* This will involve research into ideal algorithms, as well as options for path smoothing.
* Stress testing will be utilized to find the most optimized algorithm, and experimentation with multithreading will begin.

**Week 5 (06/19 - 06/25): Jumping AI**
* This week will involve creating an agent that will utilize the navmesh to jump as opposed to fly, eliminating the need for nav links.
* As this week is a stretch goal, this may be replaced with more focus on the pathfinding algorithm and optimizations.

**Week 6-7 (06/26 - 07/09): Testing, Iteration, and Refinement**
* Up until now, all testing will have been done in controlled environments. At this point, the navmesh will be brought into my capstone game for testing in a proper production environment.
* The two-week duration of this phase is to account for bugs and edge cases that may arise when several different types of AI are present, each with more complex behavior.
* During this time, I will be collaborating with the design team to make any minor changes to AI behavior to ensure engaging and realistic movement.
* Assuming my navmesh system is stable, these two weeks might also be spent testing in other projects with different constraints to verify compatibility with different environments.

**Week 8 (07/10 - 07/16): Large Scale Refinement**
* To account for the uncertainties found in any project, I've opted to allow a week for potential large-scale system refinement.
* This week will be used for additional major features, back-end reworks for stability, and addressing any major bugs that had appeared.
* If deemed unnecessary, this week will instead be dedicated to optimization-focused reworking, which will continue into the following week.

**Week 9 (07/17 - 07/23): Optimization**
* Utilizing strategies we learn in class, I will analyze the navmesh and pathfinding system and identify performance bottlenecks.
* I anticipate dynamic obstacle handling may have a large impact on performance. If I manage to complete this stretch goal, this will likely be a focus of this week.
* Wherever possible, I will create toggles for performance-intensive tasks.

**Week 10 (07/24 - 07/30): Documentation**
* As I wish to upload this project to the Unreal Asset Store, I will spend this week compiling comprehensive documentation, and ensure my codebase is well-commented.
* This documentation will include a read-me and potentially instructional videos for setting up the navmesh.
* To ensure set-up is easy and understandable, these documents will be sent to parties who are not familiar with my project for review.

**Week 11 (07/31 - 08/04): Upload to Unreal**
* I will research Unreal Asset Store standards and esure my project conforms properly.
* At this stage, the codebase will be converted into a plugin and uploaded to the Unreal Asset Store.
* I will also do small amounts of additional testing here to ensure the plugin functions as intended when downloaded.

### Conclusion

I am looking forward to not only creating a system my capstone team will be able to utilize to improve the quality of our game, but to improving my optimization and 3D math skills. Thank you for reading, and I look forward to sharing my progress next week!