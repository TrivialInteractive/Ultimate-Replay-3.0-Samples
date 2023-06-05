
This quick example shows how to read only the custom metadata from an existing replay file or stream without loading any additional replay associated infromation which may take longer to complete.
Use this approach to quickly access metadata about a replay file or replay stream in order to display a UI replay browser or similar.

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
    // The stream where the replay data will be stored
	// Stream must be writable and support seeking, position, and length
    private Stream replayStream = new MemoryStream();

    void Replay()
    {
        // Read the metadata from a given file
        CustomMetadata meta = ReplayFile.ReadMetadataOnly("Replays/MyReplay.replay");

        Debug.Log("myCustomString = " + meta.myCustomString);
        Debug.Log("myCustomNumber = " + meta.myCustomNumber);


        // Read the metadata from a given stream
        meta = ReplayStream.ReadMetadataOnly(replayStream);

        Debug.Log("myCustomString = " + meta.myCustomString);
        Debug.Log("myCustomNumber = " + meta.myCustomNumber);
    }
}
```