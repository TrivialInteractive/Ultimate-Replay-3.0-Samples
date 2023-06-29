
This quick example shows how you can fully load a replay file into memory ahead of time to avoid the default behavour where the replay system will stream in data on demand using a background thread.
This is only recommended for smaller sized replay files for best performance and memory usage.

```cs
using System.IO;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// The name of the file that the replay will be loaded from
	public string replayFile = "MyReplay.replay";

	void Start()
	{
		// Load the replay file as normal
		ReplayFileStorage storage = ReplayFileStorage.FromFile(replayFile);

		// Force the file to be pre-loaded into memory
		// Note - This file will block the main thread until loading is completed which can take some time for larger replay files.
		storage.LoadFileCompletely();


		// You can also load the replay file in a single call like so
		storage = ReplayFileStorage.ReadFileCompletely(replayFile);
	}
}
```