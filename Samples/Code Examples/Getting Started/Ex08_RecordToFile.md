
This quick example shows how to begin a new recording as shown previoulsy, but using a file as the target storage location.

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// Store the record operation for later use
	private ReplayRecordOperation recordOp = null;

	// Store the storage target for later use
	// Note that we have now used the base `ReplayStorage` type so that we can specify any type of storage that we like
	private ReplayStorage storage = null;

	// The name of the file that the replay will be saved to
	public string replayFile = "MyReplay.replay";

	void Start()
	{
		// Create new file storage
		storage = ReplayFileStorage.FromFile(replayFile);

		// Start a new recording as soo as the game loads
		recordOp = ReplayManager.BeginRecording(storage);
	}
}
```