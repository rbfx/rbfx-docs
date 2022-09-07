Folder Structure {#folder-structure}
========================================

* `Source/Editor/`: Editor;
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
