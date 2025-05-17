# CancellationToken

```csharp
[HttpGet("long-task")]
public async Task<IActionResult> SomeLongTask(CancellationToken cancellationToken)
{
    try
    {
        for (int i = 0; i < 20; i++)
        {
            cancellationToken.ThrowIfCancellationRequested();
            await Task.Delay(1000, cancellationToken); // Giả lập task lâu
        }

        return Ok("OK!");
    }
    catch (OperationCanceledException)
    {
        return StatusCode(499, "Client đã hủy yêu cầu."); // 499: Custom client cancel code
    }
}
```

```javascript
import axios from "axios";

const controller = new AbortController();

axios.get("http://localhost:5000/api/some-long-task", {
  signal: controller.signal,
})
  .then((response) => console.log(response.data))
  .catch((error) => {
    if (axios.isCancel(error)) {
      console.log("Request bị hủy.");
    }
  });

// Hủy sau 3 giây
setTimeout(() => {
  controller.abort();
}, 3000);
```

```javascript
let controller: AbortController = new AbortController();
return (
        <>
            <div style={{ marginTop: 10 }}>
                <Button
                    variant="contained"
                    sx={{ mt: 3, mb: 2 }}
                    onClick={() => {
                        if (!controller.signal.aborted) {
                            axios.get("http://localhost:5013/Heavy/long-task", {
                                signal: controller.signal,
                            })
                                .then((response) => console.log(response.data))
                                .catch((error) => {
                                    if (axios.isCancel(error)) {
                                        alert("Request bị hủy.");
                                    }
                                });
                        } else {
                            controller = new AbortController();
                            axios.get("http://localhost:5013/Heavy/long-task", {
                                signal: controller.signal,
                            })
                                .then((response) => console.log(response.data))
                                .catch((error) => {
                                    if (axios.isCancel(error)) {
                                        alert("Request bị hủy.");
                                    }
                                });
                        }
                    }
                    }
                >Connect </Button>

                <Button onClick={
                    () => {
                        controller.abort();
                    }
                } >Cancel token</Button>
        </>
    )
```
