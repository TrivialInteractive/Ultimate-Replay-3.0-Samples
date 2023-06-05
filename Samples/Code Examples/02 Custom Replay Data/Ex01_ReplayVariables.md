
This quick example shows how you can quickly record custom data as part of a replay using replay variables.
Replay variables are just special fields that the replay system will serialize and deserialize automatically, and can also support interpolation for supported types such as float for value smoothing.

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

// IMPORTANT - Replay variables require that you derive from the ReplayBehaviour class in order to work correctly
public class Example : ReplayBehaviour
{
	// Declare a field using ReplayVar attribute which the replay system will detect
	[ReplayVar]
	public int myReplayVariable = 10;

	void Update()
	{
		// Modify the value while recording is occuring
		if(IsRecording == true)
		{
			myReplayVariable++;
		}
		// Log the value while replay is occuring
		else if (IsReplaying == true)
		{
			Debug.Log("myReplayVariable = " + myReplayVariable);
		}
	}
}
```
