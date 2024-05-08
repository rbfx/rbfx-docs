Archive {#archive}
========================================

This class is part of the game engine and provides an interface for reading and writing files in binary, JSON, or XML formats. Here are the key points about the **Archive** class:

## Overview:
The Archive class represents a hierarchical structure of blocks and elements.

An archive must have exactly one root block.

Blocks can contain other blocks or elements of any type.

Each block or element may have a name, following C++ naming conventions. Arbitrary strings are not allowed, and the name “key” is reserved.

Unsafe blocks should not be closed until all items within them are serialized.

## Methods and Properties:
- **GetContext()**: Returns the context associated with the archive.
- **GetName()**: Returns the name of the archive (if applicable).
- **GetChecksum()**: Returns a checksum (if applicable).
- **IsInput()**: Indicates whether the archive is in input mode (i.e., reading).
- **IsHumanReadable()**: Determines if human readability is preferred over performance and output size. 
  - Binary serialization is disfavored. 
  - String hashes are serialized as strings when possible. 
  - Enumerators are serialized as strings when possible. 
  - Simple compound types (e.g., Vector3) are serialized as formatted strings instead of blocks.

- **IsUnorderedAccessSupportedInCurrentBlock()**: Checks if unordered element access is supported in the current open block.
- **HasElementOrBlock(const char* name)**: Determines whether an element or block with the given name is present.
- **IsEOF()**: Indicates whether the archive can no longer be serialized.
- **GetCurrentBlockPath()**: Returns the current string stack (path within the archive).
- **BeginBlock(const char\* name, unsigned& sizeHint, bool safe, ArchiveBlockType type)**: Starts a new archive block.
sizeHint is required for array blocks.
Errors during serialization of a safe block do not affect data outside of the block.

- **EndBlock()**: Ends the current archive block (may postpone exceptions).
- **Flush()**: Flushes all pending events (call before destructor).
- **ValidateName(ea::string_view name)**: Validates an element or block name.
  - Empty names are not allowed
  - Name must start with letter or underscore.
  - Name must contain only letters, digits, underscores, dots or colons.

## Block Types:
The class provides methods to open different types of blocks:
- **OpenSequentialBlock(const char\* name)**: Opens a sequential block (automatically closed when the returned object is destroyed).
- **OpenUnorderedBlock(const char\* name)**: Opens an unordered block (automatically closed when the returned object is destroyed).
- **OpenArrayBlock(const char\* name, unsigned sizeHint = 0)**: Opens an array block (with an optional size hint).
- **OpenSafeSequentialBlock(const char* name)**: Opens a safe sequential block (automatically closed when the returned object is destroyed).
- **OpenSafeUnorderedBlock(const char\* name)**: Opens safe Unordered block. Will be automatically closed when returned object is destroyed.

## Sample code:

Let's take a look at ShaderBytecode::SerializeInBlock method. It takes an Archive object as an argument, which allows it to serialize data. The purpose of this method is to serialize various properties of shader bytecode into the given archive.

```cpp
void ShaderBytecode::SerializeInBlock(Archive& archive)
{
    const unsigned version = archive.SerializeVersion(Version);
    if (version != Version)
        throw ArchiveException("Compiled shader version is outdated, cached shader is ignored");

    SerializeValue(archive, "type", type_);
    SerializeValue(archive, "mime", mime_);
    SerializeVectorAsBytes(archive, "bytecode", bytecode_);

    SerializeVectorAsObjects(archive, "vertexAttributes", vertexAttributes_, "vertexAttribute",
        [&](Archive& archive, const char* name, VertexShaderAttribute& value)
    {
        auto block = archive.OpenUnorderedBlock(name);
        SerializeValue(archive, "semantic", value.semantic_);
        SerializeValue(archive, "semanticIndex", value.semanticIndex_);
        SerializeValue(archive, "inputIndex", value.inputIndex_);
    });
}
```

- Version Check:
The first step is to check the version of the serialized data.
It retrieves the version from the archive using archive.SerializeVersion(Version) or writes the version into the file.
If the retrieved version does not match the expected Version, it throws an ArchiveException indicating that the compiled shader version is outdated, and the cached shader should be ignored. The written version will always match to Version so we don't have to do any additional checks. This makes the code cleaner and easy to read.

- Serialization of Properties:
The following properties are serialized using various helper functions:
  - type_: Serialized using SerializeValue(archive, "type", type_).
  - mime_: Serialized using SerializeValue(archive, "mime", mime_).
  - bytecode_: Serialized as a vector of bytes using SerializeVectorAsBytes(archive, "bytecode", bytecode_).

- Serialization of Vertex Attributes:
The vertexAttributes_ vector is serialized as array named “vertexAttributes”. Each element of the array is serialized within an unordered block named “vertexAttribute”.
For each VertexShaderAttribute in the vector, the following steps occur:
  - An unordered block is opened with the attribute’s name.
  - The semantic, semantic index, and input index of the attribute are serialized using SerializeValue.
  - Destructor of the block variable automatically closes the block.

## Helper functions:

### SerializeValue:

```cpp
void SerializeValue(Archive& archive, const char* name, T& value);
```

**SerializeValue** function used as a default serializer option. If you define new primitive type that can be serialized with Archive it is recommended to define SerializeValue function for your time.

Here is an example of SerializeValue defined for a SphericalHarmonicsDot9 type:
```cpp
void SerializeValue(Archive& archive, const char* name, SphericalHarmonicsDot9& value)
{
    ArchiveBlock block = archive.OpenUnorderedBlock(name);
    SerializeValue(archive, "Ar", value.Ar_);
    SerializeValue(archive, "Ag", value.Ag_);
    SerializeValue(archive, "Ab", value.Ab_);
    SerializeValue(archive, "Br", value.Br_);
    SerializeValue(archive, "Bg", value.Bg_);
    SerializeValue(archive, "Bb", value.Bb_);
    SerializeValue(archive, "C", value.C_);
}
```

### SerializeOptionalValue:

```cpp
/// Serialize element or block that's optional if archive type supports it.
/// There's no overhead on optionality if Archive doesn't support optional blocks.
template <class T, class U = EmptyObject, class TSerializer = Detail::DefaultSerializer>
void SerializeOptionalValue(Archive& archive, const char* name, T& value, const U& defaultValue = U{},
    const TSerializer& serializeValue = TSerializer{})
```

### SerializeVectorAsObjects:

```cpp
/// Serialize vector with standard interface. Content is serialized as separate objects.
template <class T, class TSerializer = Detail::DefaultSerializer>
void SerializeVectorAsObjects(Archive& archive, const char* name, T& vector, const char* element = "element", const TSerializer& serializeValue = TSerializer{})
```
The **SerializeVectorAsObjects** function is a templated utility for serializing a vector of objects (elements) using a standard interface. The function allows you to serialize each element of the vector separately.

Parameters:
- **archive**: An Archive object representing the serialization target.
- **name**: A string specifying the name of the serialized vector (used as a block name in the archive).
- **vector**: A reference to the vector of elements to be serialized.
- **element** (optional): A string specifying the name of each individual element (used within the block).
- **serializeValue** (optional): A serializer function (defaulting to Detail::DefaultSerializer) responsible for serializing an individual element.

Steps in Serialization:

- Determine the number of elements in the vector (numElements).
- Open an array block within the archive with the specified name and the determined size.
- If the archive is in input mode (i.e., deserialization), adjust the vector size based on the size hint provided by the block.
- Iterate over each element in the vector:
  - Call the serializeValue function to serialize the element using the specified element name.

### SerializeMap:

```cpp
/// Serialize map or hash map with with standard interface.
template <class T, class TSerializer = Detail::DefaultSerializer>
void SerializeMap(Archive& archive, const char* name, T& map, const char* element = "element",
    const TSerializer& serializeValue = TSerializer{}, bool clear = true)
```

The purpose of this function is to serialize the key-value pairs stored in a map (or hash map) into an Archive. It abstracts away the serialization process, allowing you to work with different types of maps consistently.

Parameters:

- **archive**: An Archive object representing the serialization target.
- **name**: A string specifying the name of the serialized map (used as a block name in the archive).
- **map**: A reference to the map (or hash map) to be serialized.
- **element** (optional): A string specifying the name of each individual element (used within the block).
- **serializeValue** (optional): A serializer function (defaulting to Detail::DefaultSerializer) responsible for serializing individual key-value pairs.
- **clear** (optional): A boolean flag indicating whether to clear the map before deserialization (input mode).

Type Traits and Conditional Compilation:

The function uses type traits and conditional compilation (if constexpr) to handle different scenarios. If the value type of the map is Variant and the serializer type matches the default (Detail::DefaultSerializer), it follows one path. Otherwise, it takes a different path.

Serialization Process:
- The function opens an array block within the archive with the specified name and the size equal to the map’s size.
- If the archive is in input mode (i.e., deserialization):
  - If clear is true, it clears the map.
  - It iterates over the expected number of elements (based on the block’s size hint):
    - Opens an unordered block for each element.
    - Deserializes the key using serializeValue.
    - Deserializes the value (either directly or via SerializeVariantInBlock if the value type is Variant).
- If the archive is in output mode (i.e., serialization):
  - It iterates over each key-value pair in the map:
    - Opens an unordered block for each element.
    - Serializes the key and value using serializeValue.