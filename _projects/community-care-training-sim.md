---
layout: post
title: Community Care Training Sim
description: A training sim made for the VHA.
---

<iframe width="560" height="315" src="https://youtu.be/md8hy9OckxQ" frameborder="0" allowfullscreen></iframe>

Source code for this project is not avalible as it now belongs to the VHA.

Summary
=======

This project was created in Unity using C#, alongside a team of four others.

The goal of this simulation was to train providers at the Veterans Health Association in the process of approving or denying patients for Community Care services.

The player begins the game by selecting an episode to play through. Each episode begins with a short quiz, which then moves into a scenario. 

Scenarios involve the player speaking with a paitent or someone with questions about the community care process. In the scenario my team and I created, the player acts as a consultant for a provider. The player is presented with dialogue choices, and they must choose which they believe is the most correct. Points are rewarded or deducted for these choices, and occasionally players can encounter branching paths depending on the choice they picked. 

At the end of the scenario, players are shown how many points they scored. If they scored enough points, they can progress into the next episode. Otherwise, they must play the episode again.


My Responsibilities
===================

### Dialogue System ###

My primary responsibility on this project was the creation of the dialogue system. 

To accomidate the VHA's vision of the game, I created four different "types" of dialogue:
1. Conversational dialogue, which simply showed what the narrator or paitent was saying at that time.
2. Selection dialogue, where the player would be given a choice of what to say.
3. Pop-up dialogue, used by the narrator to give the player more information about choices or their next goal.
4. Scene changes, which were flagged as a type of dialogue to allow for easy switching of levels for branching paths.

All dialogue was managed by a central "dialogue manager" that kept track of the flow of dialogue and showed dialouge at appropriate times. For example, certain dialogue was locked behind objectives, such as reading an email or checking the paitent's medical records. The dialogue manager would keep track of when these requirements were satisfied and would progress dialogue appropriately.

As this project was going to be handed off to the VHA for continued development, it was a priority of mine to ensure the dialogue system could easily be built upon. I created a JSON file format future developers could use to write their own scenarios. 

[text](https://i.imgur.com/MT8z8qq.png)

This JSON is automatically parsed at the beginning of the corresponding episode, and turned into a playable scenario. Developers only need modify JSON values to give player dialogue choices, create branching paths, or play different animations on paitent models. This way, developers do not need to spend time coding new scenarios individually, and development time can be spent on expanding different systems.

### Level Progression System and Game Manager ###

On top of the dialogue system, I was also responsible for implementing a level progression system. 

The VHA wanted episodes to be locked initially and unlocked once preceeding levels were completed. To accomplish this, I created a game manager that kept track of the episodes, and would mark them as "avalible" once the preceeding episode was completed with the required number of points. 

This game manager was also utilized for initializing the quiz and dialogue at the beginning of new scenarios and scenes, paitent animations, and audio. To ensure the game felt as realistic as possible, the VHA requested the player be able to enter their name at the beginning of the game. This manager stored that name and replaced it in dialogue where appropriate.

### Interaction System ###

My final main responsibilty was setting up an interaction system. 

There were a few things in a scene the player would interact with, such as their computer or the paitent's case file. When interacted with, an appropriate UI was shown, and an event would be broadcast to the dialogue manager. The dialogue manager would then remove any relevant requirements from the next line of dialogue.