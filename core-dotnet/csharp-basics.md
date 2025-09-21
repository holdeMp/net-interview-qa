# C# Basics Interview Questions & Answers 💻

> Comprehensive collection of C# fundamentals interview questions and answers for .NET developers. Covering language features, OOP concepts, and core programming principles.

## 📋 Table of Contents

- [Language Fundamentals](#language-fundamentals)
- [Object-Oriented Programming](#object-oriented-programming)
- [Delegates and Events](#delegates-and-events)
- [Memory Management](#memory-management)
- [Exception Handling](#exception-handling)
- [Collections and LINQ](#collections-and-linq)

---

## 🎯 Language Fundamentals

### Q1: What is the difference between `var` and `dynamic` in C#?

**Answer:**
`var` and `dynamic` are both used for type inference, but they work very differently:

**`var` (Implicitly Typed Local Variables):**

- Type is determined at **compile time**
- Must be initialized at declaration
- Type cannot be changed after declaration
- Provides IntelliSense and compile-time type checking

```csharp
var name = "John";        // Compiler infers string
var age = 25;             // Compiler infers int
var numbers = new int[] {1, 2, 3}; // Compiler infers int[]

// This won't compile:
// var uninitialized; // Error: must be initialized
// name = 123;        // Error: cannot change type
```

**`dynamic` (Dynamic Type):**

- Type is determined at **runtime**
- Can be assigned any type
- No compile-time type checking
- Resolves method calls at runtime

```csharp
dynamic value = "Hello";
value = 42;              // OK - can change type
value = new List<int>(); // OK - can change type

// Runtime resolution:
value.SomeMethod();      // Resolved at runtime
```

### Q2: Explain the difference between value types and reference types.

**Answer:**

| Aspect                | Value Types      | Reference Types      |
| --------------------- | ---------------- | -------------------- |
| **Storage**           | Stack memory     | Heap memory          |
| **Default Value**     | Zero-initialized | `null`               |
| **Assignment**        | Copy of value    | Copy of reference    |
| **Memory Management** | Automatic        | Garbage collected    |
| **Performance**       | Faster access    | Slower (indirection) |

**Value Types:**

```csharp
int a = 10;
int b = a;  // b gets a copy of a's value
a = 20;     // b is still 10

// Built-in value types
int number = 42;
bool flag = true;
char letter = 'A';
DateTime date = DateTime.Now;

// Custom value type
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
}
```

**Reference Types:**

```csharp
List<int> list1 = new List<int> {1, 2, 3};
List<int> list2 = list1;  // list2 references same object
list1.Add(4);             // list2 also sees the change

// Built-in reference types
string text = "Hello";
object obj = new object();
int[] array = new int[5];

// Custom reference type
class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

---

## 🏗️ Object-Oriented Programming

### Q3: What are the four pillars of OOP in C#?

**Answer:**

**1. Encapsulation:**

- Bundling data and methods together
- Controlling access through access modifiers
- Data hiding and abstraction

```csharp
public class BankAccount
{
    private decimal balance;  // Private field - encapsulated

    public decimal GetBalance()  // Public method to access
    {
        return balance;
    }

    public void Deposit(decimal amount)
    {
        if (amount > 0)
            balance += amount;
    }
}
```

**2. Inheritance:**

- Creating new classes based on existing ones
- Code reuse and "is-a" relationships

```csharp
public class Vehicle
{
    public string Brand { get; set; }
    public int Year { get; set; }

    public virtual void Start()
    {
        Console.WriteLine("Vehicle started");
    }
}

public class Car : Vehicle
{
    public int Doors { get; set; }

    public override void Start()
    {
        Console.WriteLine("Car engine started");
    }
}
```

**3. Polymorphism:**

- Same interface, different implementations
- Method overriding and overloading

```csharp
// Method Overloading
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
    public int Add(int a, int b, int c) => a + b + c;
}

// Method Overriding
Vehicle vehicle = new Car();
vehicle.Start(); // Calls Car.Start() due to polymorphism
```

**4. Abstraction:**

- Hiding complex implementation details
- Abstract classes and interfaces

```csharp
public abstract class Shape
{
    public abstract double GetArea();
    public virtual void Draw() => Console.WriteLine("Drawing shape");
}

public interface IDrawable
{
    void Draw();
    void Erase();
}
```

---

## 🎯 Delegates and Events

### Q4: What is the difference between delegates and lambdas?

**Answer:**
Delegates and lambdas are actually two very different things. "Delegate" is actually the name for a variable that holds a reference to a method or a lambda, and a lambda is a method without a permanent name.

**Key Differences:**

| Aspect               | Delegates                                   | Lambdas                                     |
| -------------------- | ------------------------------------------- | ------------------------------------------- |
| **Definition**       | Variable that holds method reference        | Method without permanent name               |
| **Declaration**      | Statement with permanent name               | Expression defined "on the fly"             |
| **Expression Trees** | Cannot be used                              | Some can be used with .NET expression trees |
| **Flexibility**      | Can hold any method with matching signature | Inline method definition                    |

**Delegate Definition:**

```csharp
delegate Int32 BinaryIntOp(Int32 x, Int32 y);

// A variable of type BinaryIntOp can have either a method or a lambda assigned to it
BinaryIntOp operation = AddNumbers;  // Method reference
BinaryIntOp lambda = (a, b) => a + b;  // Lambda expression
```

**Lambda Examples:**

```csharp
// Lambda assigned to delegate
BinaryIntOp sumOfSquares = (a, b) => a*a + b*b;

// Using with LINQ
var numbers = new[] {1, 2, 3, 4, 5};
var squares = numbers.Select(x => x * x);

// Using with Action and Func
Action<string> printAction = message => Console.WriteLine(message);
Func<int, int, int> multiply = (x, y) => x * y;
```

**Func and Action Types:**

```csharp
// Func and Action are generic delegate types
// They define names for any delegate type you might need (up to 4 parameters)

// Func<TResult> - takes no parameters, returns TResult
// Func<T, TResult> - takes one parameter, returns TResult
// Func<T1, T2, TResult> - takes two parameters, returns TResult
// Action<T> - takes one parameter, returns void
// Action<T1, T2> - takes two parameters, returns void

// Example with method reference
Int32 DiffOfSquares(Int32 x, Int32 y)
{
    return x*x - y*y;
}

Func<Int32, Int32, Int32> funcPtr = DiffOfSquares;

// Example with lambda
Func<int, int, int> add = (x, y) => x + y;
Action<string> log = message => Console.WriteLine($"Log: {message}");
```

**Important Notes:**

- Delegate types with the same signature but different names will not be implicitly cast to each other
- This includes Func and Action delegates
- If signatures are identical, you can explicitly cast between them
- C# does not have "first-class functions" - functions are not objects themselves
- You cannot do member access on function names (e.g., `MyFunction.SomeMethod()`)

**Practical Example:**

```csharp
public class Calculator
{
    // Delegate type
    public delegate int MathOperation(int x, int y);

    // Method that takes delegate
    public int Calculate(int x, int y, MathOperation operation)
    {
        return operation(x, y);
    }
}

// Usage
var calc = new Calculator();

// Using method reference
int result1 = calc.Calculate(5, 3, Add);

// Using lambda
int result2 = calc.Calculate(5, 3, (x, y) => x * y);

// Using Func
Func<int, int, int> multiply = (x, y) => x * y;
int result3 = calc.Calculate(5, 3, multiply.Invoke);

// Helper method
static int Add(int x, int y) => x + y;
```

### Q5: What are events and how do they work with delegates?

**Answer:**
Events are a special type of delegate that provides encapsulation and controlled access. They follow the publisher-subscriber pattern.

**Key Characteristics:**

- Events can only be invoked from within the class that declares them
- External classes can only subscribe (+=) or unsubscribe (-=) from events
- Events are typically used for notifications and callbacks

```csharp
public class Button
{
    // Event declaration
    public event EventHandler<ButtonClickEventArgs> Clicked;

    // Method to trigger the event
    protected virtual void OnClicked(ButtonClickEventArgs e)
    {
        Clicked?.Invoke(this, e);
    }

    public void Click()
    {
        OnClicked(new ButtonClickEventArgs { Timestamp = DateTime.Now });
    }
}

public class ButtonClickEventArgs : EventArgs
{
    public DateTime Timestamp { get; set; }
}

// Usage
var button = new Button();

// Subscribe to event
button.Clicked += (sender, e) =>
{
    Console.WriteLine($"Button clicked at {e.Timestamp}");
};

// Trigger event
button.Click();
```

---

## 🧠 Memory Management

### Q6: Explain garbage collection in C#.

**Answer:**
Garbage collection is the automatic memory management system in .NET that handles allocation and deallocation of objects on the heap.

**How it Works:**

1. **Allocation**: Objects are allocated on the managed heap
2. **Marking**: GC identifies reachable objects by following references from roots
3. **Collection**: Unreachable objects are collected and memory is freed
4. **Compaction**: Surviving objects are moved to eliminate fragmentation

**Generations:**

- **Generation 0**: New objects (short-lived)
- **Generation 1**: Objects that survived one GC cycle
- **Generation 2**: Long-lived objects

```csharp
// Force garbage collection (generally not recommended)
GC.Collect();
GC.WaitForPendingFinalizers();
GC.Collect();

// Get memory information
long memoryBefore = GC.GetTotalMemory(false);
// ... do work ...
long memoryAfter = GC.GetTotalMemory(true);
Console.WriteLine($"Memory used: {memoryAfter - memoryBefore} bytes");
```

**IDisposable Pattern:**

```csharp
public class ResourceManager : IDisposable
{
    private bool disposed = false;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!disposed)
        {
            if (disposing)
            {
                // Dispose managed resources
            }
            // Dispose unmanaged resources
            disposed = true;
        }
    }

    ~ResourceManager()
    {
        Dispose(false);
    }
}

// Usage with using statement
using (var resource = new ResourceManager())
{
    // Use resource
} // Automatically calls Dispose()
```

---

## ⚠️ Exception Handling

### Q7: What is the difference between `throw` and `throw ex`?

**Answer:**
This is a crucial difference that affects stack trace preservation:

**`throw` (Recommended):**

- Preserves the original stack trace
- Maintains the call stack from where the exception originally occurred
- Better for debugging

```csharp
try
{
    // Some operation that might throw
    ProcessData();
}
catch (Exception ex)
{
    LogError(ex);
    throw; // Preserves original stack trace
}
```

**`throw ex` (Not Recommended):**

- Resets the stack trace
- Loses information about where the exception originally occurred
- Makes debugging more difficult

```csharp
try
{
    ProcessData();
}
catch (Exception ex)
{
    LogError(ex);
    throw ex; // Resets stack trace - DON'T DO THIS
}
```

**Best Practices:**

```csharp
// 1. Catch specific exceptions when possible
try
{
    int.Parse(userInput);
}
catch (FormatException ex)
{
    // Handle specific exception
}

// 2. Use when clause for conditional catching
try
{
    ProcessFile(fileName);
}
catch (FileNotFoundException ex) when (ex.FileName.Contains("temp"))
{
    // Only catch if it's a temp file
}

// 3. Always log before re-throwing
try
{
    ProcessData();
}
catch (Exception ex)
{
    logger.LogError(ex, "Error processing data");
    throw; // Re-throw with original stack trace
}
```

---

## 📚 Collections and LINQ

### Q8: What are the main collection types in C# and when to use each?

**Answer:**

**List<T> - Dynamic Array:**

```csharp
var numbers = new List<int> {1, 2, 3, 4, 5};
numbers.Add(6);
numbers.Remove(3);
// Good for: Dynamic sizing, random access, frequent additions/removals
```

**Dictionary<TKey, TValue> - Key-Value Pairs:**

```csharp
var ages = new Dictionary<string, int>
{
    {"John", 25},
    {"Jane", 30}
};
ages["Bob"] = 28;
// Good for: Fast lookups by key, unique keys
```

**HashSet<T> - Unique Elements:**

```csharp
var uniqueNumbers = new HashSet<int> {1, 2, 2, 3, 3, 4};
// uniqueNumbers contains: {1, 2, 3, 4}
// Good for: Ensuring uniqueness, set operations
```

**Queue<T> - FIFO:**

```csharp
var queue = new Queue<string>();
queue.Enqueue("First");
queue.Enqueue("Second");
string first = queue.Dequeue(); // "First"
// Good for: Processing items in order
```

**Stack<T> - LIFO:**

```csharp
var stack = new Stack<int>();
stack.Push(1);
stack.Push(2);
int top = stack.Pop(); // 2
// Good for: Undo operations, expression evaluation
```

**LINQ Examples:**

```csharp
var numbers = new[] {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// Filtering
var evens = numbers.Where(n => n % 2 == 0);

// Projection
var squares = numbers.Select(n => n * n);

// Aggregation
var sum = numbers.Sum();
var max = numbers.Max();

// Grouping
var grouped = numbers.GroupBy(n => n % 2 == 0 ? "Even" : "Odd");

// Ordering
var sorted = numbers.OrderByDescending(n => n);

// Chaining
var result = numbers
    .Where(n => n > 5)
    .Select(n => n * 2)
    .OrderBy(n => n)
    .ToList();
```

---

## 🎓 Interview Tips

### For Candidates:

1. **Understand the Fundamentals**: Know the difference between value and reference types
2. **OOP Principles**: Be able to explain and demonstrate the four pillars
3. **Memory Management**: Understand garbage collection and disposal patterns
4. **Exception Handling**: Know best practices and common pitfalls
5. **Collections**: Understand when to use different collection types

### For Interviewers:

1. **Start with Basics**: Begin with fundamental concepts before advanced topics
2. **Code Examples**: Ask candidates to write code, not just explain concepts
3. **Real-world Scenarios**: Use practical examples from actual development
4. **Performance Questions**: Include questions about memory and performance implications
5. **Best Practices**: Test knowledge of coding standards and patterns

---

## 📚 Additional Resources

- [C# Programming Guide](https://docs.microsoft.com/en-us/dotnet/csharp/)
- [C# Language Reference](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/)
- [.NET API Reference](https://docs.microsoft.com/en-us/dotnet/api/)
- [C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)

---

[⬆️ Back to Top](#c-basics-interview-questions--answers-)
