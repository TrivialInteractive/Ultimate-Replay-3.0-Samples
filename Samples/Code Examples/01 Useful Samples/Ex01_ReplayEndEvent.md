
This quick example shows how to call a method once the replay has reached the end

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // Store the replay operation for later use
    private ReplayPlaybackOperation playbackOp = null;

    // The storage created in the previous examples. 
    // We will assume this contains a valid replay for this example.
    private ReplayMemoryStorage storage = null;

    void Start()
    {
        // Start a new replay as soon as start is called
        playbackOp = ReplayManager.BeginPlayback(storage);

        // Add an event listener
        playbackOp.OnPlaybackEnd.AddListener(OnReplayFinished);
    }

    // This method will be called once the replay has completed
    void OnReplayFinished()
    {
        Debug.Log("Replay Finished!");
    }
}
```