
This quick example shows how you can copy an in-memory replay to a replay file so that the data can persist between sessions.
This can be useful if you want to record gameplay continuoulsy and only save certain highlights to file such as a killcam or action replay.

```cs
using System.IO;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // The name of the file that the replay will be saved to
	public string replayFile = "MyReplay.replay";

    void Start()
    {
        // The memory storage where the replay data is stored.
        // We assume that the storage contans a valid replay for this example
        ReplayMemoryStorage storage = ...

        // Create a file to save the replay to
        ReplayFileStorage fileStorage = ReplayFileStorage.FromFile(replayFile);

        // Copy the replay data to file
        // Note that this will block the main thread until complete. 
        // The amount of time taken will depend on the duration and complexity (Number of recorded objects) of the recording.
        storage.CopyTo(fileStorage);
    }
}