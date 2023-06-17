---
layout: post
title: Deckweaver Decent into Chaos
description: Get in the Box Studio's premier game
---

Source code will become avalible once the project is complete.

Summary
=======

Deckweaver: Decent into Chaos is a rougelite game currently under development by my capstone team, Get in the Box Studios, on which I serve as lead programmer. The game features deckbuilding gameplay
elements, fast-paced combat, and Lovecraftian enemies for the player to face. To save their world from the invading monsters, players must complete three levels, each consisting of three shrines, and confront a final boss. However, the player won't get very far without becoming stronger &mdash; they must scour the environment for card chests so that they may customize their decks.

Responsibilties
===============

### Leadership ###

As the lead programmer for the team, a large portion of my responsibilities focused on ensuring the game functioned smoothly. This involved enforcing deadlines for builds prior to presentations and other important milestones, maintaining code standards to ensure a high-quality codebase, and conducting code reviews. I was also in charge of programmers' sprint planning, and would assign tasks on a biweekly basis. This involved making time-estimates for tasks, and ensuring the programmers were getting ample opprotunities to develop systems relevant to their career goals and interests.

As Deckweaver is a systems-heavy game, it was imperitive those systems were well-designed and easy for designers to interface with. Prior to implementing a new system, I would lead back-end design meetings, where the programmers and I would decide upon a system design that could be done quickly, while still maintaining flexibility and stability. One of these meetings lead to our highly successful "card component" system. This system allowed designers to create cards on the fly by using pre-made "components" already prepared by programmers. For example, if a designer wanted an ice explosion card, a poison explosion card, and a fire explosion card, all three cards could be made with one "explosion"-type component by modifying the values. This allowed for designers to create cards without needing to overwhelm programmers with additional tasks.

### Programming ###

Alongside overseeing the team's progress, I also took an active role in the development of the game. I worked on the following:
1. Enemy AI: Our game features four unique enemies, three of which have their own secondary variant, as well as a boss. The non-boss enemies are managed by an AI-manager, the purpose of the which was to help ensure the player was never too overwhelmed. Before an enemy is able to attack, it must request an "attack token" from the manager. There is a finite number of these tokens, and priority is given to enemies standing in front of the player. 
2. Interaction System: Throughout our world, the player can interact with card chests to expand their deck, and shrines to instigate trials. This system includes not only an "interact" feature, but also a "hover" feature, allowing players to easily see what is and is not interactable within their environment.
3. Game Manager: This manager was in charge of several aspects of the game. Its initial purpose was to keep a compedium of all cards avalible to the player, so that chests would know what they could and could not present. Not all cards within the game are accessible on the first run or within the first level. Later, this manager was further expanded to include stat-tracking. At the end of the game, players are presented with statistics about things such as how many enemies they've killed, how much damage they did, and the number of cards they picked up. Finally, this game manager is utilized in saving and reloading a game, and helping ensure a player maintains their card decks and power-ups between level transitions.

More details about systems will be added to this page upon the game's release, so please check back frequently!