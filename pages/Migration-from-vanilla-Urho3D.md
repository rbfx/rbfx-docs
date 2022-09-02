## Build system

**rbfx** build system is incompatible with original **Urho3D** build system.
The preferred way to use **rbfx** is via CMake subdirectories.

* Use `add_subdirectory (${path_to_rbfx})` command instead of `include (UrhoCommon)`.
Note that `add_subdirectory` supports out-of-tree subdirectories, so it is not required to push **rbfx** as submodule of project's repository.

* Use standard CMake commands `add_library` or `add_executable` instead of custom **Urho3D** macros `setup_main_executable` or `setup_library`

* Use `target_link_libraries (${target_name} Urho3D)` to link library or executable wiht **rbfx**

## Containers

**rbfx** uses EASTL containers with custom extensions.
Migration is pretty much straightforward. Almost all `CamelCase` member functions of Urho3D containers have `snake_case` equivalents in **rbfx** EASTL. Both `eastl::` and `ea::` namespace prefixes can be used.

* **Urho3D** containter includes are removed.
* `Urho3D::LinkedList` is replaced with `eastl::intrusive_list`. **List elements should be deleted maunally**.
* `Urho3D::Vector` and `Urho3D::PODVector` are replaced with `eastl::vector`. **Note that index based `Insert` and `Erase` are renamed to `insert_at` and `erase_at`, and `PushBack(const Vector<T>& v)` is renamed to `append(const ea::vector<T>& v)`.**
* `Urho3D::String` is replaced with `eastl::string`. **Use `eastl::to_string` to make string from number.**
* `Urho3D::List` is replaced with `eastl::list`.
* `Urho3D::Pair` is replaced with `eastl::pair`.
* `Urho3D::SharedArrayPtr` is replaced with `eastl::shared_ptr`.
* `Urho3D::UniquePtr` is replaced with `eastl::unique_ptr`.
* `Urho3D::HashSet` is replaced with `eastl::hash_set`.
* `Urho3D::HashMap` is replaced with `eastl::hash_map`.

**Why not vanilla Urho3D containters?**

They are lacking useful features and it would have taken too much resources to maintain and extend our own containter library.

**Why not standard containers of STL?**

Main reasons against STL are worse debug performance and unspecified container size that makes them incompatible with `Urho3D::Variant`. EASTL also has more features than STL and could be extended.

**Why keep SharedPtr and WeakPtr?**

`SharedPtr` and `WeakPtr` have major differences compared to `shared_ptr` and `weak_ptr` so they cannot be replaced without deep code refactoring. E.g. Urho3D smart pointers can be safely used inside object's constructor while standard pointers cannot.

**Why extend EASTL?**

Urho3D containers have some trivial but useful functions that simplify user code. It's worth to have them in EASTL too.