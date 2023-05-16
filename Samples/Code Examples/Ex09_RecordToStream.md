
This quick example shows how to begin a new recording as shown previoulsy, but using an IO stream as the target storage location.

```cs
using System.IO;
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

	// The stream where the replay data will be stored
	// Stream must be writable and support seeking, position, and length
	private Stream recordStream = new MemoryStream();

	void Start()
	{
		// Create new stream storage
		storage = ReplayStreamStorage.FromStream(recordStream);

		// Start a new recording as soon as the game loads
		recordOp = ReplayManager.BeginRecording(storage);
	}
}
```