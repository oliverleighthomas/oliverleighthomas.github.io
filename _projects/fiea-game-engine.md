---
layout: post
title: FIEA Game Engine
description: An educational game engine
---

View the source code [here](https://github.com/oliverleighthomas/FIEA-game-engine/).

Summary
=======

During my second semester at FIEA, I was tasked with building my own game engine. This involved creating custom container classes to replicate those in the C++ standard library, imlementing an RTTI and event system, and developing support for a JSON scripting language that enabled users to create their own games.

At the end of the semester, I led a team of eight programmers in recreated Flappy Bird using this engine. As the engine we were using was my own, I was responsible for its further development and maitenance. At this time, I added features such as a 2D sprite rendering object, and an action for playing audio.

Overview
========

### Containers ###

The game engine utilizes five different containers:

1. Singly-Linked List: A templated singly-linked list with a forward iterator
2. Vector: A container meant to mirror the `std::vector` class, with a random-access iterator
3. HashMap: A container meant to mirror the `std::unordered_map` class, with a forward iterator
4. Stack: A wrapper for the singly-linked list class, meant to act as a basic stack.
5. Datum: A class that stores an array of values, the type of which is determined at runtime using a discriminated union.

These containers were all created from scratch, down to their memory management. I wanted my containers to be highly configurable, and so I implemented support for custom memory allocation, equality, and hash functors users can provide.

### Dynamic Hierarchical Database ###

Ontop of these simple containers, I created a system to emulate classes in C++. 

This started with a "scope" class, which contained a vector of string/datum pairs. These scope objects were served as data tables, with the ability to store other scopes. This enabled easy creation of user-defined types. With this, I could represent nested objects in a similar fashion to XML or JSON. However, the game engine was to go beyond simply representing data.
 
On top of the scope class, I built an "attributed" class that supported perscribed attributes, or properties. When an attributed object was created, it would work alongside a "TypeManager" singleton to register types and populate a scope with data representing its attributes. For example, if we had the following C++ class:

~~~
struct Foo final
{
     int _health{ 0 };
     float _damagePerSecond{ 0.0f };
};
~~~

A scope with two datums would be created, one with the type "int" and name "_health," storing the value 0, and another with the type "float" and name "_damagePerSecond," storing the value "0.0f." These values can then be easily accessed in modified within the engine. 

The engine could now express the intent of creating objects, but not yet fully create them. To actually accomplish this, I implemented the abstract factory design pattern through use of a templated "Factory" abstract class. The game engine utilized this factory to create objects by their typename. 

### Attributed Derived Classes ###

Two attributed derived classes were now needed to make my game engine function similarly to a proper game engine.

First, I created a "GameObject" class. This was a wrapper for an attributed object, with the only difference being GameObjects would automatically populate their tables with data at compile-time.

Second, I created an "Action" class. Actions resembled functions in C++ and were updated alongside their parent GameObject. For my engine, I created a sample "ActionIncrement" action, which took in a int-type Datum and then incremented it by a user-defined value.

### Event System ###

The engine also featured a templated event system, allowing users to make use of the observer design pattern. This involved the following three classes:

1. An "event subscriber" abstract class, which would allow objects to be subscribed to events.
2. An "event publisher" class, responsible for delivering the payload to subscribers.
3. An "event" template, which held a list of subscribers and a templated payload. Through this class, subscribers could be subscribed or unsubscribed, and notified of a payload when it was ready.

### Parsing ###

To allow users to create games within my game engine, I implemented a JSON parsing system. This system followed the "chain of responsibility" pattern by using helpers and a central coordinator to read through a JSON file and parse out information. This information was then transformed into GameObjects that could have actions used upon them, be subscribed to events, or otherwise have their data accessed by the engine to allow for manipulation.