
This example shows how you can customize Ultimate Replay to implement a pooling system of your choice. By default the replay system will make use of the built in Unity pooling provided via `UnityEngine.Pool` namespace, however you can implement any other pooling solution, or even multiple pooling solutions in the same project.
To do that you will need to create a `Replay Object Lifecycle Provider` which is just a special script that is responsible for handling the creation and destruction of a specific replay object during playback.

Here is an example implementation without pooling support:

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Lifecycle;

// IMPORTANT - Custom lifecycle providers must derive from `ReplayObjectLifecycleProvider`
// NOTE - Custom lifecycle providers are scriptable objects and you will need to create an asset for each replay prefab you want to manage. For that reason it is easier to use the `CreateAssetMenu` attribute to make that process easier.
[CreateAssetMenu]
public class ExampleLifecycleProvider : ReplayObjectLifecycleProvider
{
	// The prefab that will will create or destroy - should be assigned in inspector
	public ReplayObject assignedPrefab;

	// Base implementation
	public override bool IsAssigned => assignedPrefab != null;
	public override bool ItemName => assignedPrefab != null : assignedPrefab.gameObject.name : null;
	public override ReplayIdentity ItemPrefabIdentity => assignedPrefab != null ? assignedPrefab.PrefabIdentity : ReplayIdentity.invalid;

	public override ReplayObject InstantiateReplayInstance(Vector3 position, Quaternion rotation)
	{
		// Default behaviour with no pooling
		// TODO - Replace this with some pooling implementation
		return Instantiate(assignedPrefab, position, rotation);
	}

	public override void DestroyReplayInstance(ReplayObject replayInstance)
	{
		// Default behaviour with no pooling
		// TODO - Replace this with some pooling implementation
		Destroy(replayInstance);
	}
}
```

You can see that the implementation is not too complex with only a few properties and methods needing to be implemented.
Currently this implementation does not perform any pooling but just uses the Unity instantiate/destroy methods. 
Next we can modify the script to make use of a 3rd party pooling asset of your choice.

For this example we will implement the 3rd party pooling asset `Pool Boss`, but any other asset could be implemented in much the same way:

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Lifecycle;
using DarkTonic.PoolBoss;

// IMPORTANT - Custom lifecycle providers must derive from `ReplayObjectLifecycleProvider`
// NOTE - Custom lifecycle providers are scriptable objects and you will need to create an asset for each replay prefab you want to manage. For that reason it is easier to use the `CreateAssetMenu` attribute to make that process easier.
[CreateAssetMenu]
public class ExamplePoolBossLifecycleProvider : ReplayObjectLifecycleProvider
{
	// The prefab that will will create or destroy - should be assigned in inspector
	public ReplayObject assignedPrefab;

	// Base implementation
	public override bool IsAssigned => assignedPrefab != null;
	public override bool ItemName => assignedPrefab != null : assignedPrefab.gameObject.name : null;
	public override ReplayIdentity ItemPrefabIdentity => assignedPrefab != null ? assignedPrefab.PrefabIdentity : ReplayIdentity.invalid;

	public override ReplayObject InstantiateReplayInstance(Vector3 position, Quaternion rotation)
	{
		// Hand over to our pooling implementation
		return PoolBoss.SpawnInPool(assignedPrefab.transform, position, rotation).GetComponent<ReplayObject>();
	}

	public override void DestroyReplayInstance(ReplayObject replayInstance)
	{
		// Hand over to pooling implementation
		PoolBoss.Despawn(replayInstance.transform);
	}
}
```
