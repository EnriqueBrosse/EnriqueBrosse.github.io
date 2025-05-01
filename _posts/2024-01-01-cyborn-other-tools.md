---
layout: post
title: "Cyborn Other tools"
featured-img: Cyborn_Tools 
categories: [work, Cyborn, Unreal Engine, C++, C#, xaml]
published: true
summary: ""
---

# Additional Tools Developed at Cyborn

During my time at Cyborn, I proactively identified gaps in our development workflows and delivered solutions through custom tooling. These tools ranged from Unreal Engine editor extensions to C#-based pipeline utilities, significantly improving productivity across teams. This was done under supervision of [Wouter Beert](https://www.linkedin.com/in/wouter-beert/){:target="_blank"}

## Unreal Engine Tools
- Expanding the [UE Linter](https://www.fab.com/listings/10850cb2-be1d-43d2-971d-922c73d218f3)
- Creating the Plastic SCM Diffing tool so we can diff assets from different branches 

## C# tools
- Created the our internal Package Manager + Updater
- Created a C++ Linter for Unreal Engine, parsing code and returning linter errors before compiling the project
- Android Toolkit: An android interaction tool that helps developers debug unreal engine
- Maintaining our unreal engine distribution software for deployed engines 
 

## Unreal Engine Linter
Before I joined Cyborn, the [Unreal Engine linter](https://www.fab.com/listings/10850cb2-be1d-43d2-971d-922c73d218f3) had already been customized. My main responsibility was to maintain existing lint rules and expand its capabilities to better enforce project standards and improve error visibility.

**Key Contributions:**
- Created custom lint rules for internal workflows
- **Introduced sub-violations**: Enabled grouping of related issues under parent categories to provide better structure and readability in lint reports.
- **Implemented clickable hyperlinks**: sub-violations now include direct links that:
    - Open the offending asset in the Unreal Editor
    - Highlight the exact property or node where the issue occurs
These enhancements helped artists and designers fix issues more efficiently without relying on programmers or navigating the asset manually.


## Unreal Engine Plastic SCM Differ
To streamline code and asset reviews, I created a Plastic SCM differ tool integrated directly into Unreal Engine. This tool compares the current branch with its **parent branch**, making it easier for developers and designers to see exactly what has changed.
**Key Features**:
- **Branch Comparison**: Diffs the current working branch against its parent, showing all modified assets.
- **Unreal-native Diffing**: Leverages Unreal Engine’s built-in diff tools for Blueprint and asset comparison.
- **Fallback to Plastic SCM Diff Tools**: For text assets it will seamlessly switches to Plastic SCM’s own differ.
- **Built with C++ and Slate**: Fully integrated into the Unreal Editor UI for a native experience.

This tool significantly improved our review workflows and became a core part of our development process—especially when used alongside our custom linting and verification systems

## Unreal Engine C++ linter

To enforce consistent code quality across the team, I developed a custom C++ linter specifically tailored for Unreal Engine projects.

**Key Features:**
- **Regex-based parser**: Parses C++ source files using regular expressions (excluding templated classes/functions). This also includes all the UPROPERTY, UFUNCTION, UCLASS,USTRUCT and UENUM macros. 
- **Integrated with UnrealBuildTool**: The linter runs automatically during project builds, acting as a pre-compilation validation step.
- **Editor feedback**: Errors and warnings are surfaced directly in Visual Studio, ensuring immediate developer feedback.
- **Fully unit tested**: The linter includes a comprehensive test suite to guarantee stability and validate all rule sets.

This tool enforces our internal code conventions from naming standards to formatting and structural patterns and helps reduce bugs by catching mistakes early.

## Android Toolkit

The Android Toolkit is a developer-focused debugging and automation utility built around ADB (Android Debug Bridge), designed specifically to enhance Unreal Engine development for platforms like the Meta Quest 3.

**Features:**
- **Package Management**: Lists all installed packages on a connected Android device. (The selected package will be the package where we'll be sending commands and command line arguments to)
- **PSO Cache Merging**: Automates the combination of Pipeline State Object (PSO) caches via Unreal Engine.
- **In-Game Command Execution**: Sends live console commands (e.g., stat GPU, stat FPS) to running Unreal Engine games for real-time diagnostics (with autofill for commands).
- **Pre-Launch Argument Injection**: Allows developers to configure and send command line arguments to the game before startup for debugging or feature toggling.

**Technical Details:**
- Developed in C# using Avalonia, a modern cross-platform alternative to WPF.
- Built for internal use to streamline debugging, profiling, and performance tuning on VR hardware.

This tool eliminated many manual steps in the testing and optimization loop and gave developers greater control over runtime behavior during development.

### Cyborn Package Manager

Originally developed by [An Phu Dupont](https://www.linkedin.com/in/an-phu-dupont-807763b2/), I took over the Cyborn Package Manager as the primary maintainer and contributed several key improvements that helped scale it for broader use across our development and build infrastructure.
**Key Contributions:**
- **Dependency Management:** Introduced a system to define and resolve inter-package dependencies, ensuring reliable version control and compatibility during deployment.
- **Self-Updating System Tray Utility:** 
    - Built a lightweight tray application that checks for updates on a timed interval.
    - When updates are available, it uses the CLI interface of the package manager to download and install the latest version automatically.
- **Jenkins Integration:** Making sure that Jenkins nodes would properly restart when packages were updated. This was done by sending API requests to Jenkins to turn the node temporally offline and then restarting the SSH-Agent.

These upgrades turned the Cyborn Package Manager into a more robust and automation-friendly tool, essential for maintaining consistency across developer machines and CI/CD pipelines.