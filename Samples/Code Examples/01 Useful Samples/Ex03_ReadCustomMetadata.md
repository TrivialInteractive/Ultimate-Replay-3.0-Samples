
This quick example shows how to read some custom metadata after starting a replay operation

```cs
using System;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

// Create a type deriving from ReplayMetadata where we can include some extra data fields.
// Serialization rules are almost identical to Unity JsonUtility serialization.
[Serializable]
public class CustomMetadata : ReplayMetadata
{
	public string myCustomString = "Hello";
	public int myCustomNumber = 123;
}

public class Example : MonoBehaviour
{
    // Store the replay operation for later use
    private ReplayPlaybackOperation playbackOp = null;

    // The storage created in the previous examples. 
    // We will assume this contains a valid replay for this example.
    private ReplayMemoryStorage storage = null;

    void Replay()
    {
        // Start a new replay as soon as the `Replay` method is called
        playbackOp = ReplayManager.BeginPlayback(storage);

        // Read the metadata
        CustomMetadata meta = (CustomMetadata)storage.Metadata;

        Debug.Log("myCustomString = " + meta.myCustomString);
        Debug.Log("myCustomNumber = " + meta.myCustomNumber);
    }
}
```