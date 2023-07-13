Math Cheatsheet
==============================

## Vector operations

To convert one vector to another use the .To\*Vector\* operators. Here are few examples:

```cpp
    // Make IntVector2
    const IntVector2 value{1, 2};
    // Convert it to Vector2
    const Vector2 vec2 = value.ToVector2();
    // Convert it to Vector3, set Z component to 1
    const Vector3 vec3 = value.ToVector3(1.0f);
```

To rotate vector by quaternion use the following:

```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make Quaternion
    const Quaternion quaternion(90, Vector3::UP);
    // Rotate vector by quaternion
    Vector3 rotated = quaternion * vec3; // The result is {3, 2, -1}
```

To transform vector with matrix use multiplication:

```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make Matrix
    const Matrix3 mat(90, Vector3::UP);
    // Rotate vector by quaternion
    Vector3 rotated = mat * vec3; // The result is {3, 2, -1}
```

If you use Matrix3x4 or Matrix4 but you only need to apply rotation and scale you can add a zero as W component and use multiplication:

```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make Matrix
    Matrix3x4 mat{Matrix3{90.0f, Vector3::UP}};
    mat.SetTranslation(Vector3{10, 20, 30});
    // Rotate and translate vector by matrix
    Vector3 translated = mat * vec3;  // The result is {13, 22, 29}
    // Rotate vector by matrix
    Vector3 rotated = mat * vec3.ToVector4(0.0f); // The result is {3, 2, -1}
```

## Matrix operations

In the engine, matrices are column-major (like in OpenGL) while in DirectX they are row-major.

In general, for a column-vector on the right (OpenGL convention), M1 * (M2 * (M3 * v)) = (M1 M2 M3) * v, i.e. the left-most matrix is at the root node while the right-most is at the leaf node.

```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make rotation matrix
    Matrix3x4 rotation{Matrix3{90.0f, Vector3::UP}};
    // Make translation matrix
    Matrix3x4 translation{Vector3{10,20,30}, Quaternion::IDENTITY, Vector3::ONE};
    // Transform vector by matrix
    Vector3 rt = (rotation * translation) * vec3; //33, 22, -11
    Vector3 expl = rotation * (translation * vec3); //33, 22, -11
    Vector3 tr = (translation * rotation) * vec3; //13, 22, 29
```

## Quaternion operations

Quaternion multiplication order is the same as with matrices. Here is an example to illustrate it:

```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make quaternions
    Quaternion rotationY{90.0f, Vector3::UP};
    Quaternion rotationX{90.0f, Vector3::RIGHT};
    // Transform vector by quaternions
    Vector3 expl = rotationY * (rotationX * vec3); //2,-3,-1
    Vector3 yx = (rotationY * rotationX) * vec3; //2,-3,-1
    Vector3 xy = (rotationX * rotationY) * vec3; //3,1,2
```

To revert rotation take result of the Inverse() method:
```cpp
    // Make Vector3
    const Vector3 vec3{1, 2, 3};
    // Make quaternions
    Quaternion rotationY{90.0f, Vector3::UP};
    Quaternion rotationNegY = rotationY.Inverse();
    // Transform vector by quaternions
    Vector3 rot = rotationY * vec3; //3,2,-1
    Vector3 invRot = rotationNegY * vec3; //-3,2,1
```
