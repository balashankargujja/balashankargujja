
# Service Lifetimes in DI

#### 1. Transient
- `services.AddTransient<TService, TImplementation>()`
- A new instance is created every time it is requested.
- Useful for lightweight, stateless services.
```c#
services.AddTransient<IMyService, MyService>();
```
#### 🧠 Think of it like:
Ordering a new pizza every time someone asks, even if they ask within the same minute.
#### 📌 Behavior:
- Every controller, every action, every injection gets a new instance.

#### 2. Scoped
- `services.AddScoped<TService, TImplementation>()`
- A single instance is created per HTTP request.
- Best choice for per-request operations, like database contexts.
```c#
services.AddScoped<IMyService, MyService>();
```
#### 🧠 Think of it like:
One waiter per table (request). Multiple dishes (dependencies) are served by the same waiter, but new waiters come for new tables.

#### 📌 Behavior:
- Same instance used throughout the same HTTP request.
- Different instances for different HTTP requests.

####  3. Singleton
- `services.AddSingleton<TService, TImplementation>()`
- A single instance is created once and shared forever.
- Use for stateless, thread-safe, and memory-efficient services.
```c#
services.AddSingleton<IMyService, MyService>();
```
#### 🧠 Think of it like:
One receptionist in an office – same person handles all visitor queries, forever.
#### 📌 Behavior:
- Created on app startup (or first request).
- Shared across all HTTP requests and users.

#### Summary Table
| Lifetime  | Instance Created | Shared Across Requests? | Suitable For                     |
| --------- | ---------------- | ----------------------- | -------------------------------- |
| Transient | Every injection  | ❌ No                    | Lightweight, stateless services  |
| Scoped    | Per HTTP Request | 🔁 Only within request  | EF DbContext, business logic     |
| Singleton | Once (app-wide)  | ✅ Yes                   | Caching, config, logger services |

#### Warning:
- Never use AddSingleton for services that depend on Scoped services like DbContext. This causes memory leaks or runtime errors.
