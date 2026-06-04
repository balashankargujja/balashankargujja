# ASP.NET Core Web API Coding Style Prompt

## General Coding Style
* Write code beginner-friendly (prefer code that is easy to read and understand, even if it is slightly longer)
* Write clean, professional, production-ready code.
* Keep code simple and easy to understand.
* Use step-by-step implementation (prefer every operation to be broken into logical steps)
* Add explanatory comments (comments explaining what each step is doing)
* Use Allman braces style (Opening and Closing Braces on Separate Lines)
* Use descriptive variable names (prefer descriptive names instead of short names)
* Separate Complex Expressions (prefer intermediate variables rather than chaining everything together)
* Focus on readability and maintainability.
* Use enterprise-level coding standards.
* Avoid unnecessary complexity.
* Write scalable and reusable code.
* Use proper naming conventions everywhere.
* Use async/await properly for database and API operations.
* Follow SOLID principles where applicable.
* Prefer modular and reusable architecture.
* Left-aligned method signatures and bodies (no extra indentation)
* #region blocks with short descriptions (e.g. “Check collection has items”, “Page query results”)

---

## Condition Writing Style

### if / else Conditions

* Prefer guard clauses and early returns.
* Avoid deep nested if conditions.
* Keep conditions flat and readable.
* One validation per condition block when possible.
* Use meaningful boolean variable names.
* Avoid long inline conditions.
* Extract repeated validations into helper methods or extensions.
* Keep validation logic clean and separated.

Example Preferred Style:

```csharp
if (request == null)
{
    return BadRequest("Request is required.");
}

if (request.UserId <= 0)
{
    return BadRequest("Invalid UserId.");
}
```

Avoid:

```csharp
if (request != null)
{
    if (request.UserId > 0)
    {
    }
}
```

---

## Boolean Conditions

* Use readable boolean variables.
* Make conditions self-explanatory.

Preferred:

```csharp
bool isExpired = campaign.EndDate < DateTime.UtcNow;
bool isDeleted = campaign.IsDeleted;

if (isExpired || isDeleted)
{
    return BadRequest();
}
```

Avoid giant inline conditions.

---

## Null Checks

Preferred:

```csharp
if (string.IsNullOrWhiteSpace(request.Name))
{
    return BadRequest("Name is required.");
}
```

Avoid:

```csharp
if (request.Name == null || request.Name == "")
```

---

## switch Statements

* Prefer modern switch expressions where suitable.
* Use switch statements for larger business logic.
* Keep cases clean and separated.
* Always handle default cases.

Preferred:

```csharp
string status = type switch
{
    1 => "Pending",
    2 => "Approved",
    _ => "Unknown"
};
```

---

## Return Statement Style

* Avoid writing complex logic directly inside return statements.
* Store expressions/results in meaningful variables first.
* Return the variable instead of inline logic.
* Improves readability, debugging, maintainability, and future extensibility.

Preferred:

```csharp id="eozcm2"
bool isUserValid = user != null && user.IsActive;

return isUserValid;
```

Preferred:

```csharp id="s7eq1z"
ApiResponse response = ApiResponse.Success(data);

return response;
```

Avoid:

```csharp id="p2e4na"
return user != null && user.IsActive;
```

Avoid:

```csharp id="77m5fy"
return ApiResponse.Success(data);
```

Reason:

* Easier to debug
* Easier to place breakpoints
* Easier to understand
* Easier to extend later
* Improves enterprise-level readability

---

## Method Naming Conventions

### General Rules

* Method names should clearly describe the purpose.
* Use meaningful and readable names.
* Avoid short or unclear names.
* Method names should follow PascalCase.
* Keep naming consistent across the project.
* Prefer action-based naming.

---

## Async Method Naming

* Always suffix asynchronous methods with `Async`.

Preferred:

```csharp id="1f4c9m"
GetUserByIdAsync()
CreateCampaignAsync()
UpdateTemplateAsync()
DeleteCouponAsync()
```

Avoid:

```csharp id="r8b7x1"
GetUserById()
Create()
UpdateData()
DoWork()
```

---

## Boolean Method Naming

* Boolean methods should sound like questions or validations.

Preferred:

```csharp id="94eb9j"
IsUserActive()
HasValidSubscription()
CanEditTemplate()
IsCampaignExpired()
```

Avoid:

```csharp id="4mp3gk"
CheckUser()
Validate()
Status()
```

---

## Repository Method Naming

* Repository methods should clearly indicate database operations.

Preferred:

```csharp id="d7q3l2"
GetByIdAsync()
GetAllAsync()
CreateAsync()
UpdateAsync()
DeleteAsync()
ExistsAsync()
```

---

## Service Method Naming

* Service methods should reflect business operations.

Preferred:

```csharp id="v6x1tu"
CreateCampaignAsync()
GenerateCouponAsync()
PublishTemplateAsync()
ProcessPaymentAsync()
```

---

## Avoid Generic Method Names

Avoid names like:

```csharp id="e5a2nm"
Handle()
Process()
Execute()
Manage()
DoStuff()
```

Unless the context is extremely clear.

---

## Method Naming Readability Rule

* Another developer should understand what the method does just by reading its name.
* Method names should reduce the need for additional comments.

Good Example:

```csharp id="t5j0ls"
GetActiveCampaignsByTenantIdAsync()
```

Bad Example:

```csharp id="h1y8pw"
GetDataAsync()
```

---

## Single Responsibility Naming

* One method should represent one clear responsibility.
* Method name should align with exactly one business action.

Preferred:

```csharp id="9mz2ac"
ValidateCouponExpiry()
```

Avoid:

```csharp id="k2v4nb"
ValidateAndCreateAndSaveCoupon()
```

---

## Controller Action Naming

* Controller methods should clearly represent API intent.

Preferred:

```csharp id="7z0mqp"
GetTemplatesAsync()
CreateTemplateAsync()
UpdateCampaignStatusAsync()
```

---

## Naming Mindset

* Clarity over short names
* Explicit over vague
* Business-readable naming
* Enterprise-level readability
* Self-documenting method names

---

## DbContext Coding Standards

### General DbContext Style

* Keep DbContext class clean and lightweight.
* Only include database-related configurations.
* Avoid business logic inside DbContext.
* Organize DbSets properly.
* Use meaningful DbSet names.
* Keep configuration maintainable and scalable.

---

## DbSet Naming Conventions

* Use plural entity names for DbSet properties.
* Follow PascalCase naming.

Preferred:

```csharp id="b0r6yx"
public DbSet<User> Users { get; set; }

public DbSet<Campaign> Campaigns { get; set; }

public DbSet<Template> Templates { get; set; }
```

Avoid:

```csharp id="i9c1wa"
public DbSet<User> User { get; set; }

public DbSet<Campaign> campaignTbl { get; set; }
```

---

## DbContext Class Naming

* DbContext class name should clearly represent the application/domain.

Preferred:

```csharp id="h3l9ka"
ApplicationDbContext
CampaignDbContext
TemplateDbContext
```

Avoid:

```csharp id="c8q2yt"
MainContext
MyDb
DatabaseContext1
```

---

## Constructor Style

Preferred:

```csharp id="f6w3qm"
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

* Keep constructor simple.
* Avoid additional logic in constructor.

---

## Fluent API Configuration Style

* Move entity configurations into separate configuration classes.
* Avoid large OnModelCreating methods.
* Use IEntityTypeConfiguration<T> for clean structure.

Preferred:

```csharp id="x5m2eq"
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfigurationsFromAssembly(typeof(ApplicationDbContext).Assembly);

    base.OnModelCreating(modelBuilder);
}
```

Avoid huge configuration blocks directly inside DbContext.

---

## Entity Configuration Organization

Preferred Folder Structure:

```text id="91q7cf"
Configurations/
    UserConfiguration.cs
    CampaignConfiguration.cs
    TemplateConfiguration.cs
```

---

## Table Naming Style

* Use consistent table naming conventions.
* Prefer singular or plural consistently across the project.
* Avoid mixed naming styles.

Preferred:

```text id="z2p4md"
Users
Campaigns
Templates
```

Avoid:

```text id="l7k0sa"
tbl_User
campaign_master
TemplateTbl
```

---

## Connection String Handling

* Never hardcode connection strings inside DbContext.
* Use configuration files and dependency injection.

Preferred:

```csharp id="e4n1tx"
builder.Services.AddDbContext<ApplicationDbContext>(options =>
{
    options.UseSqlServer(configuration.GetConnectionString("DefaultConnection"));
});
```

---

## Tracking Behavior

* Use AsNoTracking() for read-only queries when updates are not needed.
* Improve performance for read operations.

Preferred:

```csharp id="t9v5wu"
List<User> users = await _dbContext.Users
    .AsNoTracking()
    .ToListAsync();
```

---

## SaveChanges Handling

* Keep SaveChangesAsync centralized when possible.
* Avoid calling SaveChangesAsync repeatedly in loops.

Preferred:

```csharp id="m6q3re"
await _dbContext.SaveChangesAsync();
```

after all operations are completed.

---

## Audit Fields Preference

* Prefer common audit fields in entities.

Example:

```csharp id="u8k1yl"
CreatedAt
CreatedBy
ModifiedAt
ModifiedBy
IsDeleted
```

---

## Soft Delete Preference

* Prefer soft delete for important business data.

Preferred:

```csharp id="g3r7ph"
entity.IsDeleted = true;
```

instead of permanent deletion where appropriate.

---

## Migration Style

* Keep migrations clean and meaningful.
* Use proper migration names.

Preferred:

```text id="n2c5vb"
AddCampaignTable
UpdateTemplateColumns
AddCouponIndexes
```

Avoid:

```text id="w4x8je"
Migration1
TestMigration
TempFix
```

---

## DbContext Mindset

* DbContext should manage database interaction only.
* Keep business logic outside DbContext.
* Maintain clean separation of concerns.
* Optimize for readability and scalability.
* Enterprise-level maintainability is the goal.

---

### Architecture Preferences

* Use DTOs properly.
* Use Repository Pattern when applicable.
* Use Dependency Injection correctly.
* Separate concerns properly.
* Use Extension Methods for reusable logic.
* Keep Controllers lightweight.
* Move business logic into Services.
* Keep database logic inside repositories. (Review)
* Prefer reusable utility/helper classes.

---

### Code Readability Rules

* Proper indentation and spacing.
* Avoid giant methods.
* Keep methods focused on one responsibility.
* Use meaningful method and variable names.
* Avoid magic strings and magic numbers.
* Prefer enums/constants where appropriate.
* Write code another developer can easily scan.

---

### Validation Style

* Validate early.
* Fail fast.
* Return meaningful error messages.
* Keep validation reusable.
* Use FluentValidation or custom validation extensions when needed.

---

### API Design Preferences

* Use RESTful naming conventions.
* Return proper HTTP status codes.
* Use consistent API response structure.
* Use pagination for large datasets.
* Prefer clean request/response contracts.

---

### Preferred Coding Mindset

* Readability over cleverness.
* Maintainability over shortcuts.
* Reusability over duplication.
* Simplicity over over-engineering.
* Production-ready code over demo-style code.
