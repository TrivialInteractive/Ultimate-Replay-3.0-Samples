
This example shows how you can load replay prefabs from external locations such as asset bundles instead of forcing replay prefabs to exist in the game project.
To do this we will need to use the `Replay Object Lifecycle Provider` feature in a similar way to the previous pooling example, however this time we will be loading the asset from an asset bundle which could come from file or any other location.

Here is a simple implementation that loads the asset bundle and replay object on demand:

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Lifecycle;

// IMPORTANT - Custom lifecycle providers must derive from `ReplayObjectLifecycleProvider`
// NOTE - Custom lifecycle providers are scriptable objects and you will need to create an asset for each replay prefab you want to manage. For that reason it is easier to use the `CreateAssetMenu` attribute to make that process easier.
[CreateAssetMenu]
public class AssetBundleLifecycleProvider : ReplayObjectLifecycleProvider
{
	private AssetBundle bundle = null;
	private ReplayObject asset - null;

	// Asset bundle paths where the bundle and asset should be loaded from - Should be assigned in inspector
	public string assetBundlePath = "AssetBundles/ReplayPrefabs";
	public string prefabAssetPath = "Prefabs/ReplayCube";

	// Should match target ReplayObject.PrefabIdentity value
	public uint prefabIdentity = 0;

	// Base implementation
	public override bool IsAssigned => string.IsNullOrEmpty(asssetBundlePath) == false && string.IsNullOrEmpty(prefabAssetPath) == false && prefabIdentity != 0;
	public override string ItemName => prefabAssetPath;
	public override ReplayIdentity ItemPrefabIdentity => new ReplayIdentity(prefabIdentity);

	public override ReplayObject InstantiateReplayInstance(Vector3 position, Quaternion rotation)
	{
		// Load bundle
		if(bundle == null)	
			bundle = AssetBundle.LoadFromFile(assetBundlePath);

		// Load asset
		if(asset == null && bundle != null)
			asset = bundle.LoadAsset<ReplayObject>(prefabAssetPath);

		// Create instance
		return asset != null ? Instantiate(asset, position, rotation) : null;
	}

	public override void DestroyReplayInstance(ReplayObject replayInstance)
	{
		Destroy(replayInstance);
	}
}
```

This asset bundle lifecycle provider can be registered with the replay system in the same way as outlined in the previous example steps 1-4.

As you can see this simple implementation will allow a replay prefab to be loaded from an external asset bundle once setup via the replay settings.
In anything more than a sample project, it would be better practice to implement some form of asset bundle manager perhaps with async loading at startup that also has the capabilities of unloading unused asset bundles, but this example shows the main idea.

Although this example shows an implementation using asset bundles, the same concept can be applied to other scenarios such as addressables without too much change to the code.

One thing to note from this example is that `prefabIdentity` must be assigned manually to match the target replay prefab `PrefabIdentity` value. This value is used by the replay system to identify the prefab so it is important that this value is set and always in sync (Although `PrefabIdentity` should never change once generated). In some cases it may be possible to write some editor only code which is capable of auto-filling that field, however that is beyond the scope of this example.
