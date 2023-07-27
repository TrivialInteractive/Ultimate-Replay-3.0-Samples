
This example shows how you can customize Ultimate Replay to implement a pooling system of your choice. By default the replay system will make use of the built in Unity pooling provided via `UnityEngine.Pool` namespace, however you can implement any other pooling solution, or even multiple pooling solutions in the same project.
To do that you will need to create a `Replay Object Lifecycle Provider` which is just a special script that is responsible for handling the creation and destruction of a specific replay prefab during playback.

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
	public override string ItemName => assignedPrefab != null ? assignedPrefab.gameObject.name : null;
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
	public override string ItemName => assignedPrefab != null ? assignedPrefab.gameObject.name : null;
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

Once you have implemented the lifecycle provider script, you can then proceed to setup the lifecycle provider in your Unity project with the following steps:

1. Create an asset from your lifecycle provider script for each replay prefab you want to manage. Since we added the `CreateAssetMenu` attribute we can simply right click in the project window and select `Create -> New Example Pool Boss Lifecycle Provider` and an asset will be created.

![image](https://github.com/TrivialInteractive/Ultimate-Replay-3.0-Samples/blob/412eb63535a5050786156061101c9a54c632f72b/Images/CodeExamples/ReplayLifecycleProviderCreateAsset.png)

2. With the asset still selected, we can now assign our `ReplayObject` prefab that will be managed in the insepctor window.

![image](https://github.com/TrivialInteractive/Ultimate-Replay-3.0-Samples/blob/412eb63535a5050786156061101c9a54c632f72b/Images/CodeExamples/ReplayLifecycleProviderAssignReplayObject.png)

3. Next we need to add our lifecycle provider to the replay settings so that the replay system is aware and is able to use our implementation. Open the replay settings window by going to `Tools -> Ultimate Replay 3.0 -> Settings` and then select the `Prefabs` tab. Next we need to click the `Add Custom` button to add a new entry into the list.

![image](https://github.com/TrivialInteractive/Ultimate-Replay-3.0-Samples/blob/412eb63535a5050786156061101c9a54c632f72b/Images/CodeExamples/ReplayLifecycleProviderAddProviderSettings.png)

4. Finally we can assign our lifecycle provider asset into this new slot and that is all the setup complete.

![image](https://github.com/TrivialInteractive/Ultimate-Replay-3.0-Samples/blob/412eb63535a5050786156061101c9a54c632f72b/Images/CodeExamples/ReplayLifecycleProviderAssignProviderSettings.png)


Now when you run the game using the replay prefabs feature, you will see that our custom pooling implementation is now used for the assigned replay object. You can repeat steps 1-4 for any other replay prefabs that you would also like to pool using the same lifecycle provider implementation.
