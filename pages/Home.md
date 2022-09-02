Documentation of Urho3D Rebel Fork {#mainpage}
==================================

**Note**: Legacy and sometimes outdated documentation inherited from original Urho3D project is available \ref LegacyDocumentation "here"

**Disclaimer**: this wiki doesn't attempt to describe every single feature, class or function in the framework.
It merely offers high-level overview of subsystems and features.
It would be too expensive to maintain otherwise.
Use this wiki together with reading framework headers in `Source/Urho3D/*` and relevant comments.

### Tutorials

* [First Application](First-Application.md)
* [Using SDK](Using-SDK.md)

### Miscellaneous

* [Profiling](Profiling.md)
* [Editor Overview](Editor.md)
    * [Plugins](Plugins.md)
* [C# Support](C#-support.md)
* [External subsystems and samples](External-subsystems-and-samples.md)
* [rbfx and Urho3D differences](rbfx-and-Urho3D-differences.md)
* [Migration from vanilla Urho3D](Migration-from-vanilla-Urho3D.md)

### Project Structure

* `Source/Tools/Editor/`: WIP Editor, somewhat useful;
* `Source/Samples/`: Code-friendly samples showing how to use different features;
* `Source/Tests/`: Unit tests checked automatically by CI;
* `Source/Urho3D/`: Main code of the framework:
    * `Audio`: Audio playback utilities and scene components;
    * `Container`: Generic containers and container-related code;
    * `Core`: Generic utilities used by all other modules;
    * `CSharp`: C# bindings;
    * `Engine`: Connects all modules together and manages the framework as a whole;
    * `Glow`: Lightmap and light probe baking tool;
    * `Graphics`: Contains both low-level GAPI interop layer and high-level rendering components;
    * `IK`: Inverse kinematics components;
    * `Input`: User input handling (keyboard, mouse, touch, joystick);
    * `IO`: Low-level and high-level serialization and file system management utilities (and logger);
    * `Math`: Mathematical abstractions and geometry objects;
    * `Navigation`: Navigation mesh generation and pathfinding;
    * `Network`: Low-level networking utilities;
    * `Physics`: Physics simulation and related scene components;
    * `RenderPipeline`: Transforms resources and scene components to draw commands;
    * `Replica`: Utilities for Scene replication and synchronization between server and clients;
    * `Resource`: Resource cache and generic resource types;
    * `RmlUI`: [RmlUI](https://github.com/mikke89/RmlUi) integration;
    * `Scene`: Scene graph and some generic scene components;
    * `Script`: Script management;
    * `SystemUI`: [Dear ImGUI](https://github.com/ocornut/imgui) integration;
    * `UI`: Legacy Urho UI. **Not maintained.**
    * `Urho2D`: 2D rendering and physics. **Not maintained.**

### Asset Workflow

TODO: Write me

### Scene and Animation

* Scene Overview
* Nodes and Components
* [Scene Animation](Scene-Animation.md)

### Graphics and Rendering

* [Low-level rendering API](Low-level-Rendering-API.md)
* [Scene rendering pipeline](Scene-Rendering-Pipeline.md)
* Scene Components:
    * [Subsystems](Scene-Rendering-Subsystems.md)
    * [Zones](Rendering-Zones.md)
    * [Lights](Rendering-Lights.md)
    * [Geometries](Rendering-Geometries.md)
    * [Light Probes](Rendering-Light-Probes.md)
    * [Reflection Probes](Rendering-Reflection-Probes.md)
* Resources:
    * [Models](Model-Resources.md)
    * [Materials](Material-Resources.md)
    * [Textures](Texture-Resources.md)
    * [Techniques](Rendering-Techniques.md)
    * [Shaders](Rendering-Shaders.md)

### Multiplayer and Networking

* [Low-level transport](Low-level-Transport.md)
* High-level Replication:
    * [Replication Manager](Replication-Manager.md)
    * [Network Objects](Network-Objects.md)
        * [Standard Network Objects](Standard-Network-Objects.md)
        * [Standard Network Behaviors](Standard-Network-Behaviors.md)

### UI Overview

* `Urho3D/UI/` is legacy built-in Urho3D UI system. It is the most flexible UI solution, but it requires the biggest amount of hand-holding as well. You can do everything, but you will have to write a lot of code to make it work smoothly. Not actively maintained, bug fixes and minor tweaks only.
* `Urho3D/RmlUI/` is the integration of RmlUI library. Work in progress, but it is already useful. HTML/CSS like UI which requires certain knowledge to setup, but it supports data bindings and UI generation natively. DPI-aware. [Check out RmlUI documentation](https://mikke89.github.io/RmlUiDoc/).
* `Urho3D/SystemUI/` is the integration of Dear ImGUI library. Unlike other two UIs, it supports multiple native windows. It's not recommended to use it for actual game. Use this UI for Editor add-ons or other tools. [Check out Dear ImGUI documentation](https://github.com/ocornut/imgui/wiki).
