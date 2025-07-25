## What is an Abstract Class?
An abstract class in C# is a class that cannot be instantiated directly. It is meant to be inherited by other classes. It can contain:
- Abstract methods (methods without a body)
- Non-abstract methods (methods with a body)
- Fields, properties, constructors, etc.
```c#
public abstract class Animal
{
    // Abstract method (no implementation)
    public abstract void MakeSound();

    // Normal method
    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
}
```
Derived Class Example
```c#
public class Dog : Animal
{
    // Must override the abstract method
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```
Usage
```c#
Animal myDog = new Dog();
myDog.MakeSound();  // Output: Woof!
myDog.Sleep();      // Output: Sleeping...
```
Cannot Do
Usage
```c#
Animal a = new Animal(); // ❌ Error: Cannot create an instance of the abstract class
```

#### Benefits of Abstract Classes
| Benefit                  | Description                                         |
| ------------------------ | --------------------------------------------------- |
| ✅ Code Reuse             | Define shared methods in the base class             |
| ✅ Enforce Rules          | Force derived classes to implement specific methods |
| ✅ Strong OOP Design      | Clear contract between base and derived classes     |
| ✅ Partial Implementation | Some behavior defined, some left for subclasses     |

💼 Use Case: Online Payment System

Imagine you're building an e-commerce application. You want to support multiple payment methods: credit card, PayPal, UPI, etc.

All payment methods must have:
- Pay() method (must be implemented)
- Validate() method (can have a common implementation)
✅ Abstract Class: Payment
```c#
public abstract class Payment
{
    public string TransactionId { get; set; }

    public Payment()
    {
        TransactionId = Guid.NewGuid().ToString();
    }

    // Abstract method — must be implemented by child classes
    public abstract void Pay(decimal amount);

    // Normal method — shared by all
    public void Validate()
    {
        Console.WriteLine($"Validating transaction: {TransactionId}");
    }
}
```
💳 Derived Class 1: CreditCardPayment
```c#
public class CreditCardPayment : Payment
{
    public override void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ₹{amount} using Credit Card.");
    }
}
```
💸 Derived Class 2: UPIPayment
```c#
public class UPIPayment : Payment
{
    public override void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ₹{amount} using UPI.");
    }
}
```
🚀 Usage
```c#
class Program
{
    static void Main()
    {
        Payment payment;

        // Using Credit Card
        payment = new CreditCardPayment();
        payment.Validate();         // Common logic
        payment.Pay(999.99m);       // Custom logic

        // Using UPI
        payment = new UPIPayment();
        payment.Validate();
        payment.Pay(249.50m);
    }
}
```
🧠 Summary

- You defined common structure (Validate) and forced method (Pay) in Payment.
- Each derived class provides its own implementation of Pay.
- Easy to extend later: just add NetBankingPayment, CryptoPayment, etc.

✅ Prefer Interface When:
| Situation                                                    | Reason                                                                                          |
| ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------- |
| 🔁 You need **multiple inheritance**                         | C# allows a class to **implement multiple interfaces** but inherit only **one abstract class**. |
| 🔐 You only want to define **contracts** (method signatures) | Interfaces don't provide implementation (before C# 8.0), so they work as **pure contracts**.    |
| 🧪 You want **loose coupling** and better **testability**    | Interfaces make it easier to **mock dependencies** in unit testing (e.g., `IMailService`).      |
| 🔄 You want to change implementations **at runtime**         | Interfaces are ideal for strategies or plug-and-play designs.                                   |
| 📦 You're building **plugin or extensible architecture**     | Interfaces define clear contracts without inheritance constraints.                              |


🛑 Prefer Abstract Class When:
| Situation                                                   | Reason                                                                               |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 🔗 You need to provide **default/shared logic**             | Abstract classes can have implemented methods and fields.                            |
| 📐 Classes are **closely related** by concept               | e.g., `Animal`, `Vehicle`, `Shape`, etc.                                             |
| 📦 You want to **define base state (fields, properties)**   | Interfaces can't have fields or constructors.                                        |
| ✅ You want **partial implementation** with enforced methods | Abstract class lets you define some methods and force others via `abstract` keyword. |
