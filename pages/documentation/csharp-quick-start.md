Quick Start for C# developers
============================

## From console

To create a .NET rbfx game you need .NET 7.0 SDK installed on your machine.

Run the following to create a new application:

```console
dotnet new console -n MyGame
```

And you need to add the rbfx.Urho3DNet nuget package with the following command:

```console
cd MyGame
dotnet add package rbfx.Urho3DNet --prerelease
```

## From Visual Studio

To create a .NET Console Application and add a NuGet package to it, you need to follow these steps:

1. Open Visual Studio.
2. Go to File > New > Project.
3. Select "Console App (.NET 7.0)" as the project type.
4. Choose a name for your project and select a location to save it.
5. Click "Create."

Now you need to add rbfx nuget package into the project.

1. To add a NuGet package, right-click on the "Dependencies" folder in the Solution Explorer and select "Manage NuGet Packages."
2. In the NuGet Package Manager, search for the "rbfx.Urho3DNet" package you want to add. The rbfx may not be in release yet so don't forget to check "include prerelease" checkbox.
3. Select the package and click "Install."
4. You will see a preview of the changes that will be made to your project. Review them and click "Accept."

The NuGet package will be added to your project and its dependencies will be installed.

To use the package, add the following line at the top of your code file:

## Application loop

Here is a sample application source code:

```csharp
using Urho3DNet;

namespace SampleProject
{
    static class Program
    {
        public static void Main(string[] args)
        {

            using (var context = new Context())
            {
                using (SharedPtr<SampleApplication> app = new SampleApplication(context))
                {
                    app.Ptr.Run();
                }
            }
        }
    }
}
```

First you need to create context. Then you need create an instance of the application class. You also have to write the application class for your game. Then by calling Run() method you make the application run within the context.

For more sample code look up [sample-project-csharp](https://github.com/rbfx/sample-project-csharp).

**...To be continued...**