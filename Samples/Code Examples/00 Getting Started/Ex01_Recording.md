
This quick example shows how to begin a new recording using the ReplayManager API

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

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

		// Start a new recording as soon as the game loads
		recordOp = ReplayManager.BeginRecording(storage);
	}
}
```