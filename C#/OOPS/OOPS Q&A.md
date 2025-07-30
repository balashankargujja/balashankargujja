
# OOPS Q&A


#### 1. What are the four pillars of OOP?
The four pillars of Object-Oriented Programming (OOP) are:

- Encapsulation: Hiding internal data and exposing only necessary functionality using access modifiers.
- Abstraction: Showing only essential features and hiding the complex implementation.
- Inheritance: One class can inherit members from another class, promoting code reuse.
- Polymorphism: The ability of different classes to be treated as instances of the same class through method overriding or overloading.

#### 2. What is the difference between encapsulation and abstraction?
| Feature      | Encapsulation                            | Abstraction                           |
| ------------ | ---------------------------------------- | ------------------------------------- |
| Purpose      | Hides the internal state                 | Hides the complexity                  |
| Achieved via | Access Modifiers (private, public, etc.) | Abstract classes or interfaces        |
| Focus        | Data hiding                              | Functionality hiding                  |
| Example      | Private fields with public properties    | Interface with only method signatures |

#### 3. Can you inherit multiple classes in C#? Why not?
No, C# does not support multiple inheritance of classes to avoid ambiguity problems (like the Diamond Problem). However, a class can implement multiple interfaces, which allows similar flexibility without ambiguity.

#### 4. What is the difference between an abstract class and an interface?
| Feature          | Abstract Class                           | Interface                                                             |
| ---------------- | ---------------------------------------- | --------------------------------------------------------------------- |
| Members          | Can have fields, methods, constructors   | Cannot have fields or constructors (only properties, methods, events) |
| Access Modifiers | Members can have any access level        | All members are public by default                                     |
| Inheritance      | Only one abstract class can be inherited | Multiple interfaces can be implemented                                |
| Use Case         | When you need common base implementation | When you only need to define a contract                               |

#### 5. What is method overloading vs method overriding?
#### Method Overloading (Compile-time Polymorphism):
- Same method name with different parameters.
- Happens in the same class.
```c#
void Add(int a, int b) { }
void Add(double a, double b) { }
```
#### Method Overriding (Run-time Polymorphism):
- Base class method marked as virtual and overridden in derived class using override.
- Enables dynamic dispatch.
```c#
public virtual void Speak() => Console.WriteLine("Animal");
public override void Speak() => Console.WriteLine("Dog");
```

#### 6. Can an abstract class have a constructor in C#?
Yes, abstract classes can have constructors. They are used to initialize fields or execute code when an object of a derived class is created.
```c#
public abstract class Person
{
    public Person() => Console.WriteLine("Person constructor");
}
```

#### 7. What happens if a method in the base class is not marked virtual but redefined in derived class?
It results in method hiding, not overriding. The base class method is not polymorphic unless marked virtual.
```c#
public class Base { public void Show() => Console.WriteLine("Base"); }
public class Derived : Base { public new void Show() => Console.WriteLine("Derived"); }
```
Calling `Show()` on a `Base` reference will always invoke the base version.

#### 8. What are access modifiers? How do they relate to encapsulation?
Access modifiers control visibility of class members:

- `public`: Accessible from anywhere.
- `private`: Accessible only within the class.
- `protected`: Accessible within the class and derived classes.
- `internal`: Accessible within the same assembly.
- `protected internal`: Accessible within same assembly or derived classes.
They enforce encapsulation by hiding sensitive data and exposing only what’s needed.

#### 9. How is the sealed keyword used in inheritance?
- A sealed class cannot be inherited.
- A sealed method cannot be overridden further.
```c#
public sealed class FinalClass { }
public class A { public virtual void DoWork() { } }
public class B : A { public sealed override void DoWork() { } }
```
Useful to prevent further extension or modification, especially for security or performance reasons.

#### 10. What is the role of `base` keyword in C#?
Used to access:
- Base class constructors
- Base class methods or properties overridden in derived class
```c#
public class Parent
{
    public virtual void Show() => Console.WriteLine("Parent");
}

public class Child : Parent
{
    public override void Show()
    {
        base.Show();  // Calls base class method
        Console.WriteLine("Child");
    }
}
```

#### 11. Can interfaces have default implementations in C#?
Yes, starting with C# 8.0, interfaces can have default method implementations.
```c#
public interface ILogger
{
    void Log(string message) => Console.WriteLine($"Log: {message}");
}
```
This makes interfaces more flexible, similar to abstract classes.

#### 12. What's the difference between override and new keywords?
| Keyword    | Purpose                             | Behavior                    |
| ---------- | ----------------------------------- | --------------------------- |
| `override` | Overrides base class virtual method | Enables **polymorphism**    |
| `new`      | Hides base class method             | **Hiding**, no polymorphism |

#### 13. What is the difference between abstract class and sealed class?
- abstract class cannot be instantiated but must be inherited.
- sealed class cannot be inherited further.

#### 14. Can a class be both abstract and sealed?
Answer:
No — it’s a contradiction.
- `abstract` = must be inherited.
- `sealed` = cannot be inherited.
