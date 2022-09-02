* Latest Urho3D changes are always merged into rbfx.
* Removed AngelScript. This scripting engine is dependent on x86/64 architecture
  * Editor. It was written in AngelScript.
* Removed Lua. It was using tolua++ utility for generating bindings from manually crafted interfaces. Utility is outdated and no longer supported and each change to API had to be reflected in interface files. Support for Lua may be reintroduced in the future if anyone volunteers to implement bindings using swig which is both maintained and can automatically pick up new APIs.
* C# scripting (experimental). Most of engine APIs are accessible from C#. This language can be used same way C++ is used. Relevant features:
  * Support for generating C# wrappers for user code (planned)
  * Editor plugins in C# (working)
* Custom Urho3D containers were removed and replaced with EASTL (except for smart pointers).
* Logging system was rewritten to use [spdlog](https://github.com/gabime/spdlog) as logging backend.
  * `URHO3D_*LOG()` macros can format strings using [fmt](https://github.com/fmtlib/fmt) library. This change us fully backwards-compatible.
* Command line parsing system was rewritten to use [CLI11](https://github.com/CLIUtils/CLI11/). User may inject new command line parameters easily in `Application::Setup()` method.
* ETC2 compressed texture support added (without runtime decompression).
* [crunch](https://github.com/Unity-Technologies/crunch/) texture added, though in the future it may be replaced with [basis](https://github.com/BinomialLLC/basis_universal).
* CMake build system was rewritten to use modern CMake approach. As a result you may import engine to your codebase through `add_subdirectory()` or use engine as SDK through imported targets (little tested).
* Android build system uses standard gradle script instead of experimental kotlin scripts from upstream Urho3D.
* Database subsystem was removed as it is out of the scope of engine. It is trivial to use it as an external dependency if needed.
* Asset viewer utility for quick inspection of assets (.fbx support through AssetImporter).
* Editor application (heavily WIP), not production-ready.
* Player application meant to run an application that was packaged by editor.
* Custom profiler replaced with [tracy](https://bitbucket.org/wolfpld/tracy/) which comes as both standalone application and a tab integrated in editor.
* `SystemUI` subsystem which integrates [Dear ImGui](https://github.com/ocornut/imgui) for creation of tools.
* `Console` and `DebugHud` were rewritten to use `SystemUI`.
* UIElement may have `styleFile` xml attribute which specifies style to be automatically applied to the element.
* Improved 3D UI code (multiple instances of `UI` subsystem may exist and render to different textures).
* `StringHash` is constexpr when building without `URHO3D_HASH_DEBUG`.