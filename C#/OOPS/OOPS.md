
# OOPS

#### 1. Encapsulation
Wrapping data and methods that operate on data into a single unit (class) and restricting direct access to some of the object's components.

Example:
```c#
public class Person
{
    private string name; // Encapsulated field

    public string Name
    {
        get { return name; }
        set { name = value; }
    }
}
```
Benefit: Protects internal state; promotes data integrity.

#### 2. Abstraction
Definition: Hiding complex implementation details and showing only the essential features.

```c#
public abstract class Animal
{
    public abstract void MakeSound();
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Bark");
    }
}
```
Benefit: Reduces complexity; focuses on what an object does instead of how it does it.

#### 3. Inheritance
Allows a class (child/derived) to inherit properties and methods from another class (parent/base).
```c#
public class Vehicle
{
    public void Start() => Console.WriteLine("Vehicle started");
}

public class Car : Vehicle
{
    public void Drive() => Console.WriteLine("Car is driving");
}
```
Benefit: Code reuse and hierarchical relationships.

#### 4. Polymorphism
Allows one interface to be used for different underlying forms (types).

a. Compile-Time Polymorphism (Method Overloading)
```c#
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
}
```
b. Run-Time Polymorphism (Method Overriding)
```c#
public class Animal
{
    public virtual void MakeSound() => Console.WriteLine("Some sound");
}

public class Cat : Animal
{
    public override void MakeSound() => Console.WriteLine("Meow");
}
```
Benefit: Flexibility and ability to handle different types through a common interface.

#### Bonus: Interface (Pure Abstraction)
```c#
public interface IAnimal
{
    void Speak();
}

public class Dog : IAnimal
{
    public void Speak() => Console.WriteLine("Bark");
}
```
