
This example shows how you can use the replay storage API to read recorded properties such as position and rotation from a replay storage target.
This means you can extract any replay data from existing replay files and access it via code even if using a binary format.

The API aims to be as simple as possible, but it does require some understanding of how replays are structured in order to use correctly. The first thing to do is load our replay storage and read data from it. The storage could be in any form but for this example we will assume a relay file using the default binary format:

```cs
using UltimateReplay;
using UltimateReplay.ComponentData;
using UltimateReplay.Formatters;
using UltimateReplay.Storage;
using UnityEngine;

public class Example : MonoBehaviour
{
	void Start()
	{
		// Load our existing replay file
		ReplayStorage storage = ReplayFileStorage.FromFile("Example.replay");

		// We need to prepare the storage target for reading since we will be making direct read calls
		storage.Prepare(ReplayStorageAction.Read);
	}
}
```

Next we need to decide which data we would like to access. For example if you are only interested in data from the 5 second mark of the recording you can use the following to fetch the nearest snapshot:

```cs
// Try to get the snapshot for the 5 second mark
ReplaySnapshot snapshot = storage.FetchSnapshot(5.0f);
```

Alternatively we can access all data in order from the replay using the value known as the `Sequence ID`. It is a value that starts from 1, counts upwards and identifies each recorded snapshot in order. With that in mind we can load all recorded snapshots in order using the following approach:

```cs
for(int i = 1; i <= storage.SnapshotSize; i++)
{
	// Get the snapshot for sequence id 'i'
	ReplaySnapshot snapshot = storage.FetchSnapshot(i);
}
```

Once we have any given snapshot, we can then begin to extract data from it starting with all id's of the recorded objects along with their state data:

```cs
// Loop over all replay id's that are listed in this snapshot
foreach(ReplayIdentity id in snapshot.Identities)
{
	// Get the state data for the replay object with the given id
	// The resulting state will contain all information for all replay components associated with the target replay object
	ReplayState objectStateData = snapshot.RestoreSnapshot(id);
}
```

Once we have retrieved the `ReplayState` containing all recorded data for the object, we can then begin to unpack it by making use of formatters. All root level data is serialized by a `ReplayObjectFormatter` so we will need to use the same formatter in order to deserialize it:

```cs
// Create our formatter to handle the deserialization
ReplayObjectFormatter objectFormatter = ReplayFormatter.GetFormatterOfType<ReplayObjectFormatter>();

// Prepare the object state data to accept read calls
objectStateData.PrepareForRead();

// Deserialize the data into our formatter
objectFormatter.OnReplayDeserialize(objectStateData);

// Now that the formatter has deserialized the data, we can now access the information that was loaded
Debug.Log("Component Count: " + objectFormatter.ComponentStates.Count);
Debug.Log("Event Count: " + objectFormatter.EventStates.Count);
Debug.Log("Method Count: " + objectFormatter.MethodStates.Count);
Debug.Log("Variable Count: " + objectFormatter.VariableStates.Count);
```

As indicated by the names, `EventStates`, `MethodStates` and `VariableStates` contain all information relating to replay events, methods and variable respectively. We will be more interested in the `ComponentStates` for this example because it stores all information relating to replay components such as `ReplayTransform` or `ReplayAnimator`, etc.
Assuming we wanted to extract the transform information for the replay object we could use the following approach:

```cs
// Loop over all component states since the transform information could be stored at any slot
// Note also that there may be multiple transforms stored if child objects are also recorded
foreach(ReplayComponentData componentData in objectFormatter.ComponentStates)
{
	// Try to get the formatter for this component type as a transform formatter
	ReplayTransformFormatter transformFormatter = componentData.CreateFormatter() as ReplayTransformFormatter();

	// If not null then we have encountered some transform data
	if(transformFormatter != null)
	{
		// Prepare the compoent state data to accept read calls
		componentData.ComponentStateData.PrepareForRead();

		// Deserialize the data into out formatter
		transformFormatter.OnReplayDeserialize(componentData.ComponentStateData);
	}
}
```

Finally once we have deserialized our transform formatter, we will now be able to access the position and rotation values that were recorded:

```cs
// Get replay position
Vector3 position = transformFormatter.Position;

// Get replay rotation
Quaternion rotation = transformFormatter.Rotation;

// Log info
Debug.LogFormat("Replay Object: {0} - Pos = {1}, Rot = {2}", componentData.BehaviourIdentity, position, rotation);
```

You may have also noticed that it is possible via the `ReplayTransform` inspector to set which elements of a position and rotation are recorded, along with what space and accuracy were used while recording. 
The formatter provides easy access to all of that information also so there will be no issues if you only wanted to record the x position for example. In that case you should discard the data stored within `position.y` and `position.z` since it will be default.

```cs
Debug.Log("Position Recorded Axis: " + transformFormatter.PositionAxis);
Debug.Log("Position Recorded Space: " + transformFormatter.PositionSpace);
Debug.Log("Position Recorded Precision: " + transformFormatter.PositionPrecision);

// Simmilar properties are available for rotation and scale also
```

So that demonstrates how you can extract transform data for a particular replay object from any storage target. Although only a transform example was shown, similar principles apply for other components and you will find that other formatters such as `ReplayAudioFormatter` and `ReplayAnimationFormatter` also exist and provide the same sort of api.


The full code for this example:

```cs
using UltimateReplay;
using UltimateReplay.ComponentData;
using UltimateReplay.Formatters;
using UltimateReplay.Storage;
using UnityEngine;

public class Example : MonoBehaviour
{
	void Start()
	{
		// Load our existing replay file
		ReplayStorage storage = ReplayFileStorage.FromFile("Example.replay");

		// We need to prepare the storage target for reading since we will be making direct read calls
		storage.Prepare(ReplayStorageAction.Read);

		for(int i = 1; i < storage.SnapshotSize; i++)
		{
			// Get the snapshot for sequence id 'i'
			ReplaySnapshot snapshot = storage.FetchSnapshot(i);

			// Loop over all replay id's that are listed in this snapshot
			foreach(ReplayIdentity id in snapshot.Identities)
			{
				// Get the state data for the replay object with the given id
				// The resulting state will contain all information for all replay components associated with the target replay object
				ReplayState objectStateData = snapshot.RestoreSnapshot(id);

				// Create our formatter to handle the deserialization
				ReplayObjectFormatter objectFormatter = ReplayFormatter.GetFormatterOfType<ReplayObjectFormatter>();

				// Prepare the object state data to accept read calls
				objectStateData.PrepareForRead();

				// Deserialize the data into our formatter
				objectFormatter.OnReplayDeserialize(objectStateData);

				// Now that the formatter has deserialized the data, we can now access the information that was loaded
				Debug.Log("Component Count: " + objectFormatter.ComponentStates.Count);
				Debug.Log("Event Count: " + objectFormatter.EventStates.Count);
				Debug.Log("Method Count: " + objectFormatter.MethodStates.Count);
				Debug.Log("Variable Count: " + objectFormatter.VariableStates.Count);

				// Loop over all component states since the transform information could be stored at any slot
				// Note also that there may be multiple transforms stored if child objects are also recorded
				foreach(ReplayComponentData componentData in objectFormatter.ComponentStates)
				{
					// Try to get the formatter for this component type as a transform formatter
					ReplayTransformFormatter transformFormatter = componentData.CreateFormatter() as ReplayTransformFormatter();

					// If not null then we have encountered some transform data
					if(transformFormatter != null)
					{
						// Prepare the compoent state data to accept read calls
						componentData.ComponentStateData.PrepareForRead();

						// Deserialize the data into out formatter
						transformFormatter.OnReplayDeserialize(componentData.ComponentStateData);

						// Get replay position
						Vector3 position = transformFormatter.Position;

						// Get replay rotation
						Quaternion rotation = transformFormatter.Rotation;

						// Log info
						Debug.LogFormat("Replay Object: {0} - Pos = {1}, Rot = {2}", componentData.BehaviourIdentity, position, rotation);
					}
				}
			}
		}
	}
}
```
