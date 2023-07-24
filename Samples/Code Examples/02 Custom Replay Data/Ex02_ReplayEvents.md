
This quick example shows how you can easily record custom data as part of a replay using replay events.
Replay events can be used to record an action at a specific time stamp with optional associated data, and can be useful for one shot data that does not need to be continually recorded.

NOTE: Replay events will not be called when seeking to different timestamps in the recording.

```cs
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

// IMPORTANT - Replay events require that you derive from the ReplayBehaviour class in order to work correctly
public class Example : ReplayBehaviour
{
	// Each replay event should have a unique id value. It is good practice to use an enum if more than a few event types are required
	ushort spaceEventID = 25;
	ushort returnEventID = 44;

	// Replay events can be recorded at any time but in most cases you will mainly use `Update` or `OnReplayCapture` methods
	void Update()
	{
		// Record an event when user performs a space press
		if(Input.GetKeyDown(KeyCode.Space) == true)
		{
			// Get a replay state to store some custom data
			ReplayState data = ReplayState.pool.GetReusable();

			// Write some data with the event
			data.Write("Space key pressed!");

			// Record the event using ID and data.
			// Note - you can record an event with no data if required by passing 'null' for the data parameter.
			RecordEvent(spaceEventID, data);
		}

		// Record a parameterless event when user performs a return press
		if(Input.GetKeyDown(KeyCode.Return) == true)
		{
			// Record the event without any data
			RecordEvent(returnEventID);
		}
	}

	// Called during playback when the replay system comes across a replay event for the current time stamp
	protected override void OnReplayEvent(ushort eventID, ReplayState eventData)
	{
		// Check for space event type
		if(eventID == spaceEventID)
		{
			Debug.Log("Space event triggered = " + eventID);
			Debug.Log("Event data = " + eventData.ReadString());
		}
		// Check for return event type
		else if(eventID = returnEventID)
		{
			Debug.Log("Return event triggered = " + eventID);
		}
	}
}
```
