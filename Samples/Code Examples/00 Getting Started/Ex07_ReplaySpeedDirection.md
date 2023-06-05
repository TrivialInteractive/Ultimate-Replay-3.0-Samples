
This quick example shows how you can change the direction and speed of a running replay

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // Our running playback operation created in the previous example
    // We assume this is assigned for this sample
    private ReplayPlaybackOperation playbackOp = null;

    void ChangeReplayDirection(PlaybackDirection direction)
    {
        // Change playback direction
        playbackOp.PlaybackDirection = direction;

        // Example of changing to reverse direction
        playbackOp.PlaybackDirection = PlaybackDirection.Backward;
    }

    void ChangeReplaySpeed(float timeScale)
    {
        // Change playback time scale
        // Note: timeScale is a scalar value that determines how quickly the replay is udpated
        playbackOp.PlaybackTimeScale = timeScale;

        // Change to half speed to give a slow motion effect
        playbackOp.PlaybackTimeScale = 0.5f;

        // Change to double speed to give a fast forward effect
        playbackOp.PlaybackTimeScale = 2.0f;

        // Change back to regular speed
        playbackOp.PlaybackTimeScale = 1.0f;
    }
}
```
