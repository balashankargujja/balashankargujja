
# Extension Methods for Service Registration

In .NET Core (especially in ASP.NET Core), it's a common practice to organize and register groups of services using extension methods to keep your Startup.cs or Program.cs clean and modular.

#### Why Use Extension Methods for Service Registration?
- Clean separation of concerns
- Reusable across multiple projects
- Easier to maintain and test

#### Basic Structure
- Create a static class (e.g., ServiceCollectionExtensions.cs)
- Add an extension method on IServiceCollection
- Group related service registrations inside that method

### Example: Registering Repositories and Services
#### Step 1: Create extension class
```c#
// Extensions/ServiceCollectionExtensions.cs
using Microsoft.Extensions.DependencyInjection;
using MyApp.Services;
using MyApp.Repositories;

namespace MyApp.Extensions
{
    public static class ServiceCollectionExtensions
    {
        public static IServiceCollection AddMyAppServices(this IServiceCollection services)
        {
            // Add business logic services
            services.AddScoped<IUserService, UserService>();
            services.AddScoped<IOrderService, OrderService>();

            return services;
        }

        public static IServiceCollection AddMyAppRepositories(this IServiceCollection services)
        {
            // Add data access repositories
            services.AddScoped<IUserRepository, UserRepository>();
            services.AddScoped<IOrderRepository, OrderRepository>();

            return services;
        }
    }
}
```
#### Step 2: Call in `Program.cs`
```c#
var builder = WebApplication.CreateBuilder(args);

// Register services via extension methods
builder.Services.AddMyAppServices();
builder.Services.AddMyAppRepositories();

var app = builder.Build();
```

`Advanced Tip:` Register Services by Convention (Optional)
You can also register all services by scanning assemblies using reflection (e.g., Autofac or Scrutor), but the extension method approach is easier for beginners and very common in real-world apps.
