C# Support {#outdated-csharp-support}
==========

⚠️ C# support is experimental ⚠️ It may not work correctly, fail or crash in spectacular ways. Managed APIs will likely change in the future.

## Current C# wrapper features

* Extensive API coverage estimated at 90%+
* Support for inheriting native classes and overriding their virtual methods
* Support for `ref` parameters
* Support for native containers (`ea::map<K, V>`, `ea::unordered_map<K, V>`, `ea::vector<T>`)
* Transparently unwrapping `SharedPtr<T>` and `WeakPtr<T>`
* Serialization and deserialization of managed components
* Member variable and method renaming to match C# conventions

## Planned features

* Wrap physics subsystem

## How the code looks

```cs
using System;
using System.Diagnostics;
using System.IO;
using Urho3DNet;
using ImGuiNet;

namespace DemoApplication
{
    [ObjectFactory]
    class RotateObject : LogicComponent
    {
        public RotateObject(Context context) : base(context)
        {
            UpdateEventMask = UpdateEvent.UseUpdate;
        }

        public override void Update(float timeStep)
        {
            var d = new Quaternion(10 * timeStep, 20 * timeStep, 30 * timeStep);
            Node.Rotate(d);
        }
    }

    class DemoApplication : Application
    {
        private Scene _scene;
        private Viewport _viewport;
        private Node _camera;
        private Node _cube;
        private Node _light;

        public DemoApplication(Context context) : base(context)
        {
        }

        protected override void Dispose(bool disposing)
        {
            Renderer.SetViewport(0, null);    // Enable disposal of viewport by making it unreferenced by engine.
            _viewport.Dispose();
            _scene.Dispose();
            _camera.Dispose();
            _cube.Dispose();
            _light.Dispose();
            base.Dispose(disposing);
        }

        public override void Setup()
        {
            var currentDir = Directory.GetCurrentDirectory();
            engineParameters_[Urho3D.EpFullScreen] = false;
            engineParameters_[Urho3D.EpWindowWidth] = 1920;
            engineParameters_[Urho3D.EpWindowHeight] = 1080;
            engineParameters_[Urho3D.EpWindowTitle] = "Hello C#";
            engineParameters_[Urho3D.EpResourcePrefixPaths] = $"{currentDir};{currentDir}/..";
        }

        public override void Start()
        {
            Input.SetMouseVisible(true);

            // Viewport
            _scene = new Scene(Context);
            _scene.CreateComponent<Octree>();

            _camera = _scene.CreateChild("Camera");
            _viewport = new Viewport(Context);
            _viewport.Scene = _scene;
            _viewport.Camera = _camera.CreateComponent<Camera>();
            Renderer.SetViewport(0, _viewport);

            // Background
            Renderer.DefaultZone.FogColor = new Color(0.5f, 0.5f, 0.7f);

            // Scene
            _camera.Position = new Vector3(0, 2, -2);
            _camera.LookAt(Vector3.Zero);

            // Cube
            _cube = _scene.CreateChild("Cube");
            var model = _cube.CreateComponent<StaticModel>();
            model.SetModel(Cache.GetResource<Model>("Models/Box.mdl"));
            model.SetMaterial(0, Cache.GetResource<Material>("Materials/Stone.xml"));
            var rotator = _cube.CreateComponent<RotateObject>();

            // Light
            _light = _scene.CreateChild("Light");
            _light.CreateComponent<Light>();
            _light.Position = new Vector3(0, 2, -1);
            _light.LookAt(Vector3.Zero);

            SubscribeToEvent(E.Update, args =>
            {
                var timestep = args[E.Update.TimeStep].Float;
                Debug.Assert(this != null);

                if (ImGui.Begin("Urho3D.NET"))
                {
                    ImGui.TextColored(Color.Red, $"Hello world from C#.\nFrame time: {timestep}");
                }
                ImGui.End();
            });
        }
    }

    internal class Program
    {
        public static void Main(string[] args)
        {
            using (var context = new Context())
            {
                using (var application = new DemoApplication(context))
                {
                    application.Run();
                }
            }
        }
    }
}
```

## Requirements

* Mono 5.4.0 or later
* .NET framework 4.7 or later

## Build

1. Configure build: `cmake -DBUILD_SHARED_LIBS=ON -DURHO3D_FEATURES=CSHARP;SYSTEMUI /path/to/code`
2. `cmake --build .`
3. Create C# project in IDE of your choice, targeting .NET framework 4.7.1.
4. Copy `bin/CoreData` and `bin/Data` to output directory (where .exe will be created).
5. Add `bin/{Urho3D,ImGui}Net.dll` as references to your project.
  * Linux: Copy `bin/libUrho3D.so` to output directory.
  * Windows: Copy `bin/Urho3D.dll` to output directory.
  * MacOS: Copy `bin/libUrho3D.dylib` to output directory.
6. Build and run. For a quick test you may copy code from `Source/Samples/102_CSharpProject/Project.cs`.

## Other details

You may specify `-DURHO3D_NETFX=<value>` to target specific framework version. `<value>` is one of .NET framework identifiers specified at https://docs.microsoft.com/en-us/dotnet/standard/frameworks. Default value is `net471`.

If you set up a custom `.csproj` project be aware that `PlatformTarget` property must match CPU architecture you are targetting (`x64` for 64bit and `x86` for 32bit). Otherwise you would get `BadImageFormatException` error. `AnyCPU` platform target will not work.

When engine is built with `-DURHO3D_PLUGINS=ON` it will automatically compile `*.cs` scripts under `Scripts/` folder on resource path and make defined components available for use. You can find an example at [Scripts/RotateObject.cs](https://github.com/rokups/rbfx/blob/master/bin/Data/Scripts/RotateObject.cs).