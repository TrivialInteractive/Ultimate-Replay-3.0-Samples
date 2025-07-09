Ultimate Replay uses a ReplayScene object to store a collection of replay objects for a particular replay operation. 
Replay scenes are passed in optionally when you start a record or playback operation, however in most cases a null scene is passed which is the default.
When a null ReplayScene is provided, then the replay system will simply create a new ReplayScene object for that operation by finding all active replay objects in the current active Unity scene.

In some cases you may like to have more control over which objects are recorded, useful if you are working with additive scenes, multiple simultaneous replay operations, and a few other reasons. 
With that in mind you can create and manage a ReplayScene object manually for more control.

### Create Replay Scene
Firstly a new replay scene can be creating just using the constructor:
```cs
ReplayScene scene = new ReplayScene();
```
Alternatively you can use one of the static helper methods if you want to build a replay scene from a Unity scene.
```cs
// Get active Unity scene
Scene unityScene = SceneManager.GetActiveScene();

// Replicates the default behaviour when a null replay scene is provided
ReplayScene scene = ReplayScene.FromScene(unityScene);
```

### Modify Replay Scene
Once you have created a ReplayScene object, you can easily add and remove replay objects as needed using the following methods:
```cs
// This can be a scene object or prefab instance that has a `ReplayObject` attached
GameObject objectToReplay = ...

// Create the replay scene
ReplayScene scene = new ReplayScene();

// Add the object to the scene
scene.AddReplayObject(objectToReplay);
```
A similar method exists for removing objects
```cs
scene.RemoveReplayObject(objectToReplay);
```

### Use Replay Scene
Once you have manully created a replay scene, next you will want to use it as part of a record or playback operation. 
Both `BeginRecording` and `BeginPlayback` methods accept an optional `ReplayScene` objecy as the second parameter, which defaults to null:
```cs
// Created in previous steps
ReplayScene scene = ...

// Create some storage
ReplayStorage storage = new ReplaymemoryStorage();

// Start recording
ReplayRecordOperation recordOp = ReplayManager.BeingRecording(storage, scene);
```
The usage is very similar for playback also:
```cs
// Created in previous steps
ReplayScene scene = ...

// Create some storage
ReplayStorage storage = new ReplaymemoryStorage();

// Start recording
ReplayRecordOperation recordOp = ReplayManager.BeginPlayback(storage, scene);
```

The result of this is that the created record or playback operation will only affect replay objects that have been added to the replay scene provided, and any active replay objects that would be picked up normally will simply be ignored by these operations.
It can be very useful if you want separate record operations for player characters and scene objects as an example, or for any other reason where recording or replaying objects in the same Unity scene should be split up. 
