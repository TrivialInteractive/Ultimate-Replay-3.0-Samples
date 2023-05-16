
This quick example shows how to begin a new replay as shown previoulsy, but using a stream as the target storage location.

```cs
using System.IO;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// Store the playback operation for later use
	private ReplayPlaybackOperation playbackOp = null;

	// The stream where the replay data will be stored
	// Stream must be readable and support seeking, position, and length
	// We assume that this stream contains valid replay data for this sample
	private Stream recordStream = new MemoryStream();

	void Replay()
	{
		// Load the storage
		ReplayStorage storage = ReplayStreamStorage.FromStream(recordStream);

		// Start a new replay as soon as the `Replay` method is called
		// Note: We can reuse existing storage targets
		playbackOp = ReplayManager.BeginPlayback(storage);
	}
}
```