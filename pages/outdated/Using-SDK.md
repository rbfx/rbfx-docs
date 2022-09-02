Using framework as SDK {#outdated-using-as-sdk}
======================

If you wish to maintain engine code outside of your project you may build engine SDK. SDK is created by building install target:

1. `cmake /path/to/code/repository -DCMAKE_INSTALL_PREFIX=/path/to/SDK [optional build options]`
2. `cmake --build . --target install`

Now `/path/to/SDK` contains a newly built engine SDK. In order to use SDK in your project import engine target in your CMake project:

```cmake
include(/path/to/SDK/share/CMake/Urho3D.cmake)
add_executable(YourApplication main.cpp)
target_link_libraries(YourApplication Urho3D)
```

This will import all required include paths, compile definitions and compiler options.

### Extensive example of using WEB SDK

```cmake
cmake_minimum_required (VERSION 3.4.3)

# Name your project. Can be anything.
project (WebSdkSampleProject)

# Set binary output directories. Changing these paths will require you adjusting your resource path configuration!
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set (CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set (CMAKE_POSITION_INDEPENDENT_CODE ON)

# rbfx requires C++17 or later.
set (CMAKE_CXX_STANDARD 17)
set (CMAKE_CXX_STANDARD_REQUIRED ON)

# Make sure URHO3D_SDK variable is set!
# Include various build helpers.
include (${URHO3D_SDK}/share/cmake/Modules/UrhoCommon.cmake)
# Include SDK targets.
include (${URHO3D_SDK}/share/cmake/Urho3D.cmake)

# Pack core engine data and application data.
create_pak("${CMAKE_CURRENT_SOURCE_DIR}/Data" "${CMAKE_BINARY_DIR}/bin/Data.pak")
create_pak("${URHO3D_SDK}/bin/CoreData"       "${CMAKE_BINARY_DIR}/bin/CoreData.pak")

# Create a sample application.
add_executable(Sample WIN32 main.cpp)
target_link_libraries(Sample PUBLIC Urho3D)

# Set up web application.
if (WEB)
    # Set up sample application as web executable.
    web_executable(Sample)
    # Package .pak files into Resources.js
    package_resources_web(
            FILES        "${CMAKE_BINARY_DIR}/bin/CoreData.pak"
                         "${CMAKE_BINARY_DIR}/bin/Data.pak"
            RELATIVE_DIR "${CMAKE_BINARY_DIR}/bin"
            OUTPUT       "Resources.js"
    )
    # Let sample application know to use Resources.js
    web_link_resources(Sample Resources.js)
    # Provide a shell html file.
    target_link_libraries(Sample PRIVATE "--shell-file ${URHO3D_SDK}/bin/shell.html")
endif ()
```