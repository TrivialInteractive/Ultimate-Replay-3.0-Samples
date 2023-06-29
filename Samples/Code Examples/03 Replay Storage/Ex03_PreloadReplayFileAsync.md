
This quick example shows how you can fully load a replay file into memory ahead of time asynchronously to avoid the default behavour where the replay system will stream in data on demand using a background thread.
This is only recommended for smaller sized replay files for best performance and memory usage.

```cs
using System.Collections;
using System.IO;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// The name of the file that the replay will be loaded from
	public string replayFile = "MyReplay.replay";

	IEnumerator Start()
	{
		// Load the replay file as normal
		ReplayFileStorage storage = ReplayFileStorage.FromFile(replayFile);

		// Force the file to be pre-loaded into memory
		// Note - The loading will run in the background and we can yield on the async operation to wait for completion
		ReplayAsyncOperation loadOp = storage.LoadFileCompletelyAsync();

		// Wait for the loading to complete
		// After this point we can safely assume that the storage has been fully loaded unless there was an error
		yield return loadOp;
		



		// You can also load the replay file in a single call like so
		// Note that this method must also return the file storage object, so we must use the generic version of ReplayAsyncOperation
		ReplayAsyncOperation<ReplayFileStorage> secondLoadOp = ReplayFileStorage.ReadFileCompletelyAsync(replayFile);

		// Wait for completion like before
		yield return secondLoadOp;

		// Now we can access the storage which was fully loaded
		storage = secondLoadOp.Result;
	}
}
```