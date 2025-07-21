
# What is HttpContext

In ASP.NET Core, HttpContext represents all the information about an individual HTTP request and response. It’s available in:
- Middleware
- Controllers
- Razor pages
- Minimal APIs (via injection)
You access it like:
```c#
public async Task InvokeAsync(HttpContext context)
{
    // Use context.Request, context.Response, etc.
}
```
### 1: `HttpContext.Request`
Provides full info about the incoming HTTP request.

Key Members:
| Property      | Description                          |
| ------------- | ------------------------------------ |
| `Method`      | HTTP method: GET, POST, PUT, etc.    |
| `Path`        | URL path: `/api/products`            |
| `Query`       | Query parameters: `?id=5&sort=asc`   |
| `Headers`     | All request headers                  |
| `Cookies`     | All request cookies                  |
| `Body`        | Stream representing the request body |
| `ContentType` | MIME type (e.g., `application/json`) |
| `Scheme`      | `http` or `https`                    |
| `Host`        | Host info (e.g., `localhost:5000`)   |

#### 1. `Method`
- `HttpContext.Request.Method` returns a string like "GET", "POST", etc.
- This works in any controller because `ControllerBase` provides access to `HttpContext`.
- Example:
```c#
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;

namespace ExampleApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TestController : ControllerBase
    {
        [HttpGet]
        [HttpPost]
        [HttpPut]
        [HttpDelete]
        public IActionResult HandleRequest()
        {
            // Accessing the HTTP method
            var httpMethod = HttpContext.Request.Method;

            // Return the method used
            return Ok($"HTTP Method used: {httpMethod}");
        }
    }
}
```
Response:
```json
{
  "value": "HTTP Method used: POST"
}
```

#### 2. `Path`
- `HttpContext.Request.Path` gives you the full path portion of the URL (excluding query string).
- This is useful for logging, routing validation, and diagnostics.
- Example
```c#
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;

namespace ExampleApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TestController : ControllerBase
    {
        [HttpGet("getdata")] // catch-all route to test various paths
        public IActionResult GetPath()
        {
            // Access the request path
            var requestPath = HttpContext.Request.Path;

            // Return the path as a string
            return Ok($"Request path: {requestPath}");
        }
    }
}
```
Response:
```json
{
  "value": "Request path: /api/test/getdata"
}
```
#### 3. `QueryString`
- `HttpContext.Request.QueryString` – the query parameters as a string
- Example:
```c#
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;

namespace ExampleApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TestController : ControllerBase
    {
        // Specific route: /api/test/info/details
        [HttpGet("info/details")]
        public IActionResult GetRequestInfo()
        {
            var path = HttpContext.Request.Path;
            var queryString = HttpContext.Request.QueryString;
            var id = HttpContext.Request.Query["userId"];

            return Ok(new
            {
                Path = path.ToString(),
                Query = queryString.HasValue ? queryString.Value : "(no query parameters)"
            });
        }
    }
}
```
Response:
```json
{
  "Path": "/api/test/info/details",
  "Query": "?userId=99&active=true"
}
```
#### Optional: Include parsed query parameters
If you want to return each query parameter individually as key-value pairs:
```C#
var queryParams = HttpContext.Request.Query.ToDictionary(k => k.Key, v => v.Value.ToString());
```
#### 4. `Headers`

- You can access HTTP request headers via the `HttpContext.Request.Headers` property inside a controller.
```c#
[ApiController]
[Route("api/[controller]")]
public class SampleController : ControllerBase
{
    [HttpGet("read-headers")]
    public IActionResult ReadHeaders()
    {
        // Access a specific header (e.g., Authorization)
        if (HttpContext.Request.Headers.TryGetValue("Authorization", out var authHeader))
        {
            // Do something with authHeader
            return Ok($"Authorization header: {authHeader}");
        }

        return BadRequest("Authorization header not found");
    }
}
```
- `HttpContext.Request.Headers` is of type IHeaderDictionary, which behaves like a case-insensitive dictionary of key-value pairs.
- You can loop through all headers if needed:

```c#
foreach (var header in HttpContext.Request.Headers)
{
    Console.WriteLine($"{header.Key}: {header.Value}");
}
```
- Setting Headers on the Response
```c#
public IActionResult SetResponseHeader()
{
    HttpContext.Response.Headers["X-Response-Custom-Header"] = "CustomValue";
    return Ok("Header set");
}
```
You can also use .Add() if the header doesn't already exist:
```c#
HttpContext.Response.Headers.Add("X-New-Header", "SomeValue");
```
- Working with Strongly Typed Header Access
For built-in headers like Content-Type, User-Agent, etc., you can use typed access:
```c#
string contentType = HttpContext.Request.ContentType;
string userAgent = HttpContext.Request.Headers.UserAgent;
```
But for custom headers, stick with:
```c#
string custom = HttpContext.Request.Headers["X-Custom"];
```

### 4. `Cookies`
The `HttpContext.Request.Cookies` and `HttpContext.Response.Cookies` properties allow you to read and write HTTP cookies, respectively.
- Reading Cookies from the Request
```c#
[HttpGet("read-cookie")]
public IActionResult ReadCookie()
{
    if (HttpContext.Request.Cookies.TryGetValue("MyCookie", out string cookieValue))
    {
        return Ok($"Cookie Value: {cookieValue}");
    }

    return NotFound("Cookie not found.");
}
```
- Setting Cookies in the Response
Use HttpContext.Response.Cookies.Append(...) to send a cookie to the client.
```C#
[HttpGet("set-cookie")]
public IActionResult SetCookie()
{
    var cookieOptions = new CookieOptions
    {
        HttpOnly = true,         // Prevents JavaScript access
        Secure = true,           // Use HTTPS only
        SameSite = SameSiteMode.Strict,
        Expires = DateTimeOffset.UtcNow.AddMinutes(30)
    };

    HttpContext.Response.Cookies.Append("MyCookie", "ThisIsMyValue", cookieOptions);

    return Ok("Cookie set.");
}
```
- Deleting Cookies
```c#
[HttpGet("delete-cookie")]
public IActionResult DeleteCookie()
{
    HttpContext.Response.Cookies.Delete("MyCookie");
    return Ok("Cookie deleted.");
}
```
### 5. `Body`
- `HttpContext.Request.Body` is a `Stream`.
- It contains the raw byte stream of the request body (like JSON, form data, plain text, etc.).
- It's mostly used when you're not using model binding (i.e., [FromBody]) and want to manually read the incoming body.
```c#
[HttpPost("manual-read")]
public async Task<IActionResult> ManualRead()
{
    using var reader = new StreamReader(HttpContext.Request.Body);
    string body = await reader.ReadToEndAsync();

    return Ok(new { ReceivedBody = body });
}
```
Upload file using IFormFile
```c#
[HttpPost("upload-file")]
public async Task<IActionResult> UploadFile(IFormFile file)
{
    if (file == null || file.Length == 0)
        return BadRequest("No file uploaded.");

    var filePath = Path.Combine(Directory.GetCurrentDirectory(), "Uploads", file.FileName);

    Directory.CreateDirectory(Path.GetDirectoryName(filePath)); // Ensure directory exists

    using var stream = new FileStream(filePath, FileMode.Create);
    await file.CopyToAsync(stream);

    return Ok($"File saved as {filePath}");
}
```
If you're uploading large files, increase the request limits in Program.cs or appsettings.json.
```c#
builder.Services.Configure<FormOptions>(options =>
{
    options.MultipartBodyLengthLimit = 100_000_000; // 100 MB
});
```
### 6. `ContentType`
The ContentType property is available on both the request and response via HttpContext.Request and HttpContext.Response.
#### Reading the Request Content Type
You can read the ContentType of an incoming request like this:
```c#
[HttpPost]
public IActionResult MyAction()
{
    string requestContentType = HttpContext.Request.ContentType;

    if (requestContentType == "application/json")
    {
        // Handle JSON input
    }

    return Ok();
}
```
#### Setting the Response Content Type
```c#
[HttpGet]
public IActionResult MyAction()
{
    HttpContext.Response.ContentType = "application/json";

    var result = new { message = "Hello, world!" };
    return new JsonResult(result);
}
```
Note: If you're using JsonResult or returning objects directly, ASP.NET Core will usually set the content type for you to application/json.

### HttpContext.Response.StatusCode
The StatusCode property lets you get or set the HTTP status code for the response that your API sends to the client.

Syntax:
```c#
HttpContext.Response.StatusCode = 404;
```
It’s an int representing the HTTP status code (like 200, 404, 500, etc).
#### Best Practices:
- In controllers, prefer return StatusCode(404) or built-in helpers like NotFound(), BadRequest(), etc. Example:
```c#
return StatusCode(403);        // Custom code
return NotFound();             // 404
return Ok();                   // 200
return BadRequest("Invalid");  // 400
```
- For non-controller classes (like middleware), directly set HttpContext.Response.StatusCode.
```c#
app.Use(async (context, next) =>
{
    if (!context.User.Identity.IsAuthenticated)
    {
        context.Response.StatusCode = 401;
        await context.Response.WriteAsync("Unauthorized");
        return;
    }

    await next();
});
```
#### Tip:
Use status codes according to HTTP standards. Some common ones:
| Code | Meaning               |
| ---- | --------------------- |
| 200  | OK                    |
| 201  | Created               |
| 204  | No Content            |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 500  | Internal Server Error |

#### Recommended Way in Controllers
Instead of manually setting HttpContext.Response.StatusCode, it's better to use the built-in IActionResult return types for clarity and best practice.
| Desired Status Code | Recommended Return                         |
| ------------------- | ------------------------------------------ |
| 200 OK              | `return Ok();` or `return Ok(data);`       |
| 201 Created         | `return Created(uri, data);`               |
| 204 No Content      | `return NoContent();`                      |
| 400 Bad Request     | `return BadRequest();`                     |
| 401 Unauthorized    | `return Unauthorized();`                   |
| 403 Forbidden       | `return Forbid();`                         |
| 404 Not Found       | `return NotFound();`                       |
| 500 Internal Error  | `return StatusCode(500, "Error message");` |


# HttpContext.Response.Headers

The Headers property of HttpContext.Response allows you to read or write HTTP response headers directly.

Syntax:
```c#
HttpContext.Response.Headers["Header-Name"] = "value";
```
- The type is IHeaderDictionary
- Used to set or get headers before the response is sent
Example: Set a Custom Header
```c#
[HttpGet("custom-header")]
public IActionResult AddCustomHeader()
{
    HttpContext.Response.Headers["X-Custom-Header"] = "MyValue";
    return Ok("Header set");
}
```
Response:
```css
HTTP/1.1 200 OK
X-Custom-Header: MyValue
```
Important Rules
- Headers must be set before the response body is written or started
- You can overwrite existing headers, but some system-managed headers like Content-Length and Date may be ignored if modified
