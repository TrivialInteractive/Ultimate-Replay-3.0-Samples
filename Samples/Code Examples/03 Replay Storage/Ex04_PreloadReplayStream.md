
This quick example shows how you can fully load a replay stream into memory ahead of time to avoid the default behavour where the replay system will stream in data on demand using a background thread.
This is only recommended for smaller sized replay streams for best performance and memory usage.

```cs
using System.IO;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	void Start()
	{
		// The stream where the replay data is stored.
		// We assume that the stream contains valid replay data for this example.
		Stream stream = ...

		// Load the replay stream as normal
		ReplayStreamStorage storage = ReplayStreamStorage.FromStream(stream);

		// Force the stream to be pre-loaded into memory
		// Note - This will block the main thread until loading is completed which can take some time for larger replay streams.
		storage.LoadFileCompletely();


		// You can also load the replay stream in a single call like so
		storage = ReplayStreamStorage.ReadStreamCompletely(stream);
	}
}
```