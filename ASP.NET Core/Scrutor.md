
# What is Scrutor?

Scrutor is a NuGet library that adds assembly scanning and decoration support to the built-in ASP.NET Core DI container.

Install it:
```bash
dotnet add package Scrutor
```
#### Why Use Scrutor?
- Automatically register all services based on naming conventions.
- No need to manually add each service.
- Supports AddTransient, AddScoped, AddSingleton.

Let's say you have:
```markdown
/Services
    IUserService.cs
    UserService.cs
    IOrderService.cs
    OrderService.cs
```
#### 1. Create the interface and implementation
```csharp
// Services/IUserService.cs
public interface IUserService
{
    void DoSomething();
}

// Services/UserService.cs
public class UserService : IUserService
{
    public void DoSomething()
    {
        Console.WriteLine("User service running...");
    }
}
```
#### 2. Register using Scrutor
```csharp
// Program.cs or in your ServiceCollectionExtensions.cs

using Scrutor;

builder.Services.Scan(scan => scan
    .FromAssemblyOf<IUserService>() // or .FromCallingAssembly() if in same project
        .AddClasses(classes => classes.Where(type => type.Name.EndsWith("Service")))
            .AsImplementedInterfaces()
            .WithScopedLifetime()
);
```
This will register:
```csharp
UserService => IUserService
OrderService => IOrderService
```
with scoped lifetime.

#### Optional: Use Custom Extension Method for Organization
```csharp
// Extensions/ServiceCollectionExtensions.cs

public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddBusinessServices(this IServiceCollection services)
    {
        services.Scan(scan => scan
            .FromAssemblyOf<IUserService>() // Replace with your marker type
                .AddClasses(c => c.Where(type => type.Name.EndsWith("Service")))
                .AsImplementedInterfaces()
                .WithScopedLifetime()
        );

        return services;
    }
}
```
Then in `Program.cs`:
```csharp
builder.Services.AddBusinessServices();
```
Lifetime Options
- `.WithScopedLifetime()`
- `.WithSingletonLifetime()`
- `.WithTransientLifetime()`

Summary
| Feature              | Description                    |
| -------------------- | ------------------------------ |
| 📦 Auto Registration | Scans assemblies for services  |
| ⚙️ Convention-Based  | Uses naming (e.g., `*Service`) |
| 🧼 Clean Code        | No manual registration clutter |

#### Why FromAssemblyOf<IUserService>()?
- `IUserService` is used just as a marker to say:
👉 “Start scanning from the assembly where IUserService lives.”

#### Alternatives You Can Use
Depending on your project structure, you can use:

#### Option 1: `.FromAssemblyOf<SomeType>()`
```c#
.FromAssemblyOf<IUserService>()
```
- Scans the same assembly that contains IUserService.
- Use any known type in the same assembly (interface or class).

#### Option 2: `.FromCallingAssembly()`
```c#
.FromCallingAssembly()
```
- Scans the currently running assembly (usually the main Web API project).
- Useful when you're registering in the same project.

#### Option 3: `.FromAssemblies(...) or .FromAssemblies(AppDomain.CurrentDomain.GetAssemblies())`
```c#
.FromAssemblies(typeof(IUserService).Assembly, typeof(IOtherService).Assembly)
```
- Scans multiple assemblies.
- Good when you split services into multiple class libraries.

#### ✅ Best Practices
| Situation                        | Recommended Approach                   |
| -------------------------------- | -------------------------------------- |
| Services are in the same project | `.FromCallingAssembly()`               |
| Services in a class library      | `.FromAssemblyOf<SomeTypeInLibrary>()` |
| Services in multiple libraries   | `.FromAssemblies(...)`                 |
