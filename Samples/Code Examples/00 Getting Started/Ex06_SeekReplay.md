
This quick example shows how you can seek though the timeline of a replay much like a video player to reach a certain time or offset.  
Seeking can be very useful in order to jump to certain parts of the replay, or to simply support a sliding time bar as you would see in video players so that the user can scroll manually.

There are 2 main ways to seek using the replay API:
1. Absolute seeking where you specify the time stamp in seconds that you would like to jump to
2. Normalized seeking where you specify a value between 0-1 representing the progress of the replay, where 0 = start and 1 = end

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
	// Our running playback operation created in the previous example
	// We assume this is assigned for this sample
	private ReplayPlaybackOperation playbackOp = null;

	void SeekToTime(float time)
	{
		// Seek to the target time in seconds from the start of the replay
		playbackOp.SeekPlayback(time);

		// We can also seek from the current location to move forward 1 second for example
		playbackOp.SeekPlayback(1f, PlaybackOrigin.Current);

		// And we can also seek from the end of the replay to show the last 2 seconds for example
		// In the case of `PlaybackOrigin.End`, the specified time offset represents a negative offset from the end of the replay
		playbackOp.SeekPlayback(2f, PlaybackOrigin.End);
	}

	void SeekNormalzied(float t)
	{
		// t must be a value between 0-1
		t = Mathf.Clamp01(t);

		// Seek to the target value
		// This is very useful when the duration of the replay is not known or useful
		playbackOp.SeekPlaybackNormalized(t);

		// We could seek to the middle of the replay like so regardless of duration
		playbackOp.SeekPlaybackNormalized(0.5f);
	}
}
```
