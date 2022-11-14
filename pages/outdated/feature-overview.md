Feature Overview {#outdated-feature-overview}
========================================

* Multiple rendering API support
  * Direct3D11
  * OpenGL 2.0 or 3.2
  * OpenGL ES 2.0 or 3.0
  * WebGL
* HLSL or GLSL shaders + caching of HLSL bytecode
* Configurable rendering pipeline with default implementations of
  * Forward
  * Light pre-pass
  * Deferred
* Component based scene model
* Skeletal (with hardware skinning), vertex morph and node animation
* Automatic instancing on SM3 capable hardware
* Point, spot and directional lights
* Shadow mapping for all light types
  * Cascaded shadow maps for directional lights
  * Normal offset adjustment in addition to depth bias
* Particle rendering
* Geomipmapped terrain
* Static and skinned decals
* Auxiliary view rendering (reflections etc.)
* Geometry, material & animation LOD
* Software rasterized occlusion culling
* Post-processing
* HDR rendering and PBR rendering
* 2D sprites and particles that integrate into the 3D scene
* Task-based multithreading
* Hierarchical performance profiler
* Scene and object load/save in binary, XML and JSON formats
* Keyframe animation of object attributes
* Background loading of resources
* Keyboard, mouse, joystick and touch input (if available)
* Cross-platform support using SDL 2.0
* Physics using Bullet
* 2D physics using Box2D
* Scripting using C#
* Networking using SLikeNet + possibility to make HTTP requests
* Pathfinding and crowd simulation using Recast/Detour
* Inverse kinematics
* Image loading using stb_image + DDS / KTX / PVR compressed texture support + WEBP image format support
* 2D and “3D” audio playback, Ogg Vorbis support using stb_vorbis + WAV format support
* TrueType font rendering using FreeType
* Unicode string support
* Inbuilt UI based on subset of html and css, localization
* WYSIWYG scene editor editor with undo & redo capabilities and hot code reload
* Model/scene/animation/material import from formats supported by Open Asset Import Library
* Alternative model/animation import from OGRE mesh.xml and skeleton.xml files
* Supported IDEs: Visual Studio, Xcode, Eclipse, CodeBlocks, CodeLite, QtCreator, CLion
* Supported compiler toolchains: MSVC, GCC, Clang, MinGW, and their cross-compiling derivatives
* Supports both 32-bit and 64-bit build
* Build as single external library (can be linked against statically or dynamically)
* Dear ImGui integration used in tools
