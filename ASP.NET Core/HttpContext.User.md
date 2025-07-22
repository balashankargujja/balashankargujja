
# HttpContext.User

`HttpContext.User` represents the authenticated user making the request. It's a `ClaimsPrincipal` object that contains all the claims (like username, roles, email) associated with that user.

Type
```c#
public ClaimsPrincipal User { get; }
```
#### 🔐 Used in Authentication & Authorization
- After a user logs in (via JWT, cookies, OAuth, etc.), their identity is stored in `HttpContext.User`
- It's used to check:
    - Who the user is
    - What roles or claims they have
#### Usage 1: Accessing Current User Information
```c#
[HttpGet("getdata")]
public IActionResult GetCurrentUser()
{
    var username = HttpContext.User.Identity.Name;  // usually from "sub" or "name" claim
    var isAuthenticated = HttpContext.User.Identity.IsAuthenticated;

    return Ok(new { username, isAuthenticated });
}
```
#### Usage 2:  Accessing Claims
```c#
[HttpGet("claims")]
public IActionResult GetClaims()
{
    var claims = HttpContext.User.Claims
                  .Select(c => new { c.Type, c.Value })
                  .ToList();

    return Ok(claims);
}
```
#### Usage 3:  Get Specific Claim
```c#
var email = HttpContext.User.FindFirst("email")?.Value;
var userId = HttpContext.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
```

#### Usage 4:  Role-Based Authorization
Use `[Authorize(Roles = "Admin")]` to restrict access, and check role like:
```c#
bool isAdmin = HttpContext.User.IsInRole("Admin");
```
#### Usage 5: Use Inside Services (not controller)?
Inject `IHttpContextAccessor:`
```c#
public class MyService
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public MyService(IHttpContextAccessor accessor)
    {
        _httpContextAccessor = accessor;
    }

    public string GetUsername()
    {
        return _httpContextAccessor.HttpContext.User.Identity.Name;
    }
}
```
And register in `Program.cs:`
```c#
builder.Services.AddHttpContextAccessor();
```
#### Example with JWT Auth Flow
If you're using JWT Bearer Authentication, and the token includes claims like:
```json
{
  "sub": "user123",
  "email": "user@example.com",
  "role": "Admin"
}
```
Then you can extract those from `HttpContext.User` as shown above.
#### 🧠 Summary
| Property/Method                 | Description                         |
| ------------------------------- | ----------------------------------- |
| `User.Identity.Name`            | Gets the username (if available)    |
| `User.Identity.IsAuthenticated` | `true` if the user is authenticated |
| `User.Claims`                   | All claims (name, email, role, etc) |
| `User.FindFirst("claimType")`   | Gets specific claim                 |
| `User.IsInRole("RoleName")`     | Checks if user is in a role         |
