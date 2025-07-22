
# HttpContext.Connection

HttpContext.Connection provides metadata about the network connection used to send the current HTTP request.

It's part of Microsoft.AspNetCore.Http and exposes connection-level info like:
- Client IP and Port
- Server IP and Port
- TLS details (if HTTPS)

#### Key Properties of HttpContext.Connection
| Property                    | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| `RemoteIpAddress`           | IP address of the client making the request                  |
| `RemotePort`                | Port on the client used for the request                      |
| `LocalIpAddress`            | IP address of the server receiving the request               |
| `LocalPort`                 | Port on the server that accepted the request                 |
| `Id`                        | Unique identifier for the connection                         |
| `ClientCertificate`         | X.509 client certificate (for mutual TLS)                    |
| `ConnectionClosedRequested` | Checks if connection close was requested (in newer versions) |

#### Example in Controller
```c#
[ApiController]
[Route("api/[controller]")]
public class ConnectionInfoController : ControllerBase
{
    [HttpGet("info")]
    public IActionResult GetConnectionInfo()
    {
        var connection = HttpContext.Connection;

        var info = new
        {
            ConnectionId = connection.Id,
            RemoteIpAddress = connection.RemoteIpAddress?.ToString(),
            RemotePort = connection.RemotePort,
            LocalIpAddress = connection.LocalIpAddress?.ToString(),
            LocalPort = connection.LocalPort
        };

        return Ok(info);
    }
}
```
Output:
```json
{
  "ConnectionId": "0HLR23G3NV2MJ",
  "RemoteIpAddress": "::1",
  "RemotePort": 53422,
  "LocalIpAddress": "::1",
  "LocalPort": 5000
}
```
