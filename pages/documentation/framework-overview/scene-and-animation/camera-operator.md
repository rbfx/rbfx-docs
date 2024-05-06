Camera Operator {#camera-operator}
========================================

The **CameraOperator** component serves as a helper that manages camera-related functionality within a game engine.
Its primary purpose is to track points in world space and update the camera’s position accordingly.
By doing so, it ensures that the camera focuses on relevant areas or objects during gameplay.

## How to Use

When integrating the CameraOperator component into your game, consider the following aspects:

- **Tracking Nodes (TrackNode, RemoveTrackedNode, RemoveAllTrackedNodes)**:
The component allows you to track multiple scene nodes (such as objects or characters) in the game world.
Use **TrackNode** to add a specific node to the list of tracked objects.
If a node becomes irrelevant (e.g., an object moves out of view), remove it using **RemoveTrackedNode**.
To clear all tracked nodes, call **RemoveAllTrackedNodes**.

- **Bounding Box Tracking (IsBoundingBoxTrackingEnabled, SetBoundingBoxTrackingEnabled, GetBoundingBox)**:
The boundingBoxEnabled_ flag determines whether bounding box tracking is active.
By default, it’s disabled.
If enabled, the component uses the specified bounding box (via SetBoundingBox) to determine the camera’s focus area.
Adjust the bounding box to encapsulate the relevant scene region you want the camera to cover.

- **Padding (SetPadding, SetUniformPadding, GetPadding)**:
Padding ensures that the camera doesn’t focus too closely on the tracked objects.
Use SetPadding to define padding in world space units.
Alternatively, set uniform padding in all directions using **SetUniformPadding**.

- **Camera Movement (MoveCamera)**:
The MoveCamera method updates the camera’s position based on the tracked nodes and bounding box.
It calculates the optimal camera position to include all relevant objects while considering padding.
The camera position will be updated automatically every frame if the component is enabled but you can also disable component and call MoveCamera when you need to move the camera node and adjust camera parameters.

Remember that the CameraOperator collaborates with other components (e.g., the actual camera component) to achieve the desired camera behavior.

In summary, the CameraOperator component streamlines camera management by tracking nodes, adjusting camera position, and ensuring proper framing based on the specified bounding box and padding. It’s a valuable tool for enhancing gameplay visuals.