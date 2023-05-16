
This quick example shows how to start a replay that was already recorded previously using the ReplayManager API

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
    ReplayMemoryStorage storage = null;

    void Replay()
    {
        // Start a new replay as soon as the `Replay` method is called
        playbackOp = ReplayManager.BeginPlayback(storage);
    }
}
```
