This quick example shows how you can continue gameplay frm the point where the replay finishes, with all recorded objects maintaining their last replayed values. This can be very useful if you intend to create a time rewind feature or similar.

You can inform the replay system that you wish to keep the last state of the replay when exiting play mode when starting a replay:
```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    void Start()
    {
        // Load a given replay
        ReplayStorage storage = ReplayFileStorage.FromFile("MyReplay.replay");
        
        // Start playback and be sure to keep the replay state when finished
        ReplayManager.BeginPlayback(storage, null, null, RestoreSceneMode.KeepState);
    }
}
```
Additionally, you can change the restre scene mode at any time when the replay is running:
```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    void Start()
    {
        // This example assumes that we have already started a playback peration
        ReplayPlaybackOperation playback = ...

        // Change the restore scene mode as required while the replay is running
        playback.RestoreSceneMode = RestoreSceneMode.KeepState;
        playback.RestoreSceneMode = RestoreSceneMode.RestoreState;
    }
}
```

It is also possible to fetch an arbitrary snapshot from any replay storage and restore one or more replay objects with this information without needing to start a playback operation. In this case you can achieve similar behaviour as a save system, although it is generally not recommended to use Ultimate Rpelay 3.0 for such a purpose due to potential inaccuracies.
```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    void Start()
    {
        // Load a given replay
        ReplayStorage storage = ReplayFileStorage.FromFile("MyReplay.replay");
        
        // Prepare the storage for reading
        storage.Prepare(ReplayStorageAction.Read);
        
        // Fetch a snapshot that we wish to restore - in this case we get the last snapshot
        ReplaySnapshot snapshot = storage.FetchSnapshot(storage.Duration);
        
        // Create a scene containing the objects we wish to affect. 
        // We use the default behaviour here which is to find active objects in the current scene
        ReplayScene updateScene = ReplayScene.FromCurrentScene();
        
        // Apply the snapshot
        updateScene.RestoreSnapshot(snapshot, storage);
        
        // Finally we should release the storage
        storage.Dispose();
    }
}
```
