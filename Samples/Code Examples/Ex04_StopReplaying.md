
This quick example shows how to stop a running replay using the ReplayManager API
Note: By default the replay system will automatically stop the replay once it reaches the end. This behaviour can be changed in the replay settings `Tools -> Ultimate Replay 3.0 -> Settings`

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // Our running playback operation created in the previous example
    // We assume this is assigned for this sample
	private ReplayPlaybackOperation playbackOp = null;

    void StopReplay()
    {
        playbackOp.StopPlayback();
    }
}
```