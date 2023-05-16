
This quick example shows how you can pause and resume an already running replay

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // Our running playback operation created in the previous example
    // We assume this is assigned for this sample
    private ReplayPlaybackOperation playbackOp = null;

    void PauseReplay()
    {
        // Pause the replay if it is running.
        // Note: This method will do nothing if the replay is already paused.
        playbackOp.PausePlayback();
    }

    void ResumeReplay()
    {
        // Resume the replay if it is running
        // Note: This method will do nothing if the replay is already paused.
        playbackOp.ResumePlayback();
    }
}
```
