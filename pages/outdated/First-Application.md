First Application {#outdated-first-application}
=================

This is a gentle introduction into writing your first application using the engine.

1. Create a directory for your first project.
2. Clone repository into that directory: `git clone https://github.com/rbfx/rbfx.git`
3. Create `CMakeLists.txt` file. This is your application project file.
```CMake
cmake_minimum_required (VERSION 3.14)

# Name your project. Can be anything.
project (MyAwesomeGame)

# Minimal supported C++ version.
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set binary output directories. Changing these paths will require you adjusting your resource path configuration!
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set (CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# Include cloned engine directory in a build.
add_subdirectory(rbfx)

# Define executable name.
add_executable(AwesomeGame WIN32 main.cpp)

# Link to game engine library.
target_link_libraries(AwesomeGame Urho3D)
```
4. Create `main.cpp`.
```cpp
#include <Urho3D/Engine/Application.h>
#include <Urho3D/Engine/EngineDefs.h>
#include <Urho3D/Input/Input.h>

// This is probably always OK.
using namespace Urho3D;


class AwesomeGameApplication : public Application
{
    // This macro defines some methods that every `Urho3D::Object` descendant should have.
    URHO3D_OBJECT(AwesomeGameApplication, Application);
public:
    // Likewise every `Urho3D::Object` descendant should implement constructor with single `Context*` parameter.
    AwesomeGameApplication(Context* context)
        : Application(context)
    {
    }

    void Setup() override
    {
        // Engine is not initialized yet. Set up all the parameters now.
        engineParameters_[EP_FULL_SCREEN] = false;
        engineParameters_[EP_WINDOW_HEIGHT] = 600;
        engineParameters_[EP_WINDOW_WIDTH] = 800;
        // Resource prefix path is a list of semicolon-separated paths which will be checked for containing resource directories. They are relative to application executable file.
        engineParameters_[EP_RESOURCE_PREFIX_PATHS] = ".;..";
    }

    void Start() override
    {
        // At this point engine is initialized, but first frame was not rendered yet. Further setup should be done here. To make sample a little bit user friendly show mouse cursor here.
        GetSubsystem<Input>()->SetMouseVisible(true);
    }

    void Stop() override
    {
        // This step is executed when application is closing. No more frames will be rendered after this method is invoked.
    }
};

// A helper macro which defines main function. Forgetting it will result in linker errors complaining about missing `_main` or `_WinMain@16`.
URHO3D_DEFINE_APPLICATION_MAIN(AwesomeGameApplication);
```
5. Generate build tree with CMake.
6. Build.

Congratulations! You just created your first Urho3D application which does nothing but shows a black window. Good job!