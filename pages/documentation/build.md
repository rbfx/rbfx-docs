Building the Project  {#build}
==============================

This document provides instructions on how to build the project on various platforms using CMake.

## Common CMake Build Parameters

The following table lists the common CMake build parameters that are defined in the current open file:

| Parameter | Description |
| --- | --- |
| URHO3D_ENABLE_ALL | Enable all features by default |
| URHO3D_GLOW | Lightmapping subsystem enabled |
| URHO3D_IK | Inverse kinematics subsystem enabled |
| URHO3D_LOGGING | Enable logging subsystem |
| URHO3D_NAVIGATION | Navigation subsystem enabled |
| URHO3D_NETWORK | Networking subsystem enabled |
| URHO3D_PHYSICS | Physics subsystem enabled |
| URHO3D_PROFILING | Profiler support enabled |
| URHO3D_PROFILING_FALLBACK | Profiler uses low-precision timer |
| URHO3D_PROFILING_SYSTRACE | Profiler systrace support enabled |
| URHO3D_SYSTEMUI | Build SystemUI subsystem |
| URHO3D_URHO2D | 2D subsystem enabled |
| URHO3D_PHYSICS2D | 2D physics subsystem enabled |
| URHO3D_RMLUI | HTML subset UIs via RmlUI middleware |
| URHO3D_PARTICLE_GRAPH | Particle Graph Effects |
| URHO3D_COMPUTE | Enable Compute shaders |
| URHO3D_ACTIONS | Tweening actions |
| URHO3D_SHADER_TRANSLATOR | Enable shader translation from universal GLSL shaders to other GAPI via glslang and SPIRV-Cross |
| URHO3D_SHADER_OPTIMIZER | Enable shader optimization via SPIRV-Tools |
| URHO3D_CSHARP | Enable C# support |
| URHO3D_NETFX | TargetFramework value for .NET libraries |
| URHO3D_NETFX_RUNTIME_VERSION | Version of runtime to use. |
| URHO3D_DEBUG_ASSERT | Enable Urho3D assert macros |
| URHO3D_FILEWATCHER | Watch filesystem for resource changes |
| URHO3D_SPHERICAL_HARMONICS | Use spherical harmonics for ambient lighting |
| URHO3D_HASH_DEBUG | Enable StringHash name debugging |
| URHO3D_MONOLITHIC_HEADER | Create Urho3DAll.h which includes all engine headers. |
| URHO3D_MINIDUMPS | Enable writing minidumps on crash |
| URHO3D_PLUGINS | Enable plugins |
| URHO3D_THREADING | Enable multithreading |
| URHO3D_WEBP | WebP support enabled |
| URHO3D_TESTING | Enable unit tests |
| URHO3D_PACKAGING | Enable *.pak file creation |
| EMSCRIPTEN_WASM | Use wasm instead of asm.js |
| EMSCRIPTEN_TOTAL_MEMORY | Memory limit in megabytes. Set to 0 for dynamic growth. Must be multiple of 64KB. |
| URHO3D_PLUGIN_LIST | List of plugins to be statically linked with Editor and Player executables |
| URHO3D_PARALLEL_BUILD | MSVC-only: enable parallel builds. A bool or a number of processors to use. |
| URHO3D_PLAYER | Build player application |
| URHO3D_EDITOR | Build editor application |
| URHO3D_EXTRAS | Build extra tools |
| URHO3D_TOOLS | Tools enabled. Bool or a list of tool target names. |
| URHO3D_SAMPLES | Build samples |
| URHO3D_MERGE_STATIC_LIBS | Merge third party dependency libs to Urho3D.a |
| URHO3D_NO_EDITOR_PLAYER_EXE | Do not build editor or player executables. |
| URHO3D_SSL | Enable OpenSSL support |
| URHO3D_SDK | Path to a SDK or a build containing tools compiled for current host architecture |

Nearly all features are disabled by default, therefore please explicitly enable features you are going to use, or use `-DURHO3D_ENABLE_ALL=ON` to enable everything (which is a lot, and will build much slower).

## Building on Windows (Win32)

To build the project on Windows, follow these steps:

1. Open the command prompt and navigate to the project directory.
2. Run the following command to configure the build:

   ```
   cmake -G "Visual Studio 17 2022" -A x64   \
         -S. -Bcmake-build-win32             \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for Visual Studio 2022 and generates a 64-bit build.

3. Run the following command to build the project:

   ```
   cmake --build cmake-build-win32 --config Release
   ```

   This command builds the project in Release mode.

## Building on Windows (UWP)

To build the project on Windows, targetting UWP platform, follow these steps:

1. Open the command prompt and navigate to the project directory.
2. Run the following command to configure the build:

   ```
   cmake -G "Visual Studio 17 2022" -A x64   \
         -S. -Bcmake-build-uwp               \
         -DCMAKE_SYSTEM_NAME=WindowsStore    \
         "-DCMAKE_SYSTEM_VERSION=10.0"       \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for Visual Studio 2022 and generates a 64-bit build.

3. Run the following command to build the project:

   ```
   cmake --build cmake-build-uwp --config Release
   ```

   This command builds the project in Release mode.

## Building on Linux

To build the project on Linux, follow these steps:

1. Open the terminal and navigate to the project directory.
2. Run the following command to configure the build:

   ```
   cmake -S. -Bcmake-build-linux    \
         -DCMAKE_BUILD_TYPE=Release \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for Release mode.

3. Run the following command to build the project:

   ```
   cmake --build cmake-build-linux
   ```

   This command builds the project.

## Building on MacOS

To build the project on MacOS, follow these steps:

1. Open the terminal and navigate to the project directory.
2. Run the following command to configure the build:

   ```
   cmake -G Xcode -T buildsystem=1  \
         -S. -Bcmake-build-macos    \
         -DCMAKE_BUILD_TYPE=Release \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for Release mode.

3. Run the following command to build the project:

   ```
   cmake --build cmake-build-macos
   ```

   This command builds the project.

   Alternatively, you may open the Xcode project a generated  in `cmake-build`` directory.

## Building on Android

To build the project on Android, follow these steps:

1. Open Android Studio.
2. Open Android project which is in `android` subdirectory inside the project folder.
3. Press the `Make Project` button (green hammer in the toolbar) or `CTRL+F9` to build the project.

## Building on iOS

To build the project on iOS, follow these steps:

1. Open the terminal and navigate to the project directory.
2. Run the following command to configure the build:

   ```
   cmake -G Xcode -T buildsystem=1  \
          -S. -Bcmake-build-ios     \
         -DCMAKE_TOOLCHAIN_FILE=CMake/Toolchains/IOS.cmake \
         -DPLATFORM=SIMULATOR64     \
         -DDEPLOYMENT_TARGET=11     \
         -DCMAKE_BUILD_TYPE=Release \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for iOS simulator. Build parameters may need to be changed in order to target real hardware or a different OS version.

3. Run the following command to build the project:

   ```
   cmake --build cmake-build-ios
   ```

   This command builds the project.

   Alternatively, you may open the Xcode project a generated  in `cmake-build`` directory.

## Building for Web (Emscripten)

To build the project for Web using Emscripten, follow these steps:

1. Install Emscripten SDK.
   ```
   git clone https://github.com/emscripten-core/emsdk.git
   cd emsdk
   ./emsdk install latest
   ./emsdk activate latest
   ```

   You may need to adapt these commands, depending on OS you are on and shell you are using:
   - CMD: Use `./emsdk.bat`
   - Bash: Use `./emsdk`.
   - PowerShell: Use `./emsdk.ps1`.

2. Open the terminal and navigate to the project directory.
3. Run the following command to configure the build, replacing `$EMSDK` with the path to cloned Emscripten SDK directory:

   ```
   cmake -S. -Bcmake-build-web      \
         "-DCMAKE_TOOLCHAIN_FILE=$EMSDK/upstream/emscripten/cmake/Modules/Platform/Emscripten.cmake" \
         "-DEMSCRIPTEN_ROOT_PATH=$EMSDK/upstream/emscripten/" \
         -DCMAKE_BUILD_TYPE=Release \
         -DURHO3D_SAMPLES=ON
   ```

   This command configures the build for Release mode and Emscripten.

4. Run the following command to build the project:

   ```
   cmake --build cmake-build-web
   ```

   This command builds the project.

## Use of native tools during crosscompiling

In some cases build process needs to use tools build for the host system. Such cases are:
- When using `-DURHO3D_CSHARP=ON`, `swig` executable is required to generate C# bindings.
- When using `-DURHO3D_PACKAGING=ON`, `PackageTool` is required to generate `.pak` files.

While desktop builds can build and use these tools on their own, mobile/web/uwp builds can not. Native tools must be provided in that case.

### Use local native build

1. Build project as described above, with a parameter `-DURHO3D_TOOLS=PackageTool;swig`.
2. Generate mobile/web/uwp project with a parameter `-DURHO3D_SDK=path/to/cmake-build-win32`.
3. Build mobile/web/uwp as described above.

### Use SDK installation

1. Build project as described above, with parameters `-DURHO3D_TOOLS=PackageTool;swig` and `-DURHO3D_PREFIX_PATH=path/to/SDK`.
2. Install SDK.

   ```
   cmake --install cmake-build-win32
   ```

3. Generate mobile/web/uwp project with a parameter `-DURHO3D_SDK=path/to/SDK`.
4. Build mobile/web/uwp as described above.
