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
ValidateName(ea::string_view name): Validates an element or block name.

## Block Types:
The class provides methods to open different types of blocks:
- **OpenSequentialBlock(const char\* name)**: Opens a sequential block (automatically closed when the returned object is destroyed).
- **OpenUnorderedBlock(const char\* name)**: Opens an unordered block (automatically closed when the returned object is destroyed).
- **OpenArrayBlock(const char\* name, unsigned sizeHint = 0)**: Opens an array block (with an optional size hint).
- **OpenSafeSequentialBlock(const char* name)**: Opens a safe sequential block (automatically closed when the returned object is destroyed).
- **OpenSafeUnorderedBlock(const char\* name)**: Opens safe Unordered block. Will be automatically closed when returned object is destroyed.

## Sample code:

Let's take a look at ShaderBytecode::SerializeInBlock method. It takes an Archive object as an argument, which allows it to serialize data. The purpose of this method is to serialize various properties of shader bytecode into the given archive.

```c++
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
