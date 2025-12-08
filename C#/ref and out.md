
# ref and out

✅ Difference Between `ref` and `out` in C#

Both `ref` and `out` are parameter modifiers used to pass arguments by reference, but they have different rules and use cases.

#### 🔵 1. Initialization Requirement
#### ref
- The variable must be initialized before passing to the method.
```c#
int a = 10;
Method(ref a);   // OK
```
#### out
- The variable does not need to be initialized before passing.
```c#
int a;
Method(out a);   // OK
```

#### 🔵 2. Method Requirement
#### ref
- The method does not need to assign a new value.
```c#
void Method(ref int x)
{
    x += 5;   // not mandatory
}
```
#### out
- The method must assign a value before returning.
```c#
void Method(out int x)
{
    x = 100;  // mandatory
}
```

#### 🔵 3. Purpose / Use Cases
#### ref
Used when:
- You want to read the existing value
- And also modify it
Example: update an existing value.
#### out
Used when:
- Method needs to return multiple values
- Caller only wants the output value, not input
Common example: `int.TryParse()` uses out.

#### 🔵 4. Data Flow
| Modifier | Input          | Output |
| -------- | -------------- | ------ |
| **ref**  | ✔️ Yes         | ✔️ Yes |
| **out**  | ❌ No (ignored) | ✔️ Yes |

#### 🔵 5. Example Code
Example with ref
```c#
void Add(ref int value)
{
    value += 10;
}

int num = 5;
Add(ref num);
Console.WriteLine(num); // Output: 15
```

#### Example with out
```c#
void GetValues(out int x, out int y)
{
    x = 10;
    y = 20;
}

int a, b;
GetValues(out a, out b);
Console.WriteLine(a + ", " + b); // Output: 10, 20
```

#### 🎯 Summary (One-Line Answer for Interviews)

- `ref` passes a value by reference that must be initialized before use and may or may not be modified.
- `out` also passes by reference but must be assigned inside the method and is used for returning multiple values.
