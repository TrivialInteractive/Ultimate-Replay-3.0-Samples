
This quick example shows how to check if a specific file is a valid replay file format. This can be useful for scanning all replay files in a given directory where there may also be other non-replay related files.

```cs

public class Example : MonoBehaviour
{
	void Start()
	{
		// Check if the specified file path is a valid replay file
		bool isReplayFile = ReplayFileStorage.IsReplayFile("MyReplays/Test.replay");

		// True if the file can be read by the replay system
		Debug.Log("IsReplayFile = " + isReplayFile);
	}
}
```
