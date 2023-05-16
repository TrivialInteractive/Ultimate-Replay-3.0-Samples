
This quick example shows how to stop a running recording using the ReplayManager API
Note: Recordings must be stopped before they can be replayed

```cs
using System.Collections;
using UnityEngine;
using UltimateReplay;
using UltimateReplay.Storage;

public class Example : MonoBehaviour
{
    // Our running record operation created in the previous example
    // We assume this is assigned for this sample
    private ReplayRecordOperation recordOp = null;

    IEnumerator Start()
    {
        // Wait some time so that we can record some data
        yield return new WaitForSeconds(5);

        // Stop recording
        recordOp.StopRecording();

        // IMPORTANT - recordOp will be disposed after the `StopRecording` call.
    }
}
```
