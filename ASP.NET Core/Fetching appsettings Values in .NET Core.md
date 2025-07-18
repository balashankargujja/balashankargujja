# Fetching appsettings.json Values in .NET Core

Consider the following appsettings.json file:
```json
{
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  },
  "MyKey": "My appsettings.json Value",
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```
Accessing the above values in a class:
```cs
public class CustomerService
{
private readonly IConfiguration _configuration;

public CustomerService(IConfiguration configuration)
{
_configuration = configuration;
}

public void GetData()
{
var myKeyValue = _configuration["MyKey"];
var title = _configuration["Position:Title"];
var name = _configuration["Position:Name"];
var defaultLogLevel = _configuration["Logging:LogLevel:Default"];
// You can now use these values as needed
}
}

```

Here's how to bind a section from appsettings.json to a strongly-typed class in ASP.NET Core
#### Example: Binding Position section to a class
#### Step 1: `appsettings.json`
```json
{
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
}
```
#### Step 2: Create a POCO class for `Position`
```cs
public class PositionOptions
{
    public string Title { get; set; }
    public string Name { get; set; }
}

```
#### Step 3: Register the configuration binding in `Program.cs`
```cs
builder.Services.Configure<PositionOptions>(
    builder.Configuration.GetSection("Position"));
```
#### Step 4: Inject and use `IOptions<PositionOptions>`
```cs
using Microsoft.Extensions.Options;

public class CustomerService
{
    private readonly PositionOptions _position;

    public CustomerService(IOptions<PositionOptions> options)
    {
        _position = options.Value;
    }

    public void GetData()
    {
        var title = _position.Title;
        var name = _position.Name;
        Console.WriteLine(title);
    }
}

```
