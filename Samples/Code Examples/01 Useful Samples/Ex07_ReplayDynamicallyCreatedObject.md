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
                // Create or instantiate some new object
                GameObject newObject = GameObject.CreatePrimitive(PrimitiveType.Cube);

                // Use the helper API to add and setup the replay components - we can add as many as we like
                // Note that we are using the `Prefab` method here instead which performs extra setup such as registering with the replay manager
                // This should only be called once
                ReplayObject replayObject = ReplayObject.MakeReplayPrefab(newObject, typeof(ReplayTransform), typeof(ReplayAnimator));

                // NewObject must be treated as a prefab in this case since it was registered as a prefab with the replay manager
                // It just means that we cannot record it directly and instead should create a clone instance
                // If multiple instances need to be created then you should store `newObject` like a prefab so that it can be cloned for each new object you need to add.
                GameObject newObjectClone = Instantiate(newObject);

                // Finally since a record operation is already underway, we will need to manually inform the replay system that we want to add a new object to be recorded.
                // This can be done via a `ReplayScene` reference, but there are also a number of helper methods that can be used to make this easy
                // This can be called any number of times 
                ReplayManager.AddReplayObjectToRecordScenes(newObjectClone);
        }
}
```

After that the new object clone will now be recorded and replayed as a prefab. That means during payback the object will be destroyed until it reacehes the time stamp where it was created,
and at that point the replay system will instantiate a new clone of the object to perform playback.
