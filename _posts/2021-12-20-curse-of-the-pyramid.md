---
layout: post
title: "Curse of the pyramid"
featured-img: CurseOfThePyramid
categories: [school, Unreal Engine, C++]
published: true
summary: "Game projects game"
---

# Curse of the Pyramid

Curse Of The Pyramid is a single-player hack and slash, dungeon crawler, bullet hell roguelike with a unique twist: Soul Mode.
In Soul Mode you play as the soul version of yourself, not only having access to a different ability set but also you are able to interact with special gameplay elements. As the enemies are making also use of this system, you have to make quick decisions in your fighting style to make it out of every room alive. 


<p align="center"><iframe src="https://www.youtube.com/embed/us-wrjvyLB4" frameborder="0" 
allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>


Group Project – 3rd Year, [Digital Arts and Entertainment](https://www.digitalartsandentertainment.be/){:target="_blank"}
Shortlisted for the [Grads In Games – Student Game (Group) Award 2022](https://gradsingames.com/news/grads-in-games-awards-2022-the-shortlist/){:target="_blank"}.

Curse of the Pyramid was a third-year group project developed as part of the Digital Arts and Entertainment curriculum. The game combined exploration, puzzle-solving, and combat within a semi-randomly generated dungeon environment.


The project was developed by: 
- [Enrique Brossé](https://www.linkedin.com/in/enrique-brosse/){:target="_blank"} (Back-end, Tool, Optimization)
- [Steven Serra Mock](https://www.linkedin.com/in/stevenserramock/){:target="_blank"} (Director, Main Game Design, LookDev, Tech Art,  Character, VFX, SFX, Soundtrack-Composition)
- [Silke Van Der Smissen](https://www.linkedin.com/in/silke-van-der-smissen-41079920a){:target="_blank"} (Environment, Prop, LookDev, Tech Art, Lighting)
- [Bente Schoone](https://www.linkedin.com/in/benteschoone/){:target="_blank"} (Character, Rigging, Animation, UI, Prop, Cinematography)
- [Judith Verdonck](https://www.linkedin.com/in/judith-verdonck-33905b221){:target="_blank"} (Gameplay, AI, Optimization)

For this project I was responsible for the following features: 

- Making a system to spawn the semi random dungeon generator
- Making a system where enemies should spawn in the room
- Making the dialog system for interaction with the non playable characters
- Implementing the 4 puzzles to solve in the game 
- Implementing traps
- UI programming 

## The semi random dungeon generator 


The dungeon in Curse of the Pyramid was designed to be semi-random. It followed a predefined structure where key rooms—such as the start room, puzzle rooms, and the path leading to the central chamber—were always present to ensure gameplay consistency. The rest of the dungeon was populated with randomized rooms, allowing each playthrough to feel unique while still supporting a controlled gameplay flow.


### Layout Tool (Level Builder)

To support the **semi-random dungeon system** in Curse of the Pyramid, I developed a custom layout tool also known as the Level Builder. This tool was responsible for defining how dungeon rooms were placed and connected within the game world.

#### Core Structure
At the heart of the system is a **Data Asset** (DA_Level) containing:
- Nodes: Represent individual level rooms, each with a unique ID.
- Connections: Define valid directional links between rooms (e.g., a door leading east from one room connects to a west-facing door in another).
- Spawners: Associated with each room to manage difficulty scaling and room population.
![](../assets/img/posts/CurseOfThePyramid/DA_Level.png)

#### Level Builder Actor

The Level Builder is an in-editor actor that visualizes and populates dungeon layouts:

- It uses a configurable grid sized based on the number of nodes and their dimensions.
- By pressing the in-editor button “Make Level”, the actor populates the layout grid using the data in DA_Level, assigning room types and establishing valid connections.
![](../assets/img/posts/CurseOfThePyramid/LayoutTool.png)

#### Visual Wrappers
Two key blueprint classes enhance the layout tool's interactivity:

- Node Wrapper: Represents individual room nodes. Each node uses an enum (RoomType) to define whether it’s a fixed, random, or special room. The node changes color in-editor based on its type, and if a room is fixed, the level asset name can be manually specified.

- Connection Wrapper: Visualizes directional connections between nodes using arrows. It automatically rotates based on the Orientation enum (e.g., N, NE, E, etc.) to match room connectivity.

![](../assets/img/posts/CurseOfThePyramid/NodeWrapper.png)

This tool allowed our designers to efficiently build controlled, replayable dungeon layouts while still supporting randomized elements and gameplay variety.



## The puzzles 

I was responsible for implementing the in-game puzzles designed by [Steven](https://www.linkedin.com/in/stevenserramock/){:target="_blank"}. These puzzles were key gameplay elements in Curse of the Pyramid, contributing to both the challenge and the narrative pacing of the game.

Each puzzle was designed with a unique mechanic and theme, and I worked closely with the design team to translate these concepts into fully functional and bug-free gameplay systems.

Below are video walkthroughs demonstrating how to complete the puzzles:

<p float="left">
<iframe src="https://www.youtube.com/embed/sUfFDAuab_E" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<iframe src="https://www.youtube.com/embed/L6yrXnhD5nA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<iframe src="https://www.youtube.com/embed/tl2wj8h22HQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<iframe src="https://www.youtube.com/embed/rOCN6sIt-J4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>