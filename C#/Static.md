
# Static Keyword

The `static` keyword is used to declare members (fields, methods, properties, constructors) or types (classes) that belong to the type itself rather than to a specific object.

#### 1. Static Fields
A static field is shared among all instances of a class.
```c#
class Counter
{
    public static int count = 0;

    public Counter()
    {
        count++;
    }
}
```
- All instances of Counter share the same count.
- Changing count in one instance affects all others.

#### 2. Static Methods
Static methods can be called on the class itself, without creating an instance.

```c#
class MathHelper
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}

int result = MathHelper.Add(5, 10); // No object needed

```
- Static methods cannot access instance members directly.
- They can only access other static members.

#### 3. Static Properties
Similar to fields, but with getter/setter control:
```c#
class Configuration
{
    private static string _appName = "MyApp";

    public static string AppName
    {
        get { return _appName; }
        set { _appName = value; }
    }
}
```
Usage :
```c#
Console.WriteLine(Configuration.AppName);
Configuration.AppName = "NewName";
```

#### 4. Static Class
A class that can only contain static members.
```c#
static class Utility
{
    public static void Print(string msg)
    {
        Console.WriteLine(msg);
    }
}
```
Usage :
```c#
Utility.Print("Hello");
```
Notes :
- Cannot be instantiated.
- Cannot contain instance members.
- Cannot be inherited.

#### 5. Static Method in Non-Static Class
```c#
public class AppInfo
{
    public static string GetAppName()
    {
        return "MyDotNetApp";
    }

    public string GetVersion()
    {
        return "1.0";
    }
}
```
- `GetAppName` is static — call it like `AppInfo.GetAppName()`
- `GetVersion` is instance-based — requires `new AppInfo().GetVersion()`

#### 6. Static Local Functions
```c#
public class Calculator
{
    public int SquareThenAddTen(int x)
    {
        static int Square(int n) => n * n;

        return Square(x) + 10;
    }
}
```
- The `static` on local function ensures it does not capture variables from outside.
- Improves performance and avoids memory leaks.

#### 7. Initializing Static Fields
You can initialize:
- Inline
- Or using a static constructor
```c#
public class AppConfig
{
    public static string AppName;
    public static readonly DateTime StartTime;

    static AppConfig()
    {
        AppName = "MyApp";
        StartTime = DateTime.Now;
    }
}
```
