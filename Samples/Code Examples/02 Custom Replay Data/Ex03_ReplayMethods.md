
This quick example shows how you can easily record custom data as part of a replay using replay methods.
Replay methods can be used to record a method call (Similar to networking RPC's) at a specific time stamp with optional arguments (Primitive or IReplaySerializable), and can be useful for one shot data that does not need to be continually recorded.
Replay methods are generally slightly easier to work with than replay events, since there is no need to serialize additional data manually (Replay methods serialize arguments automatically), but note that replay methods have slightly more overhead so may not be ideal in high performance scenarios.

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

// IMPORTANT - Replay methods require that you derive from the ReplayBehaviour class in order to work correctly
public class Example : ReplayBehaviour
{
	// Replay methods can be recorded at any time but in most cases you will mainly use `Update` or `OnReplayCapture` methods
	void Update()
	{
		// Record a method call when user performs a space press
		if(Input.GetKeyDown(KeyCode.Space) == true)
		{
			// This will record the method call as part of the replay and the method will be called during playback once the same playback time has been reached.
			// NOTE: Recording the method call will also cause the method to be invoked immediatley. If that is not desirable, you can simply return from the replay method if `IsRecording` is true.
			RecordMethodCall(MyReplayableMethod, 123, "Hello World");
		}
	}

	// It is important to add the `ReplayMethod` attribute otherwise the method will not be called during playback
	[ReplayMethod]
	void MyReplayableMethod(int a, string b)
	{
		Debug.LogFormat("Replay method called: IsRecording = {0}: Args = {1}, {2}", IsRecording a, b);
	}
}
```
