---
layout: post
title: Community Care Training Sim
description: A training sim made for the VHA.
---

[ VIDEO ]

Source code for this project is not avalible, as it now belongs to the VHA.

Summary
=======

This project was created in Unity using C#, alongside a team of four others.

The goal of this simulation was to train providers at the Veterans Health Association through the process of approving or denying patients Community Care services.

The player begins the game by selecting an episode to play through. Each episode begins with a short quiz, which then moves into a scenario. 

Scenarios involve the player speaking with a paitent or otherwise someone with questions about the community care process. In the scenario my team and I created, the player spoke to another provider
as a consultant. The player is presented with dialogue choices, and they must choose which they believe is the most correct. Points are rewarded or deducted for these choices, and occasionally players
can encounter branching paths depending on the choice they picked. 

At the end of the scenario, players are shown how many points they scored. If they scored enough points, they can progress into the next episode. Otherwise, they must play the episode again.


My Responsibilities
===================

### Dialogue System ###

My primary responsibility on this project was the creation of the dialogue system. 

To accomidate the VHA's vision of the game, I created four different "types" of dialogue:
1. Conversational dialogue, which simply showed what the narrator or paitent was saying at that time.
2. Selection dialogue, which was when the player was given a choice of what to say.
3. Pop-up dialogue, used by the narrator to give the player more information either about choices or their next goal.
4. Scene changes, which were flagged as a type of dialogue to allow for easy switching of levels for branching paths.

All dialogue was managed by a central "dialogue manager," which kept track of the flow of dialogue and showed dialouge at appropriate times. For example, certain dialogue was locked behind required
goals, such as reading an email or checking the paitent's medical records. The dialogue manager would keep track of when these requirements were met or not met, and would progress dialogue appropriately.

As this project was going to be handed off to the VHA for continued development, it was important to ensure dialogue could be added as easily as possible. I decided to create a json file format 
future developers could utilize to easily create the dialogue-portion of episodes. In this json file, developers could edit things like the dialogue being said, options presented to the player
in the case of selection dialogue, what piece of dialogue would come next, and the requirements a piece of dialogue might have before becoming accessible.

[ PIC ]

### Level Progression System and Game Manager ###

Ontop of the dialogue system, I was also responsible for implementing a level progression system. The VHA wanted episodes to be locked initially, and unlocked once preceeding levels were completed.

To accomplish this, I created a game manager that kept track of the episodes, and would mark them as "avalible" once the preceeding episode was completed with the required number of points. 

This game manager was also utilized for initializing the quiz or dialogue at the beginning of every scene change, paitent animations, and audio. To ensure the game felt as realistic as possible,
the VHA requested the player be able to enter their name at the beginning of the game. This manager stored that game and replaced it in dialogue where appropriate.

### Interaction System ###

My final main responsibilty was setting up an interaction system. 

There were a few things in a scene the player would interact with, such as their computer or the paitent's case file. When interacted with, an appropriate UI was shown. The interaction system
also informed the dialogue manager of satisfied requirements. For example, when the case file was opened, it would broadcast an event the dialogue manager would receive, and the dialogue manager would
remove any "case file" requirements from the next line of dialogue.