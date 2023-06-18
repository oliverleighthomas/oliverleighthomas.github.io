---
layout: post
title: 6DOF Navmesh
description: A 6DOF navmesh plugin for Unreal
---

Source code can be found [here](https://github.com/oliverleighthomas/6DOF-Navmesh).

Summary
=======

In the Summer of 2023, the design team of my capstone project, "Deckweaver: Decent into Chaos," decided they wanted a floating-type enemy. After doing a little research, I realized that we had two options for how to accomplish this: fake the enemy's movement, something I feared would diminish players' overall experience, or implement a specialized navmesh that allowed for six degrees of freedom. Never one to shy away from a challenge and eager to learn more about AI pathfinding, I opted to create this navmesh myself.

The goal is to develop a navmesh that will allow our flying enemies to navigate through and around obstacles in a 3D space. Our environments feature a great many ruins our enemy could float through, keeping the player constantly on their toes. To increase accuracy and allow for tighter navigation around these obstacles, I am incorporating octree optimization in the navmesh generation process.

This project is still a work-in-progress, but you can read about what I've accomplished on my [blog](https://oliverleighthomas.github.io/blog/)! Every Sunday, I share progress updates and plans for further development.