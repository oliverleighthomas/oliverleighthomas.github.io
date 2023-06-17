---
layout: post
title: 6DOF Navmesh
description: A 6DOF navmesh plugin for Unreal
---

Source code can be found [here](http://www.unexpected-vortices.com/sw/rippledoc/quick-markdown-example.html).

Summary
=======

In the Summer of 2023, my capstone project's design team decided they wanted a floating-type enemy in our game, Deckweaver: Decent into Chaos. After doing a little research, I realized that our
options were to either fake the enemy's movement, something I feared would detract from the players' overall experience, or to implement a specialized navmesh that allowed for six degrees of
freedom. Never one to shy away from a challenge, and eager to learn more about AI pathfinding, I opted to create this navmesh myself.

The goal is to develop a navmesh that will allow our flying enemies to navigate through and around obstacles in a 3D space, as our environments feature a great many ruins for 
the enemy to float through. To increase accuracy and allow for tighter navigation around these obstacles, I am also utilizing octree optimization to help visualize the navmesh space. 

This project is still a work-in-progress, but you can read about what I've accomplished on my [blog]()! The blog is updated every Sunday with new progress and plans for the next week.