---
layout: post
title: Deckweaver: Decent into Chaos
description: Get in the Box Studio's premier game
---

Source code will become avalible once the project is complete.

Summary
=======

Deckweaver: Decent into Chaos is a game currently in development from my capstone team, Get in the Box, on which I serve as lead programmer. It is a rougelite featuring deckbuilding gameplay
elements, fastpaced combat, and Lovecraftian enemies for the player to face. Players must complete three levels, defeating three trials in each level, before they are able to face the final boss,
and potentially save their world from the monsters invading it. However, the player won't get very far without becoming stronger &mdash; they must scour the environment for card chests so that
they may customize their decks.

Alongside my role as lead programmer, I also helped create the enemy AI for my team, as well as the interaction system and a game manager, used for stat-tracking, saving and reloading, and 
storing avalible cards.

Responsibilties
===============

### Leadership ###

As the lead programmer for the team, a large portion of my responsibilities involved keeping the backend of our game running as smoothly as possible. I enforced deadlines for builds before
presentations and other important due dates, maintained code standards to ensure quality across the entire code base, and conducted code reviews to confirm those standards were met. I was also
in charge of programmers' sprint planning, and would assign tasks on a biweekly basis. This involved hours-estimates, and ensuring the programmers working under me were getting ample opprotunities
to develop systems relevant to their career goals and interests.

As Deckweaver is a systems-heavy game, it was imperitive those systems were well-designed and easy for designers to interface with. I would lead systems design meetings whenever a designer
requested a new system to see how we could make it as quickly as possible, while still ensuring its flexibility and stability. One of these meetings lead to our very successful "card component"
system, a system that allowed designers to create cards on the fly by using pre-made "components" that programmers had made. For example, if a designer wanted an ice explosion card, a poison explosion
card, and a fire explosion card, all three cards could be made with one "explosion"-type component by changing the values. This allowed for designers to create cards without needing to overwhelm
programmers with additional tasks.

### Programming ###

Of course, I would not let my programmers have all the fun in-engine while I stood by the sidelines. Alongside my leadership duties, I developed the following:
1. The game's AI. Our game features 4 unique enemies, 3 of which have their own secondary variant, and a boss. The non-boss enemies are managed by an AI-manager. The purpose of the manager
was to keep enemies from all attacking at once, and to prioritize enemies in front of the player as opposed to behind. 
2. The interaction system. Throughout our world, the player can interact with card chests to expand their deck, and shrines to instigate trials. 
3. The game manager. This manager was in charge of several aspects of the game, such as stat-tracking and keeping a compendium of all avalible cards. 

More on these systems will be added to this page once the game is out, so please check back frequently!