
# Request Delegates

In .NET Core (specifically in ASP.NET Core), Request Delegates and the methods like app.Use(), app.Run(), and app.Map() are all part of the middleware pipeline, which is a key concept in how ASP.NET Core handles HTTP requests.
#### 1. What are Request Delegates?
A request delegate is a function that can process an HTTP request. It's used to build a pipeline of request handlers (middleware) that are executed in order.
#### Signature of a request delegate:
```c#
public delegate Task RequestDelegate(HttpContext context);
```
Each middleware receives an HttpContext and can:
- Do something with the request/response
- Optionally call the next middleware in the pipeline
#### 2. What is app.Use()?
app.Use() is used to add middleware to the pipeline. Each middleware can perform logic before and/or after the next middleware.
```c#
app.Use(async (context, next) =>
{
    // Logic before next middleware
    await next.Invoke(); // Call next middleware
    // Logic after next middleware
});
```
#### 3. What is app.Run()?
app.Run() is a terminal middleware, which means it does not call the next middleware in the pipeline. Once app.Run() executes, the request pipeline ends there.
```c#
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from Run middleware");
});
```
#### Difference between app.Use() and app.Run()
| Feature                | `app.Use()`                                    | `app.Run()`                                   |
| ---------------------- | ---------------------------------------------- | --------------------------------------------- |
| Middleware type        | Non-terminal (can call `next()`)               | Terminal (ends the pipeline)                  |
| Calls next middleware? | Yes (if you call `await next()`)               | No                                            |
| Use case               | Logging, Authentication, etc. (pipeline logic) | Final response (e.g., returning HTML or JSON) |

#### Simple Example
```c#
app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("Use 1 Start\n");
    await next();
    await context.Response.WriteAsync("Use 1 End\n");
});

app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("Use 2 Start\n");
    await next();
    await context.Response.WriteAsync("Use 2 End\n");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from Run()\n");
});
```
#### Output
```sql
Use 1 Start
Use 2 Start
Hello from Run()
Use 2 End
Use 1 End
```

### `Program.cs` (Minimal Hosting Model - .NET 6/7/8)
```c#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("➡️ Use Middleware 1 Start\n");
    await next(); // Pass control to the next middleware
    await context.Response.WriteAsync("⬅️ Use Middleware 1 End\n");
});

app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("➡️ Use Middleware 2 Start\n");
    await next(); // Pass control to the next middleware
    await context.Response.WriteAsync("⬅️ Use Middleware 2 End\n");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("✅ Final response from Run()\n");
});

app.Run();
```
You’ll get this output:
```sql
➡️ Use Middleware 1 Start
➡️ Use Middleware 2 Start
✅ Final response from Run()
⬅️ Use Middleware 2 End
⬅️ Use Middleware 1 End
```
Let's create a real-time custom middleware that logs the request path and timestamp, then plug it into the pipeline in your .NET 6+ Program.cs.
#### 1. Create Custom Middleware Class
```c#
// RequestLoggingMiddleware.cs
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public RequestLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var path = context.Request.Path;
        var time = DateTime.Now.ToString("HH:mm:ss");

        await context.Response.WriteAsync($"📝 [Middleware] Request to {path} at {time}\n");

        await _next(context); // Call the next middleware
    }
}
```
#### 2. Create an Extension Method (optional but clean)
```c#
// RequestLoggingMiddlewareExtensions.cs
public static class RequestLoggingMiddlewareExtensions
{
    public static IApplicationBuilder UseRequestLogging(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<RequestLoggingMiddleware>();
    }
}
```
#### 3. Use It in Program.cs
```c#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Use custom middleware
app.UseRequestLogging();

app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("➡️ Use Middleware 1 Start\n");
    await next();
    await context.Response.WriteAsync("⬅️ Use Middleware 1 End\n");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("✅ Final response from Run()\n");
});

app.Run();
```
#### Example Output in Browser
```sql
📝 [Middleware] Request to / at 22:30:01
➡️ Use Middleware 1 Start
✅ Final response from Run()
⬅️ Use Middleware 1 End
```
