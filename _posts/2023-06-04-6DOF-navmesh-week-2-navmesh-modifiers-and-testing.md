---
layout: post
title: 6DOF Navmesh Week 2 - Navmesh Modifiers and Testing
---

Week two of my 6DOF navmesh project has concluded, and I am very excited about my progress! This week, I was finally able to see my work in action.

The objective for this week was to create navmesh modifiers that would mark certain areas as of higher cost, so users could create areas to be avoided in their environments. In addition, I wanted to create a basic pathfinding algorithm that utilized my navmesh to navigate around obstacles in order to reach designated goal points. However, I decided to start this week by implementing one of the optimization strategies I discussed in my previous post.

After consulting with my advisor, I realized that ensuring my navmesh was optomized before testing began in a production environment should be a priority. I was already encountering issues with speed in a small testing space, which could mean the navmesh wasn't yet usuable in a full project. After planning out my tasks for this week, I concluded I had the time to implement one of the three optimization strategies I had identified.

### Optimization