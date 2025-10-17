
# Async and Await Usage

A brief description of what this project does and who it's for

### 🌱 1. The Basic Idea
Imagine this:
- You go to a restaurant and order food 🍛.
- While your food is being cooked, you don’t stand still and wait.
- You might scroll your phone, talk to a friend, etc.
- When food is ready, the waiter notifies you.

👉 That’s asynchronous behavior.

Now imagine the opposite — you stand near the counter, waiting for food and do nothing else until it’s ready.
That’s synchronous behavior.

#### 💡 In C# terms:

- Synchronous method: Code waits until the operation finishes.
- Asynchronous method: Code starts a task (like fetching data from DB or API) and continues doing other work. When the task finishes, the program resumes from where it left off.

### 🧠 2. Why Async is Needed in Web APIs

When your API calls a database, it may take time — like 1–2 seconds.

If you use normal (sync) code, the server thread is blocked during that time ⛔.

If you use async, the thread is freed up and can handle other requests 💨.

So — Async makes your API faster, scalable, and responsive, especially when there are many users.

### ⚙️ 3. How Async Works in Code

Here’s a simple example 👇

🧩 Without Async:
```c#
public IEnumerable<User> GetUsers()
{
    var users = _context.Users.ToList(); // waits here
    return users;
}
```
When this runs, your thread waits until all users are fetched.

🧩 With Async:
```c#
public async Task<IEnumerable<User>> GetUsersAsync()
{
    var users = await _context.Users.ToListAsync(); // doesn't block
    return users;
}
```
Let’s break it down 🔍
| Part                      | Meaning                                                                       |
| ------------------------- | ----------------------------------------------------------------------------- |
| `async`                   | tells C# that this method uses asynchronous operations                        |
| `Task<IEnumerable<User>>` | means this method returns a “task” (work that completes in future)            |
| `await`                   | means “pause here until this operation completes, but don’t block the thread” |
| `ToListAsync()`           | EF Core provides async versions of DB methods                                 |

### 💬 4. Step-by-step flow

- You call GetUsersAsync()
- It runs ToListAsync() → starts fetching data
- While DB query runs, your thread can handle other web requests
- When DB data is ready, the method resumes after await
- Returns the list of users

| Term            | Meaning (simple)                                       |
| --------------- | ------------------------------------------------------ |
| `async`         | Marks a method that uses asynchronous code             |
| `await`         | Waits for a background operation to finish             |
| `Task`          | A “promise” that something will complete in the future |
| `ToListAsync()` | Async version of `ToList()` provided by EF Core        |

### 🧩 5. Query Execution Methods

These methods actually execute the SQL query and fetch data.
| Sync Method         | Async Method             | Description                                   |
| ------------------- | ------------------------ | --------------------------------------------- |
| `ToList()`          | `ToListAsync()`          | Executes query and returns all rows as a list |
| `First()`           | `FirstAsync()`           | Gets the first record (throws error if none)  |
| `FirstOrDefault()`  | `FirstOrDefaultAsync()`  | Gets the first record or `null` if none       |
| `Single()`          | `SingleAsync()`          | Expects exactly one record (error if 0 or >1) |
| `SingleOrDefault()` | `SingleOrDefaultAsync()` | Gets single record or `null`                  |
| `Last()`            | `LastAsync()`            | Gets last record (error if none)              |
| `LastOrDefault()`   | `LastOrDefaultAsync()`   | Gets last record or `null`                    |
| `Count()`           | `CountAsync()`           | Counts rows in result                         |
| `LongCount()`       | `LongCountAsync()`       | Same as above but for long type               |
| `Any()`             | `AnyAsync()`             | Checks if any records exist                   |
| `All()`             | `AllAsync()`             | Checks if all records satisfy condition       |
| `Max()`             | `MaxAsync()`             | Gets maximum value                            |
| `Min()`             | `MinAsync()`             | Gets minimum value                            |
| `Average()`         | `AverageAsync()`         | Calculates average                            |
| `Sum()`             | `SumAsync()`             | Calculates total sum                          |

### 🧩 2. Entity Retrieval by Key
| Sync Method                       | Async Method                           | Description                        |
| --------------------------------- | -------------------------------------- | ---------------------------------- |
| `Find()`                          | `FindAsync()`                          | Finds an entity by its primary key |
| `Find(params object[] keyValues)` | `FindAsync(params object[] keyValues)` | Overload version for multiple keys |

Example :
```c#
var user = await _context.Users.FindAsync(1);
```

### 🧩 3. Save / Update Operations
| Sync Method     | Async Method         | Description                           |
| --------------- | -------------------- | ------------------------------------- |
| `SaveChanges()` | `SaveChangesAsync()` | Saves all pending changes to DB       |

📘 Example:
```c#
await _context.Users.AddAsync(newUser);
await _context.SaveChangesAsync();
```

### 🧩 4. Raw SQL Execution
| Sync Method                         | Async Method                               | Description                                          |
| ----------------------------------- | ------------------------------------------ | ---------------------------------------------------- |
| `FromSqlRaw()`                      | *(Same name but used with awaitable LINQ)* | Query using raw SQL (await `ToListAsync()` after it) |
| `Database.ExecuteSqlRaw()`          | `Database.ExecuteSqlRawAsync()`            | Execute non-query SQL (INSERT, UPDATE, DELETE)       |

📘 Example:
```c#
await _context.Database.ExecuteSqlRawAsync("EXEC proc_UpdateUser @Id={0}", id);
```

### 🧩 5. Change Tracking (Indirect Async)
| Sync Method | Async Method    | Description                       |
| ----------- | --------------- | --------------------------------- |
| `Reload()`  | `ReloadAsync()` | Reloads entity data from database |

```c#
await _context.Entry(user).ReloadAsync();
```

### 🧩 6. Asynchronous Streaming (Advanced)
EF Core supports asynchronous iteration with await foreach (C# 8+).
📘 Example:
```c#
await foreach (var user in _context.Users.AsAsyncEnumerable())
{
    Console.WriteLine(user.Name);
}
```
✅ Advantage: Fetches records one by one instead of loading all at once (memory-efficient).

### 🧩 7. Transactions
| Sync Method          | Async Method              | Description                |
| -------------------- | ------------------------- | -------------------------- |
| `BeginTransaction()` | `BeginTransactionAsync()` | Starts a new transaction   |
| `Commit()`           | `CommitAsync()`           | Commits the transaction    |
| `Rollback()`         | `RollbackAsync()`         | Rolls back the transaction |

Example:
```c#
using var transaction = await _context.Database.BeginTransactionAsync();
try
{
    // Perform multiple DB actions here
    await _context.SaveChangesAsync();
    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
}
```

### 🧩 8. Migration and Database Management
| Sync Method       | Async Method           | Description                      |
| ----------------- | ---------------------- | -------------------------------- |
| `EnsureCreated()` | `EnsureCreatedAsync()` | Creates DB if not exists         |
| `EnsureDeleted()` | `EnsureDeletedAsync()` | Deletes DB if exists             |
| `CanConnect()`    | `CanConnectAsync()`    | Checks if DB connection is valid |
| `Migrate()`       | `MigrateAsync()`       | Applies pending migrations       |

📘 Example:
```c#
if (!await _context.Database.CanConnectAsync())
{
    await _context.Database.MigrateAsync();
}
```

### 🧩 9. LINQ Extension Async Methods (from EF Core)
You can also use async LINQ methods from:
```c#
using Microsoft.EntityFrameworkCore;
```
Examples:
```c#
await _context.Users.Where(u => u.IsActive).CountAsync();
await _context.Users.OrderBy(u => u.Name).ToListAsync();
await _context.Users.AnyAsync(u => u.Email == "balu@gmail.com");
```

### 🧾 10. Summary Table (Cheat Sheet)
| Category           | Async Methods                                                                                               |
| ------------------ | ----------------------------------------------------------------------------------------------------------- |
| **Query Results**  | `ToListAsync`, `FirstOrDefaultAsync`, `SingleAsync`, `CountAsync`, `AnyAsync`, `SumAsync`, `MaxAsync`, etc. |
| **Entity Find**    | `FindAsync`                                                                                                 |
| **Save Data**      | `AddAsync`, `AddRangeAsync`, `SaveChangesAsync`                                                             |
| **Raw SQL**        | `ExecuteSqlRawAsync`, `ExecuteSqlInterpolatedAsync`                                                         |
| **Transactions**   | `BeginTransactionAsync`, `CommitAsync`, `RollbackAsync`                                                     |
| **DB Management**  | `EnsureCreatedAsync`, `EnsureDeletedAsync`, `MigrateAsync`, `CanConnectAsync`                               |
| **Reloading Data** | `ReloadAsync`                                                                                               |
| **Streaming**      | `AsAsyncEnumerable()`                                                                                       |

Everything in one place 💪

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using MyWebApi.Data;
using MyWebApi.Models;

namespace MyWebApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private readonly AppDbContext _context;

        public UsersController(AppDbContext context)
        {
            _context = context;
        }

        // ============================================================
        // 1️⃣ GET ALL USERS
        // ============================================================
        [HttpGet]
        public async Task<IActionResult> GetUsers()
        {
            var users = await _context.Users.ToListAsync();
            return Ok(users);
        }

        // ============================================================
        // 2️⃣ GET SINGLE USER BY ID
        // ============================================================
        [HttpGet("{id}")]
        public async Task<IActionResult> GetUserById(int id)
        {
            var user = await _context.Users.FindAsync(id);

            if (user == null)
                return NotFound();

            return Ok(user);
        }

        // ============================================================
        // 3️⃣ ADD NEW USER
        // ============================================================
        [HttpPost]
        public async Task<IActionResult> AddUser(User user)
        {
            await _context.Users.AddAsync(user);
            await _context.SaveChangesAsync();

            return CreatedAtAction(nameof(GetUserById), new { id = user.Id }, user);
        }

        // ============================================================
        // 4️⃣ UPDATE EXISTING USER
        // ============================================================
        [HttpPut("{id}")]
        public async Task<IActionResult> UpdateUser(int id, User updatedUser)
        {
            var user = await _context.Users.FindAsync(id);

            if (user == null)
                return NotFound();

            user.Name = updatedUser.Name;
            user.Email = updatedUser.Email;

            await _context.SaveChangesAsync();
            return Ok(user);
        }

        // ============================================================
        // 5️⃣ DELETE USER
        // ============================================================
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteUser(int id)
        {
            var user = await _context.Users.FindAsync(id);

            if (user == null)
                return NotFound();

            _context.Users.Remove(user);
            await _context.SaveChangesAsync();

            return NoContent();
        }

        // ============================================================
        // 6️⃣ COUNT USERS (Using CountAsync)
        // ============================================================
        [HttpGet("count")]
        public async Task<IActionResult> GetUserCount()
        {
            int count = await _context.Users.CountAsync();
            return Ok(count);
        }

        // ============================================================
        // 7️⃣ CHECK IF USER EXISTS (Using AnyAsync)
        // ============================================================
        [HttpGet("exists/{email}")]
        public async Task<IActionResult> CheckUserExists(string email)
        {
            bool exists = await _context.Users.AnyAsync(u => u.Email == email);
            return Ok(exists);
        }

        // ============================================================
        // 8️⃣ RUN RAW SQL QUERY (FromSqlRaw)
        // ============================================================
        [HttpGet("raw")]
        public async Task<IActionResult> GetUsersByRawSql()
        {
            var users = await _context.Users
                .FromSqlRaw("SELECT * FROM Users WHERE Id > {0}", 0)
                .ToListAsync();

            return Ok(users);
        }

        // ============================================================
        // 9️⃣ EXECUTE STORED PROCEDURE (FromSqlInterpolated)
        // ============================================================
        [HttpGet("proc")]
        public async Task<IActionResult> GetUsersFromProc()
        {
            var users = await _context.Users
                .FromSqlInterpolated($"EXEC proc_getallusers")
                .ToListAsync();

            return Ok(users);
        }

        // ============================================================
        // 🔟 DATABASE TRANSACTION (BeginTransactionAsync)
        // ============================================================
        [HttpPost("transaction")]
        public async Task<IActionResult> CreateTwoUsers()
        {
            using var transaction = await _context.Database.BeginTransactionAsync();

            try
            {
                var user1 = new User { Name = "Balu", Email = "balu@example.com" };
                var user2 = new User { Name = "Shankar", Email = "shankar@example.com" };

                await _context.Users.AddAsync(user1);
                await _context.Users.AddAsync(user2);

                await _context.SaveChangesAsync();
                await transaction.CommitAsync();

                return Ok("Both users added successfully!");
            }
            catch
            {
                await transaction.RollbackAsync();
                return StatusCode(500, "Transaction failed, rolled back.");
            }
        }

        // ============================================================
        // 1️⃣1️⃣ RELOAD ENTITY FROM DATABASE (ReloadAsync)
        // ============================================================
        [HttpGet("reload/{id}")]
        public async Task<IActionResult> ReloadUser(int id)
        {
            var user = await _context.Users.FindAsync(id);
            if (user == null)
                return NotFound();

            await _context.Entry(user).ReloadAsync();
            return Ok(user);
        }
    }
}
```

### 🧾 Summary of Async Methods Used

| Operation        | Method Used                                                   |
| ---------------- | ------------------------------------------------------------- |
| Get all users    | `ToListAsync()`                                               |
| Get by ID        | `FindAsync()`                                                 |
| Add user         | `AddAsync()`, `SaveChangesAsync()`                            |
| Update user      | `SaveChangesAsync()`                                          |
| Delete user      | `SaveChangesAsync()`                                          |
| Count users      | `CountAsync()`                                                |
| Check existence  | `AnyAsync()`                                                  |
| Raw SQL          | `FromSqlRaw()`                                                |
| Stored Procedure | `FromSqlInterpolated()`                                       |
| Transaction      | `BeginTransactionAsync()`, `CommitAsync()`, `RollbackAsync()` |
| Reload Entity    | `ReloadAsync()`                                               |
