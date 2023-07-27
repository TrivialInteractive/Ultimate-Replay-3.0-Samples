
This quick example shows how you can copy an in-memory replay to a replay file so that the data can persist between sessions.
For this example the copy operation will run on the background thread allowing other gameplay or loading UI to run while the copy occurs.
This can be useful if you want to record gameplay continuously and only save certain highlights to file such as a killcam or action replay.

```cs
using System.IO;
using System.Collections;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // The name of the file that the replay will be saved to
	public string replayFile = "MyReplay.replay";

    IEnumerator Start()
    {
        // The memory storage where the replay data is stored.
        // We assume that the storage contans a valid replay for this example
        ReplayMemoryStorage storage = ...

        // Create a file to save the replay to
        ReplayFileStorage fileStorage = ReplayFileStorage.FromFile(replayFile);

        // Copy the replay data to file
        // Note that this will NOT block the main thread and will instead run on a background thread. 
        // The amount of time taken will depend on the duration and complexity (Number of recorded objects) of the recording.
        ReplayAsyncOperation copyOp = storage.CopyToAsync(fileStorage);

        // Wait for copy to complete
        yield return copyOp;



        // Alternativley we can wait for the operation to complete while getting progress updates
        // Loop until completed
        while(copyOp.IsDone == false)
        {
            // Log the progress
            Debug.LogFormat("Copy Progress: {0}%", copyOp.Progress * 100);

            // Important that we yield to not block the main thread
            yield return null;
        }
    }
}