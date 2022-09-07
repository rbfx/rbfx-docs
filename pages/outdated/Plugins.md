Plugins {#outdated-plugins}
===============================================

Editor workflow is centered around plugins and scenes. User is expected to implement a custom logic in a plugin and use it in a scene. For example plugin may provide a component that implements movement of node it is attached to.

## Dynamic Plugins

If engine is built with `-DBUILD_SHARED_LIBS=ON` - all plugins will be dynamic libraries loaded at runtime. You may customize which plugins are loaded in editor only (for editor utilities) and which plugins are loaded in the editor and in the game (for custom logic). Player application is in charge of loading game plugins. Editor may load/unload/reload dynamic plugins.
![](https://user-images.githubusercontent.com/19151258/63638170-59c0b280-c68d-11e9-8525-9f3b2923969b.png)

## Static Plugins

If engine is built with `-DBUILD_SHARED_LIBS=OFF` - all plugins will be static libraries. In this case user is in charge of loading and these plugins. This is typically done in a method that overrides `Application::Start()`. This is achieved by user building custom executables for editor and player. Use `-DURHO3D_NO_EDITOR_PLAYER_EXE=ON` build parameter to disable creation of default player and editor executables. In such builds editor and player executables are replaced by static `libPlayer` and `libEditor` libraries that implement all required logic. Investigate [PlayerHost.cpp](https://github.com/rokups/rbfx/blob/master/Source/Player/PlayerHost.cpp) and [EditorHost.cpp](https://github.com/rokups/rbfx/blob/master/Source/Tools/Editor/EditorHost.cpp) for example how to initialize plugins in static builds. Static plugins are never unloaded, `PluginApplication::Load()` is executed once on application startup.