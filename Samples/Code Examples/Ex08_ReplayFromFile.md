
This quick example shows how to begin a new replay as shown previoulsy, but using a file as the target storage location.

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// Store the playback operation for later use
	private ReplayPlaybackOperation playbackOp = null;

	// Our file storage created in the previous example
	// We assume this is assigned for this sample with a valid ReplayFileStorage instance
	private ReplayStorage storage = null;

	// The name of the file that the replay will be loaded from
	public string replayFile = "MyReplay.replay";

	void Replay()
	{
		// Start a new replay as soon as the `Replay` method is called
		// Note: We can reuse existing storage targets
		playbackOp = ReplayManager.BeginPlayback(storage);
	}

	void ReplayPath()
	{
		// We can also load the file back from a path if we are resuming from a different session for example
		// Note: The `FromFile` method works for creating new files as well as reading existing files
		storage = ReplayFileStorage.FromFile(replayFile);

		// Start a new replay
		playbackOp = ReplayManager.BeginPlayback(storage);
	}
}
```