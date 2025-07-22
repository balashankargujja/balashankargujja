
# HttpContext.Session

`HttpContext.Session` is a key-value storage mechanism for storing user data per session, across multiple HTTP requests.
- Accessed via `HttpContext.Session` inside Controllers or Middleware
- You must:
    - Enable a session middleware
    - Configure session services
    - Use Set, Get, and Remove methods
#### Setup in ASP.NET Core Web API
```c#
var builder = WebApplication.CreateBuilder(args);

// Add session services
builder.Services.AddDistributedMemoryCache(); // or Redis
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

builder.Services.AddControllers();

var app = builder.Build();

// Use session middleware
app.UseSession();

app.MapControllers();
app.Run();
```
#### Using HttpContext.Session in a Controller
```c#
[ApiController]
[Route("api/[controller]")]
public class SessionDemoController : ControllerBase
{
    [HttpGet("set")]
    public IActionResult SetSession()
    {
        HttpContext.Session.SetString("Username", "Balashankar");
        HttpContext.Session.SetInt32("UserId", 101);
        return Ok("Session values set.");
    }

    [HttpGet("get")]
    public IActionResult GetSession()
    {
        var username = HttpContext.Session.GetString("Username");
        var userId = HttpContext.Session.GetInt32("UserId");

        return Ok(new
        {
            Username = username,
            UserId = userId
        });
    }

    [HttpGet("remove")]
    public IActionResult RemoveSession()
    {
        HttpContext.Session.Remove("Username");
        return Ok("Username removed from session.");
    }

    [HttpGet("clear")]
    public IActionResult ClearSession()
    {
        HttpContext.Session.Clear();
        return Ok("All session data cleared.");
    }
}
```
#### Session Data Methods
| Method                        | Description        |
| ----------------------------- | ------------------ |
| `SetString(key, value)`       | Stores a string    |
| `GetString(key)`              | Retrieves a string |
| `SetInt32(key, value)`        | Stores an int      |
| `GetInt32(key)`               | Retrieves an int   |
| `Set(key, byte[])`            | Stores raw data    |
| `TryGetValue(key, out value)` | Gets raw byte\[]   |
| `Remove(key)`                 | Removes one item   |
| `Clear()`                     | Removes all        |

#### Gotchas & Notes
- Session only works after UseSession() middleware
- It only works for stateful clients (like browsers) — not ideal for pure REST APIs
- Not suitable for large-scale microservices unless using distributed session (Redis)
- If you're using IHttpContextAccessor, you can also access session inside services

#### Optional: Access Session Outside Controller
```c#
public class MyService
{
    private readonly IHttpContextAccessor _contextAccessor;

    public MyService(IHttpContextAccessor accessor)
    {
        _contextAccessor = accessor;
    }

    public string GetUsername()
    {
        return _contextAccessor.HttpContext?.Session?.GetString("Username");
    }
}
```
Also register `IHttpContextAccessor:`
```c#
builder.Services.AddHttpContextAccessor();
```
