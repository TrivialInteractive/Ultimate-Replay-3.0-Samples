
This quick example shows how to include some custom metadata when starting a record operation

```cs
using System;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

// Create a type deriving from ReplayMetadata where we can include some extra data fields.
// Serialization rules are almost identical to Unity JsonUtility serialization.
[Serializable]
public class CustomMetadata : ReplayMetadata
{
	public string myCustomString = "Hello";
	public int myCustomNumber = 123;
}

public class Example : MonoBehaviour
{
	// Store the record operation for later use
	private ReplayRecordOperation recordOp = null;

	// Store the storage target for later use
	private ReplayMemoryStorage storage = null;

	void Start()
	{
		// Create a storage target where the replay data will be saved
		storage = new ReplayMemoryStorage("MyReplay");

		// Create the metadata
		CustomMetadata meta = new CustomMetadata();
		meta.myCustomString = "World";
		meta.myCustomNumber = 456;

		// Assign the metadata to the storage target
		storage.Metadata = meta;

		// Start a new recording as soon as the game loads
		recordOp = ReplayManager.BeginRecording(storage);
	}
}
```
