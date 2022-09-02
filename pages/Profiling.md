Engine integrates [tracy](https://bitbucket.org/wolfpld/tracy/) for CPU profiling. GPU profiling may be supported in the future. In order to profile your application do the following:

1. Build engine with `-DURHO3D_PROFILING=ON`
2. Mark code sections you wish to profile with `URHO3D_PROFILE*` macros.
3. Connect profiler application by pressing `Connect`:

[[images/connect.png]]

[[images/profiler.png]]

# Macros

## URHO3D_PROFILE

Profile a block of code until end of scope. Pass unquoted string as a name of block.

```cpp
void ProfiledFunction()
{
    // Code timing of which is not included in profiled block.
    URHO3D_PROFILE("ProfiledFunctionBlock");
    // Other code here which is included in block timing.
}
```

## URHO3D_PROFILE_C

Profile a block of code until end of scope. Pass unquoted string as a name of block. Displayed block will have a specified rgb color.

```cpp
void ProfiledFunction()
{
    // Code timing of which is not included in profiled block.
    URHO3D_PROFILE_C("ProfiledFunctionBlock", 0xff0000);    // Red block
    // Other code here which is included in block timing.
}
```

## URHO3D_PROFILE_THREAD

If you use worker threads this macro can be used to give meaningful names to the threads. Place this macro at the start of worker function.

```cpp
void WorkerFunction()
{
    URHO3D_PROFILE_THREAD("MyWorker");
    // Other code.
}
```

## URHO3D_PROFILE_VALUE

Keep track of arbitrary numeric values.

```cpp
void Update()
{
    URHO3D_PROFILE_VALUE("FPS", GetFps());
    // Other code.
}
```

## URHO3D_PROFILE_MESSAGE

Save a message to profiler log window.

```cpp
void Update()
{
    URHO3D_PROFILE_MESSAGE("Hello world", 11);
    // Other code.
}
```

## URHO3D_PROFILE_ZONENAME

Set a custom string name to the profiled block.

```cpp
void ProfiledFunction()
{
    // Code timing of which is not included in profiled block.
    URHO3D_PROFILE("ProfiledFunctionBlock");
    URHO3D_PROFILE_ZONENAME("SomethingElse", 13);    // Profiler will display this block with "SomethingElse" name instead.
    // Other code here which is included in block timing.
}
```

## URHO3D_PROFILE_FRAME

Internal engine macro. Do not use.
