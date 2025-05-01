---
layout: post
title: "Wall Town Wonders"
featured-img: WallTownWonders 
categories: [work, Unreal Engine, C++, Cyborn]
published: true
summary: ""
---

# Wall Town Wonders 

Wall Town Wonders is a Mixed reality cozy life sim city builder where you become the host of a vibrant community in your living room.

## My Responsibilities 

For Wall Town Wonders, I contributed across several areas of the project, with a strong focus on tooling, platform abstraction, and localization. My key responsibilities included:

- **Localization Pipeline Overhaul**
    I reworked the entire localization workflow to make it accessible to all team members. This involved rebuilding our original C# localization command line interface tool directly within the Unreal Editor, complete with a user-friendly GUI. Later in production, I developed a Python script to automate translation using AI-based services, speeding up the localization process for multiple languages.

- **Platform Abstraction & Refactoring**
    A major part of my role was to refactor the codebase and remove all hard dependencies on Meta (e.g., Quest-specific APIs and assumptions). I introduced a plugin-based interface architecture, allowing the game to be cleanly ported to other Android-based platforms with minimal code duplication or platform-specific conditionals.

- **Tool Development for Modular Block Structure**
    I created an editor tool to support our modular block-building system. This tool automated the generation and updating of Blueprints and Data Assets used throughout the game's construction pipeline, significantly improving iteration speed for designers and artists.


## Localization improvements
During Wall Town Wonders, I led several upgrades to our localization pipeline, with the goal of making it more scalable, user-friendly, and production-ready for multilingual support.

## String Table Integration
The first major change was migrating all FText instances to use [String Tables](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-string-tables-for-text-in-unreal-engine). This allowed for centralized management of localized text and eliminated the need to manually edit FText values inside Blueprints.

To enforce this across the project, I created a custom lint rule for our Unreal Engine Linter, which flagged any Blueprint FText that wasn’t sourced from a String Table. This ensured consistency across the team and minimized potential issues during localization export.

We later added additional rules—such as disallowing standard quotation marks (") in String Tables, since they broke the CSV export format. To solve this, I built an Asset Action Utility that automatically replaced standard quotes (") with safe alternatives (“ ”) that preserved formatting without breaking file integrity.

### In-Editor Localization GUI

The second major improvement was porting and expanding the C# localization tool I had originally built for Hubris into Unreal Engine using Slate. This new GUI made the localization workflow accessible directly within the editor and eliminated the need for external scripts or command-line usage. 
While the design was inspired by the BYG [BYG Localization Plugin](https://unreal-garden.com/tutorials/byg-localization/).

We made several customizations to better fit our needs:

- Added Multi platform support 
- Backing the FText localization as was previously done by Unreal Engine their Localization Dashboard
- Automated the importing and compiling of text  

## Wall Town Wonders Tools

For this part I'm going to explain some terms that we use internally for this project.

- Piece: This is a data object with a few variables: a static mesh asset and 
    - A static mesh 
    - [Custom primitive data](https://dev.epicgames.com/documentation/en-us/unreal-engine/storing-custom-data-in-unreal-engine-materials-per-primitive)
    - A Unique Id that represents the name of the component
- PieceVisualizerComponent: This is a component derived from Static Mesh and holds a Piece Object 
- BlockData: This holds a struct with Pieces in it wrapped in a structure
- Block: This is the actor that will represent a house/building in the game 

![](../assets/img/posts/Wall%20Town%20Wonders/BlockImporter.png)

This tool was an Editor Utility Widget and had the following functionalities:
- Import blocks from blender generated data
- Create Pieces assets from selected Static Mesh Assets
- Create Blueprint Children for all the selected blueprints
- Creating BlockData From Unreal Engine selected actor in a level 
- Updating the BlockData on all the Blocks which will updates existing components and create/delete components in the asset in editor time
