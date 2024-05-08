SerializableResource {#serializable-resource}
========================================

The **SerializableResource** serves as a base class for resources that need to be serialized (i.e., converted into a format that can be saved to disk). It is designed to work with **Archive** serialization, which allows objects to be stored and retrieved in a consistent manner.

The class inherits from **SimpleResource**. **SerializeInBlock(Archive& archive)** override is an override of a method from the **SimpleResource** base class. It handles the serialization of the resource data into an archive (e.g., binary, JSON or XML).

- **GetValue() const** returns a pointer to the resource value (a Serializable object).

- **SetValue(Serializable\* serializable)** sets the value of the resource to the provided Serializable object. The SerializableResource will own the value via SharedPtr from this moment.

The file of the **SerializableResource** has default extension **.serializable** and contains the value type name and attributes of the **Serializable** object:

```json
{
	"type": "TypeName",
	"value": {
		"attributes": [
			{
				"name": "AttributeName",
				"type": "Float",
				"value": 1.0
			}
        ]
    }
}
```

Main advantage of the **SerializableResource** over custom resource formats is that **Editor** understands **SerializableResource** structure and level designers can edit values in **Serializable** right in the editor.
