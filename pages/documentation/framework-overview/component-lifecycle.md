Component Life Cycle {#component-lifecycle}
========================================

## When created from code

When a component is attached to a GameObject, game engine executes a number of event methods in a predetermined order.

```cpp
    auto scene = MakeShared<Scene>(context);
    auto node = scene->CreateChild();
    node->CreateComponent<LifeCycleComponent>();
    scene.Reset();
```

The following is the order of execution for event methods:

- **OnNodeSet(nullptr, node)**: This method is called when the component is attached to a node.
- **OnSceneSet(scene)**: This method is called when the component's node is attached to a scene.
- **OnMarkedDirty(node)**: Node calls the OnMarkedDirty method at AddComponent.
- **OnSceneSet(nullptr)**: This method is called when the component's node is detached from a scene. In this case the scene gets destroyed.
- **OnNodeSet(node, nullptr)**: This method is called when the component is detached from a node.

When component is loaded from a file the following sequence of event methods is executed:

## When loaded from resource

```xml
<scene>
		<node>
            <component type="LifeCycleComponent">
            </component>
		</node>
</scene>
```
- OnNodeSet(nullptr, node): This method is called when the component is attached to a node.
- OnSceneSet(scene): This method is called when the component's node is attached to a scene.
- OnMarkedDirty(node): Node calls the OnMarkedDirty method at AddComponent.
- **ApplyAttributes()**: After whole scene loaded the ApplyAttributes call is executed for all nodes recursively,
- OnSceneSet(nullptr): This method is called when the component's node is detached from a scene. In this case the scene gets destroyed.
- OnNodeSet(node, nullptr): This method is called when the component is detached from a node.

## OnSetEnabled

OnSetEnabled is only executed when the state of the component changes. For example in the following code the OnSetEnabled won't be executed although the component was enabled by default but then, when attached to the node is effectively disabled:

```cpp
    auto scene = MakeShared<Scene>(context);
    auto node = scene->CreateChild();
    node->SetEnabled(false);
    node->CreateComponent<LifeCycleComponent>();
```

But then when node->SetEnabled(true) is executed the component's event method OnSetEnabled will be called.

If you have some complex logic that depends on component been enabled be sure to check state of the component when OnNodeSet is executed:

```cpp
    bool enabled = IsEnabledEffective();
```