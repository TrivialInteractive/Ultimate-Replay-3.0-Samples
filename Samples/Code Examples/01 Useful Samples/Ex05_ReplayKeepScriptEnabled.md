
This quick example shows how you can prevent a game script from being disabled by the replay system when using the default replay preparer.
The normal behaviour of the replay system is that all scripts attached to a ReplayObject hierarchy will be disabled during playback mode so that they cannot udpate an object that is being replayed causing potentially inaccurate replays.
This is not always desirable though so this example shows a couple of ways to prevent that default behaviour.

Example 1: Derive from `ReplayBehaviour` and the script will remain alive while in playback mode as shown in the sample code.

```cs
using UnityEngine;
using UltimateReplay;

public class Example : ReplayBehaviour
{
    void Update()
    {
        // Update will be called as normal by Unity even when in playback mode
        Debug.Log("Update called, IsReplaying = " + ReplayManager.IsReplayingAny);


        // You may only want to perform certain actions while this object is being recorded or replayed. You can check the following properties which can let you know the current replay object state
        if(IsReplaying == true)
        {
            // This replay object is being replayed - do some specific actions
            Debug.Log("Replaying Object");
        }

        if(IsRecording == true)
        {
            // This replay object is being recorded - do some specific actions
            Debug.Log("Recording Object");
        }

        // NOTE: It is possible for a single replay object to be both recorded and replayed at the same time, in which case both above properties would be true.
    }
}
```

Example 2: Use the special `ReplayPreparerIgnore` attribute to exclude your script from state preparation and it will remain enabled during playback mode. This option is useful if you can't derive from `ReplayObject' for any reason, or if you simply don't want the additional overhead that it would bring.

```cs
using UnityEngine;
using UltimateReplay;

// Add the attribute to a behaviour script and the default state preparer will ignore it 
[ReplayPreparerIgnore]
public class Example : MonoBehaviour
{
    // NOTE: This attribute is only applicable when using the `DefaultReplayPreparer`. If you implement a custom replay preparer then the attribute will have no effect unless your custom preparer implements code to take it into account.
}
```