---
layout: post
title: FIEA Game Engine
description: An educational game engine
---

View the source code [here](https://github.com/oliverleighthomas/FIEA-game-engine/).

Summary
=======

Over the course of my second semester at FIEA, I was tasked with building my own game engine. This involved creating my own container classes meant to replicate
those of the C++ standard library, imlementing an RTTI and event system, to creating a parser for a JSON scripting language to allow users to create their
own games.

At the end of the semester, I lead a team of eight programmers and recreated Flappy Bird utilizing this engine. As the engine we were using was my own, I was tasked 
with further development and maitenance, and created an object to render 2D sprites and an action that played audio.

Overview
========

### Containers ###

The game engine makes use of five different containers:

1. Singly-Linked List: A templated singly-linked list with a forward iterator
2. Vector: A container meant to mirror the `std::vector` class, with a random-access iterator
3. HashMap: A container meant to mirror the `std::unordered_map` class, with a forward iterator
4. Stack: A wrapper for the "singly-linked list" class, meant to act as a basic stack.
5. Datum: A class that stores an array of values, the type of which is determined at runtime utilizing a discriminated union.

These containers were all created from scratch, so memory management was a key focus of mine. I also wanted my containers to be highly configurable to a user's specifications, and so
I included the ability to provide custom functors for things such as memory allocation, equality checks of stored data, and hashing.

### Dynamic Hierarchical Database ###

Ontop of these simple containers, I created a system to mimic classes in C++. 

This started with a "scope" class, which contained a vector of string/datum pairs. These scope objects were essentially tables of data, and that data could include other scopes. This allowed
for the easy creation of user-defined types. At this point, I had a recursive data structure that represented nested objects, similar to XML or JSON. However, the game engine was meant to be more
than a representation of data. I wanted to be able to represent data in a way that mirrored C++'s classes.
 
An "attributed" class was built ontop of "scope." This class was essentially a scope with support for perscribed attributes, or properties. When created, a scope/datum pair was created to represent
the data of an attributed object. For example, if we had the following C++ class:

~~~
struct Foo final
{
     int _health{ 0 };
     float _damagePerSecond{ 0.0f };
};
~~~

We would want this data to be easily accessible in a scope/datum structure. An attributed object would populate a scope with information about the health and damage per second of the Foo object. 

At this time, I also created a "TypeManager" singleton that managed the type-registration of derived attributed classes.

With these classes, the engine could now express the intent of creating objects. To actually accomplish this, I implemented an abstract factory design pattern through use of a templated "Factory" abstract 
class. The game engine utilized this factory to create objects by their typename. 

### Attributed Derived Classes ###

There were two "attributed" derived classes needed to make my game engine function similarly to a proper game engine.

First, I created a "GameObject" class. This was simply a wrapper for an attributed object, however GameObjects would automatically populate their tables with data at compile-time.

Second, I created an "Action" class. Actions were meant to mirror functions in C++, and would be updated alongside their parent GameObject. For my engine, I created a sample "ActionIncrement" action,
which took in a Datum that represented either an int, and then incremented it by a user-defined value.

### Event System ###

A templated event system was also implemented into the engine, allowing users to make use of the observer design pattern. This involved the creation of three classes:

1. An "event subscriber" abstract class, which would allow objects to be subscribed to events.
2. An "event publisher" class, which was used to deliver the payload to subscribers.
3. An "event" template, which held a list of subscribers and a templated payload. Through this class, subscribers could be subscribed or unsubscribed, and notified of a payload when it was ready.

### Parsing ###

Finally, to allow users to create games within my game engine, I created a JSON parsing system. This system made use of the "chain of responsibility" pattern by using helpers and a central
co-ordinator to read through a JSON file and parse out information. This information was then turned into GameObjects that could have actions used upon them, be subscribed to events, or 
otherwise have their data accessed by the engine to allow for manipulation.