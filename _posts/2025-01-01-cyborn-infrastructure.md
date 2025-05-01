---
layout: post
title: "Cyborn Infrastructure"
featured-img: Cyborn_Infrastructure 
categories: [work, Cyborn, jenkins]
published: true
summary: ""
---

# Infrastructure

During my time at Cyborn I have also worked on some parts of our infrastructure. This was done under supervision of [Wouter Beert](https://www.linkedin.com/in/wouter-beert/){:target="_blank"}
- Setup Test server for Jenkins so we could safely change pipeline code without breaking production 
- Maintaining and updating existing Jenkins pipelines
- Created a Jenkins load balancer plugin to work with Unreal Engine their swarm coordinator so we could reserve nodes for building, testing and making light builds.
- Created Continues Integration workflow for game projects
- Created Continues Integration workflow for internal tools
- Continues Delivery for the meta quest store via Jenkins
- Replaced Incredibuild with [Unreal Engine Build accelerator](https://dev.epicgames.com/documentation/en-us/unreal-engine/horde-unreal-build-accelerator-and-remote-compilation-tutorial-for-unreal-engine). 
- Increased the Jenkins nodes from a single (built-in) node to 9 nodes (including the build-in node)
- Deployed a private GitLab server that runs on our Linux Machine 

## Custom load balancer for Jenkins

To better manage our automated build pipeline for the game projects, I developed a custom load balancer tailored for Unreal Engine workflows. The default Jenkins load distribution system wasn’t sufficient for our needs, especially when dealing with engine-specific builds and light baking constraints.

### Why We Built It
The default Jenkins behavior led to several issues:
- **Incompatible node usage**: Builds would sometimes run on nodes without the correct Unreal Engine version installed.
- **Resource conflicts**: Lighting builds would overload machines already compiling game builds and vise versa. 
- **Concurrency problems**: Multiple Unreal Engine projects compiling on the same node would fail due to UnrealBuildTool's lack of support for concurrent execution.

### How It Works
To solve this, I built a custom system with the following components:
- **Unreal Swarm Coordinator Extension**
    I modified the [Unreal Engine Swarm Coordinator](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-swarm-in-unreal-engine) to support locking and unlocking nodes remotely. This ensured that once the node is locked, that that node would be unavailable for light bakes or other Jenkins jobs.
- **C# CLI Locking Tool**
    I wrote a command-line tool in C# to interface with the modified Swarm Coordinator. This tool was used by the custom coordinator to lock or unlock nodes before Jenkins jobs starter or after they were finished, keeping scheduling conflicts to a minimum.
- **Engine Version Detection**
    [Wouter](https://www.linkedin.com/in/wouter-beert/){:target="_blank"} added metadata to each .uproject file, specifying which Unreal Engine versions were required for different platforms. This allowed build scripts to dynamically detect compatibility instead of hardcoding assumptions.
- **Jenkins Load Balancer Plugin (Java)**
    Finally, I created a Jenkins plugin in Java that served as a smart load balancer. It pulled the .uproject file from source control, parsed the required engine versions, and only assigned builds to nodes with matching engine installs. It also ensured that nodes weren’t already engaged in incompatible tasks like lighting or another game build.

## Continues Integration for game projects

After wrapping up Hubris, we focused on building a more robust and developer-friendly pipeline for future game projects. The goal was to improve build stability, enforce code and asset quality, and tightly integrate with our issue tracking system Jira.

### Source Control & Tooling Foundation
We used [Plastic SCM (Unity DevOps)](https://docs.unity.com/ugs/en-us/manual/devops/manual/unity-version-control) as our version control system and developed several custom tools to enhance the workflow:

- [**Custom Jira Issue Tracker**](https://docs.unity.com/ugs/en-us/manual/devops/manual/unity-version-control)
    We created a fully custom Jira issue tracker plugin for Plastic SCM, giving us granular control over issue linking, validation, and automation triggers tied to commits.
- [**Plastic External Tools**](https://docs.unity.com/ugs/en-us/manual/devops/manual/config-files/externaltools-conf)
    We built a cross-platform desktop tool using [Avalonia (a WPF replacement)](https://avaloniaui.net/). The tool dynamically adjusted its GUI based on the command-line parameters and allowed developers to:
    - Trigger game builds via the Jenkins API.
    ![](../assets/img/posts/Cyborn%20Infrastructure/Create%20Build.png)
    - Trigger automated test runs via the Jenkins API.
    ![](../assets/img/posts/Cyborn%20Infrastructure/Run%20Automated%20Tests.png)
    - Initiate the automated review process via the Jenkins API.
    ![](../assets/img/posts/Cyborn%20Infrastructure/Ready%20For%20Review.png)

### Enforced Quality Controls
We used [client-side triggers](https://docs.unity.com/ugs/en-us/manual/devops/manual/triggers/tutorials/create-first-trigger) to enforce project quality standards:
- Prevented check-ins of unapproved assets unless they passed both the C++ Linter and Unreal Engine Blueprint Linter.
- Ensured developers followed correct asset submission procedures before changes landed in key branches.

### Branch-Specific Configuration
All build and test logic was controlled via a single configuration file located at the root of each workspace. This enabled:
- Per-branch customization of pipeline steps.
- Platform-specific build and test control.
- Fine-grained automated test selection (e.g., only VR tests for certain branches).
This approach allowed teams to safely experiment or scale testing independently, without disrupting the mainline build process.

### CI Workflow: From Branch to Merge
This structured development and verification pipeline was designed by [Wouter Beert](https://www.linkedin.com/in/wouter-beert/) and implemented by me. It streamlined our process from branch creation to verified merges, tightly integrated with Jira, Jenkins, and our custom tools.

**Step-by-Step Workflow**
1. **Branch Creation via Custom Jira Tracker**
    Developers created feature branches using our custom Jira issue tracker integration. Each branch followed a consistent naming convention:
    ```{JiraID}_IssueTitle```
2. **Development & Build Execution**
    Developers implemented the ticket’s requirements and used the external Avalonia tool to:
    - Trigger builds across target platforms via the Jenkins API.
    - Run automated tests based on branch-specific configurations.
3. **Automated Verification Process**
    Once the work was complete, developers initiated a **verification process** through the same external tool.
    - If **any step failed** (e.g., build errors, test failures), the developer would be required to fix and restart the process.
4. **QA Manual Review**
    If verification passed, the Jira ticket was automatically moved to a "Ready for QA Review" state.
    - If approved, Jira automation transitioned the issue to "Ready for Merge".
    - If rejected, the ticket moved back to the "In Development" state for revisions.
5. **Automated Merging**
    Once marked "Ready for Merge," our Jenkins-based pulling and merging pipeline would:
    - Re-verify the branch in a separate environment.
    - Automatically merge it into the parent branch if all validations succeeded

### Automated Verification Process
Our verification pipelines ensured that only stable, tested, and reviewed branches were merged back into parent branches. This process was fully integrated with Jira, Slack, and our NAS-based build storage.
**Verification Steps**
1. **Merge Parent Into Feature Branch**
    Before any validation, we automatically merged the latest version of the parent branch into the feature branch. This ensured compatibility and helped surface any potential merge conflicts early.
2. **Build Creation & Distribution**
    - A full build was created for the appropriate platform(s).
    - The build was uploaded to our **NAS** for access.
    - Notifications were sent to the branch owner via **Slack**, and a build status update was posted to the relevant **Jira issue**.
3. **Automated Testing (if configured)**
    - If automated tests were defined for the branch, they were executed at this stage.
    - Results were sent to both the user and posted to the associated Jira ticket.
4. **Final Merge (for Ready-for-Merge Issues Only)**
    - If the Jira issue was in the "Ready for Merge" state and all previous steps succeeded, the branch was automatically merged into the parent branch.

**Failure Handling**
If **any step** in the verification process failed—whether during merging, building, or testing:
- The Jira issue was automatically transitioned back to "In Progress".
- A Slack notification was sent to the developer with details of the failure.
- The developer could then resolve the issue and reinitiate the verification process.

## Continuous Integration for Internal Software
For internal tooling **where I was the sole developer** the CI process was designed to be lightweight, fast, and automated, while still ensuring stability for office-wide use.

**Workflow Overview**
- **Trigger**:
    Jenkins jobs were automatically triggered on commits to the main branch of any internal software repository.
- **Build & Packaging**:
    The CI system used C#'s publishing tools to compile and package the application for distribution.
- **Deployment**:
    Once built, the application was uploaded to our **Cyborn Package Manager**, a custom internal system for managing software versions and distribution.
- **Availability**:
    After publishing, the updated version was **immediately available** to the rest of the team in the office, streamlining tool updates without requiring manual deployment.

**Why This Worked**
Because I was the sole maintainer of these internal tools, a strict branching or review process wasn’t necessary. Instead, this lean pipeline prioritized speed and convenience while still ensuring every deployed version was cleanly built and versioned.

