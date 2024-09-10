Math Cheatsheet
==============================

## Angles, radians and degrees

Radians and degrees are two units of measurement for angles. A full circle is 360 degrees, while the same angle in radians is 2π. This means that 1 radian is equal to 180/π degrees, and 1 degree is equal to π/180 radians.

To convert an angle from degrees to radians, you can use the following formula:
```cpp
radians = degrees * M_DEGTORAD;
```

To convert an angle from radians to degrees, you can use the following formula:
```cpp
degrees = radians * M_RADTODEG;
```

The \ref Atan2 function is a two-argument arctangent function that calculates the angle between the positive x-axis and the ray from the origin to the point (x, y) in the Cartesian plane. The result is given in degrees and is confined to the range (-180, 180].

The yaw angle is the angle between the positive x-axis and the projection of the direction vector onto the x-z plane. You can calculate the yaw angle from a direction vector using the \ref Atan2 function as follows:
```cpp
    Quaternion rot(0, 70, 0);
    auto dir = rot * Vector3::FORWARD;
    float angle = Atan2(dir.x_, dir.z_); // 70.0f
```

Some Atan2 result values for reference:
```
Atan2( 0.0f,  0.0f) =>  0.0f
Atan2( 0.0f,  1.0f) =>  0.0f
Atan2( 1.0f,  1.0f) => 45.0f
Atan2( 1.0f,  0.0f) => 90.0f
Atan2( 1.0f, -1.0f) => 135.0f
Atan2(-1.0f, -1.0f) => -135.0f
Atan2(-1.0f,  1.0f) => -45.0f
```

Here is a C++ function that clamps an angle value in degrees between -180 and 180 degrees:

```
float ClampAngle(float angle)
{
    angle = Urho3D::Mod(angle, 360.0f);
    if (angle > 180.0f)
        return angle - 360.0f;
    if (angle < -180.0f)
        return angle + 360.0f;
    return angle;
}
```

This function takes an angle value in degrees as an input and returns the clamped angle value between -180 and 180 degrees. The Urho3D::Mod function is used to calculate the remainder of the division of the angle by 360, which brings the angle value within the range of -360 to 360 degrees. Then, if the angle is greater than 180 degrees, 360 is subtracted from it to bring it within the desired range. Similarly, if the angle is less than -180 degrees, 360 is added to it to bring it within the desired range.

This clamping function is very useful when you add or subtract angles. It is not absolutely necessary but makes this easier to understand and also keeps value close to 0 for better precision.

## Interpolation

Linear Interpolation (Lerp) is a mathematical function that is used to find a point some fraction of the way along a line between two endpoints. In other words, it is used to find a value between two values that are known.

```cpp
    Vector3 vectorA{1, 0, 0};
    Vector3 vectorB{0, 1, 0};
    float t = 0.2f;

    auto res1 = vectorA.Lerp(vectorB, t); // {0.8f, 0.2f, 0.0f}
    auto res2 = Urho3D::Lerp(vectorA, vectorB, t); // {0.8f, 0.2f, 0.0f}
```

An easing function is a mathematical function that modifies the time value of an animation to create a more natural and smooth effect. It is used to change the rate of change of a parameter over time. Easing functions are mostly used in animations to change a component value in a defined period of time. You can move objects, change their colors, scales, rotations and anything you want simply using easing equations.

For example, if you have an animation that moves an object from point A to point B in a straight line, it will look very robotic and unnatural. But if you use an easing function to modify the time value of the animation, you can create a more natural and smooth effect.

There are many different types of easing functions, each with its own unique effect. Some common types include linear easing, quadratic easing, cubic easing, and elastic easing.

Here is an example of interpolation with easing:
```cpp
    auto res1 = vectorA.Lerp(vectorB, Urho3D::BackInOut(t));
```

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

All rotations in the engine performed in a clockwise order. Here is what going to happen with a vector {x,y,z} if rotated around an axis at 90 degrees:

```
Axis X (Vector::RIGHT):   { x, y, z } => { x,-z, y }
Axis Y (Vector::UP):      { x, y, z } => { z, y,-x }
Axis Z (Vector::FORWARD): { x, y, z } => {-y, x, z }
```

## Matrix operations

In the engine, matrices are column-major (like in OpenGL) while in DirectX they are row-major.

### Matrix Multiplication Order

In general, for a column-vector on the right (OpenGL convention), M1 * (M2 * (M3 * v)) = (M1 M2 M3) * v, i.e. the left-most matrix is at the root node while the right-most is at the leaf node.

When you apply multiple transformations (like translation, rotation, and scaling) to an object, the order in which you multiply the matrices matters. The general order is:

- Scale
- Rotate
- Translate

This order ensures that each transformation is applied correctly without unintended distortions. For example, if you scale an object first, then rotate it, the rotation will not affect the scale. Similarly, translating an object after scaling and rotating it ensures that the object moves to the correct position.

### Mental Model for Applying Transformations

To visualize how matrices are applied to object transformations, you can think of it as a series of steps:

- Start with the object at the origin.
- Apply scaling: This changes the size of the object.
- Apply rotation: This rotates the object around the origin.
- Apply translation: This moves the object to its final position.

In the engine, this sequence is represented by multiplying the matrices in the reverse order of how you want the transformations to be applied. For example, if you want to scale, then rotate, and finally translate an object, you would multiply the translation matrix by the rotation matrix, and then by the scaling matrix.

Below is a sample code demonstrating the order of matrix multiplication:

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

Alternatively, if you prefer a visual approach, here are some illustrations depicting the matrix multiplication sequence:

#### translation * rotation:
![translation * rotation](images/documentation/math/rotate-translate.gif)


#### rotation * translation:
![rotation * translation](images/documentation/math/translate-rotate.gif)

### Node transform matrices

To combine world transform matrices to make a matrix that would transform from one game object space into another object space, you can multiply the inverse of the world transform matrix of the second object
by the world transform matrix of the first object. This will give you a matrix that will transform from one object space to another object space.

Here is an example code:
```cpp
    Matrix3x4 transformA = nodeA->GetWorldTransform();
    Matrix3x4 transformB = nodeB->GetWorldTransform();
    Matrix3x4 transformAtoB = transformB.Inverse() * transformA;
    const Vector3 positionInA{1, 2, 3};
    const Vector3 positionInB = transformAtoB * positionInA;
```
The transformAtoB matrix will now transform from the nodeA object’s space to the nodeB object’s space.

### Bone bind pose matrices

The bind pose matrix is the matrix that represents the position and orientation of a joint in its default pose. It is used as a reference for skinning, which is the process of deforming a mesh based on the movement of joints in a skeleton.

When an animation is played, each joint in the skeleton is transformed by its animation matrix. The bind pose matrix is then used to transform the vertices of the mesh into the space of the joint. This is done by multiplying each vertex by the inverse of the bind pose matrix for the joint it is associated with.

```cpp
    const Vector3 bindSpacePosition{1, 2, 3};
    const Matrix3x4 offsetMatrix = animatedModel->GetSkeleton().GetBone(0u)->offsetMatrix_;
    const Vector3 localSpacePosition = offsetMatrix * bindSpacePosition;
```

## Quaternion operations

To build a quaternion that rotates an object by 180 degrees around each axis (X, Y, and Z), you can use the axis-angle representation of quaternions. Here’s how you can do it:

```cpp
    // Quaternion for 180 degrees rotation around X-axis
    Quaternion rotationX{0.0f, 1.0f, 0.0f, 0.0f};
    // Quaternion for 180 degrees rotation around Y-axis
    Quaternion rotationY{0.0f, 0.0f, 1.0f, 0.0f};
    // Quaternion for 180 degrees rotation around Z-axis
    Quaternion rotationZ{0.0f, 0.0f, 0.0f, 1.0f};
```

### Quaternion Multiplication Order

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

To reverse rotation take result of the Inverse() method:
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
