### Version 3.3.0 or newer

This example shows how you can setup a runtime created or instantiated game object to be replayable. 
It is posisble in older versions with much more setup, however version 3.3.0 introduces the new API to make this setup much easier, and will be the focus of this sample. 

```cs
using UltimateReplay;

public class Example : MonoBehaviour
{
        void Start()
        {
                // Create or instantiate some new object
                GameObject newObject = GameObject.CreatePrimitive(PrimitiveType.Cube);

                // Use the helper API to add and setup the replay components - we can add as many as we like
                ReplayObject replayObject = ReplayObject.MakeReplayObject(newObject, typeof(ReplayTransform), typeof(ReplayAnimator));
        }
}
```

The game object is now setup with the necessary replay components and is now read for recording. 
Any record operations started now will also include this new replay object (Assuming that you are not providing a custom ReplayScene).

### Spawning during recording
The above will work fine for record operations that start after the object is setup for replay, but what about if you want to create or spawn an object while the recording is already underway?

In that case you will need to use the replay prefabs feature which requires a little more setup.

```cs
using UltimateReplay;

public class Example : MonoBehaviour
{
        void Start()
        {
                // Created in above example
                ReplayObject replayObject = ...

                // Next we need to register this object as a replay prefab
                // This will allow the replay system to destroy and instatnaite versions of this object on demand as required by the current playback state
                ReplayManager.AddReplayPrefabAssetProvider(replayObject);

                // Finally since a record operation is already underway, we will need to manually inform the replay system that we want to add a new object to be recorded.
                // This can be done via a `ReplayScene` reference, but there are also a number of helper methods that can be used to make this easy
                ReplayManager.AddReplayObjectToRecordScenes(replayObject);
        }
}
```

After that the new object will now be recorded and replayed as a prefab. That means during payback the object will be destroyed until it reacehes the time stamp where it was created,
and at that point the replay system will instantiate a new clone of the object to perform playback.
