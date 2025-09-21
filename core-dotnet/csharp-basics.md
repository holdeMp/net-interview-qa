# C# Basics Interview Questions & Answers 💻

> Comprehensive collection of C# fundamentals interview questions and answers for .NET developers. Covering language features, OOP concepts, and core programming principles.

## 📋 Table of Contents

- [Language Fundamentals](#language-fundamentals)
- [Object-Oriented Programming](#object-oriented-programming)
- [Delegates and Events](#delegates-and-events)
- [Memory Management](#memory-management)
- [Exception Handling](#exception-handling)
- [Collections and LINQ](#collections-and-linq)
  - [Collection Interfaces](#collection-interfaces)
  - [Collection Types](#collection-types)
- [Threading and Concurrency](#threading-and-concurrency)

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

### Q2: What are the `in`, `ref`, and `out` modifiers in C#?

**Answer:**
These modifiers control how parameters are passed to methods and whether they can be modified:

| Modifier  | Purpose                      | Can Modify | Must Initialize | Performance                   |
| --------- | ---------------------------- | ---------- | --------------- | ----------------------------- |
| **`ref`** | Parameter may be modified    | ✅ Yes     | ✅ Yes          | Pass by reference             |
| **`in`**  | Parameter cannot be modified | ❌ No      | ✅ Yes          | Pass by reference (read-only) |
| **`out`** | Parameter must be modified   | ✅ Yes     | ❌ No           | Pass by reference             |

**`ref` Modifier:**

```csharp
public void ModifyValue(ref int value)
{
    value = 42; // Can modify the original variable
}

// Usage
int number = 10;
ModifyValue(ref number);
Console.WriteLine(number); // Output: 42
```

**`in` Modifier (C# 7.2+):**

```csharp
public void ProcessData(in LargeStruct data)
{
    // Can read but cannot modify 'data'
    Console.WriteLine($"Processing: {data.Value}");
    // data.Value = 100; // Compile error - cannot modify
}

// Usage
var largeData = new LargeStruct { Value = 100 };
ProcessData(in largeData); // Explicit 'in' keyword (optional)
ProcessData(largeData);    // Implicit 'in' - compiler infers it
```

**`out` Modifier:**

```csharp
public bool TryParseNumber(string input, out int result)
{
    if (int.TryParse(input, out int parsed))
    {
        result = parsed; // Must assign a value
        return true;
    }
    result = 0; // Must assign a value even on failure
    return false;
}

// Usage
if (TryParseNumber("123", out int number))
{
    Console.WriteLine($"Parsed: {number}");
}
```

**Practical Examples:**

```csharp
// ref - for modifying existing values
public void Swap(ref int a, ref int b)
{
    int temp = a;
    a = b;
    b = temp;
}

// in - for large structs (performance optimization)
public struct Point3D
{
    public double X, Y, Z;
    // ... other fields
}

public double CalculateDistance(in Point3D p1, in Point3D p2)
{
    // Read-only access, no copying of large struct
    return Math.Sqrt(Math.Pow(p1.X - p2.X, 2) +
                     Math.Pow(p1.Y - p2.Y, 2) +
                     Math.Pow(p1.Z - p2.Z, 2));
}

// out - for multiple return values
public bool TryDivide(double dividend, double divisor, out double result)
{
    if (divisor != 0)
    {
        result = dividend / divisor;
        return true;
    }
    result = 0;
    return false;
}

// Usage with multiple out parameters
if (TryDivide(10, 2, out double quotient))
{
    Console.WriteLine($"Result: {quotient}");
}
```

**Key Points:**

- `ref` and `out` parameters must be explicitly marked at both declaration and call site
- `in` parameters can be called without the `in` keyword (compiler infers it)
- `out` parameters don't need to be initialized before the method call
- `in` parameters are read-only and cannot be modified within the method
- These modifiers are particularly useful for performance optimization with large value types

### Q3: Explain the difference between value types and reference types.

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

### Q4: What are the four pillars of OOP in C#?

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

### Q5: What are the differences between abstract classes and interfaces in C#?

**Answer:**
The differences between abstract classes and interfaces have evolved significantly with C# 8.0. Based on the [comprehensive analysis by Jeremy Bytes](https://jeremybytes.blogspot.com/2020/10/abstract-classes-vs-interfaces-in-c.html), here's how the traditional differences have changed:

**Traditional Differences (Pre-C# 8.0):**

| Aspect                  | Abstract Class                                              | Interface (Pre-C# 8)                       |
| ----------------------- | ----------------------------------------------------------- | ------------------------------------------ |
| **Implementation Code** | ✅ May contain implementation                               | ❌ Only declarations                       |
| **Inheritance**         | Single inheritance only                                     | Multiple implementation                    |
| **Access Modifiers**    | ✅ Public, private, protected                               | ❌ Always public                           |
| **Members**             | Fields, properties, constructors, methods, events, indexers | Properties, methods, events, indexers only |
| **Static Members**      | ✅ Supported                                                | ❌ Not supported                           |

**Modern Differences (C# 8.0+):**

| Aspect                  | Abstract Class                                              | Interface (C# 8+)                     | Status               |
| ----------------------- | ----------------------------------------------------------- | ------------------------------------- | -------------------- |
| **Implementation Code** | ✅ May contain implementation                               | ✅ Default implementation             | **Changed**          |
| **Inheritance**         | Single inheritance only                                     | Multiple implementation               | **Unchanged**        |
| **Access Modifiers**    | ✅ Public, private, protected                               | ✅ Public, private, protected         | **Changed**          |
| **Instance Members**    | Fields, properties, constructors, methods, events, indexers | Properties, methods, events, indexers | **Mostly Unchanged** |
| **Static Members**      | ✅ Supported                                                | ✅ Supported                          | **Changed**          |

**1. Implementation Code (C# 8+ Change):**

**Interfaces with Default Implementation:**

```csharp
public interface ILogger
{
    // Traditional interface method
    void Log(string message);

    // Default implementation (C# 8+)
    void LogError(string message)
    {
        Log($"ERROR: {message}");
    }

    // Default implementation with parameters
    void LogWithTimestamp(string message)
    {
        Log($"[{DateTime.Now:yyyy-MM-dd HH:mm:ss}] {message}");
    }
}

public class FileLogger : ILogger
{
    public void Log(string message)
    {
        // Must implement this
        File.AppendAllText("log.txt", message + Environment.NewLine);
    }

    // LogError and LogWithTimestamp are automatically available
    // Can override if needed
    public void LogError(string message)
    {
        Log($"CRITICAL ERROR: {message}");
    }
}
```

**2. Access Modifiers (C# 8+ Change):**

```csharp
public interface IDataProcessor
{
    // Public by default
    void ProcessData(string data);

    // Private method for internal use
    private string ValidateData(string data)
    {
        return string.IsNullOrEmpty(data) ? "Invalid" : data;
    }

    // Public method using private method
    void ProcessAndValidate(string data)
    {
        var validatedData = ValidateData(data);
        ProcessData(validatedData);
    }
}
```

**3. Static Members (C# 8+ Change):**

```csharp
public interface IMathOperations
{
    // Static field
    static readonly double Pi = 3.14159;

    // Static method
    static double CalculateCircleArea(double radius)
    {
        return Pi * radius * radius;
    }

    // Instance method
    double Add(double a, double b);
}

public class Calculator : IMathOperations
{
    public double Add(double a, double b) => a + b;

    // Can access static members
    public double GetCircleArea(double radius)
    {
        return IMathOperations.CalculateCircleArea(radius);
    }
}
```

**4. Multiple Inheritance Through Interfaces:**

```csharp
public interface IReadable
{
    string Read();
}

public interface IWritable
{
    void Write(string content);
}

public interface IReadWrite : IReadable, IWritable
{
    // Can combine multiple interfaces
    bool IsReadOnly { get; }
}

public class Document : IReadWrite
{
    private string content = "";

    public string Read() => content;

    public void Write(string newContent) => content = newContent;

    public bool IsReadOnly => false;
}

// Usage
public void ProcessDocument(IReadWrite document)
{
    // Can call methods from both interfaces
    var data = document.Read();
    document.Write(data.ToUpper());
}
```

**5. Diamond Problem Avoidance:**

```csharp
public interface IInterfaceA
{
    void Method() => Console.WriteLine("Interface A");
}

public interface IInterfaceB
{
    void Method() => Console.WriteLine("Interface B");
}

public class MyClass : IInterfaceA, IInterfaceB
{
    // Must explicitly implement to avoid ambiguity
    void IInterfaceA.Method() => Console.WriteLine("Explicit A");
    void IInterfaceB.Method() => Console.WriteLine("Explicit B");

    // Or provide a single implementation
    public void Method() => Console.WriteLine("MyClass implementation");
}

// Usage - must specify interface type
MyClass obj = new MyClass();
((IInterfaceA)obj).Method(); // Calls explicit A implementation
((IInterfaceB)obj).Method(); // Calls explicit B implementation
obj.Method(); // Calls public implementation
```

**When to Use Each (Modern Guidelines):**

**Use Abstract Classes when:**

- There **is** shared code between implementations
- You need instance fields or constructors
- You want to provide a base implementation that can be extended
- You need protected members for derived classes

```csharp
public abstract class Animal
{
    protected string name; // Instance field
    protected int age;     // Instance field

    public Animal(string name, int age)
    {
        this.name = name;
        this.age = age;
    }

    // Shared implementation
    public virtual void MakeSound()
    {
        Console.WriteLine($"{name} makes a sound");
    }

    // Abstract method - must be implemented
    public abstract void Move();
}

public class Dog : Animal
{
    public Dog(string name, int age) : base(name, age) { }

    public override void Move() => Console.WriteLine($"{name} runs");

    public override void MakeSound() => Console.WriteLine($"{name} barks");
}
```

**Use Interfaces when:**

- There is **no** shared code between implementations
- You want to define a contract/capability
- You need multiple inheritance
- You want to use mix-ins (interfaces with only default implementations)

```csharp
// Mix-in example - interface with only default implementations
public interface ILogging
{
    void Log(string message) => Console.WriteLine($"[LOG] {message}");
    void LogError(string message) => Console.WriteLine($"[ERROR] {message}");
}

public interface IValidation
{
    bool IsValid(string input) => !string.IsNullOrEmpty(input);
    string Sanitize(string input) => input?.Trim() ?? "";
}

// Class gets functionality by implementing interfaces
public class UserService : ILogging, IValidation
{
    public void CreateUser(string username)
    {
        if (IsValid(username)) // From IValidation
        {
            var sanitized = Sanitize(username); // From IValidation
            Log($"Creating user: {sanitized}"); // From ILogging
            // Implementation...
        }
        else
        {
            LogError("Invalid username provided"); // From ILogging
        }
    }
}
```

**Key Takeaways:**

1. **Technical differences have blurred** - 3 out of 5 traditional differences no longer apply
2. **Interfaces are more powerful** - Can now have implementation, access modifiers, and static members
3. **Choose based on usage patterns** - Abstract classes for shared code, interfaces for contracts
4. **Consider mix-ins** - Interfaces with only default implementations can provide reusable functionality
5. **Explicit interface implementation** - Helps avoid the diamond problem with multiple inheritance

**Best Practice:**
Stick with the "path of least surprise" - use abstract classes when you have shared implementation code, and interfaces when you're defining capabilities or contracts, even though the technical differences have become less distinct.

---

## 🎯 Delegates and Events

### Q6: What is the difference between delegates and lambdas?

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

### Q7: What are events and how do they work with delegates?

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

### Q8: What is the difference between multiprocessing and multithreading?

**Answer:**
Multiprocessing and multithreading are two different approaches to concurrent execution. For comprehensive coverage of threading concepts, multithreading, async/await patterns, synchronization, and parallel programming, please refer to our dedicated [C# Threading and Concurrency Guide](csharp-threading.md).

**Quick Summary:**

| Aspect              | Multiprocessing                              | Multithreading                                  |
| ------------------- | -------------------------------------------- | ----------------------------------------------- |
| **Definition**      | Multiple processes running simultaneously    | Multiple threads within a single process        |
| **Memory**          | Separate memory spaces                       | Shared memory space                             |
| **Communication**   | IPC (Inter-Process Communication)            | Direct memory access                            |
| **Overhead**        | Higher (process creation/context switching)  | Lower (thread creation/context switching)       |
| **Fault Isolation** | Better (process crash doesn't affect others) | Poorer (thread crash can affect entire process) |
| **Scalability**     | Limited by CPU cores                         | Limited by CPU cores and memory                 |
| **Platform**        | Cross-platform                               | Platform-dependent                              |

**When to Use Each:**

- **Multiprocessing**: Complete isolation, different applications, maximum fault tolerance
- **Multithreading**: Shared data, single application, I/O-bound operations, UI responsiveness

For detailed examples, best practices, and advanced threading concepts, see the [Threading and Concurrency section](csharp-threading.md).

### Q9: Explain garbage collection in C#.

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

### Q10: What are finalizers and how do they differ from IDisposable?

**Answer:**
Finalizers and IDisposable serve different purposes in resource management, though they can work together to ensure proper cleanup of unmanaged resources.

**Finalizers (Destructors):**

Finalizers are special methods that are automatically called by the garbage collector when an object is being collected. They provide a safety net for cleaning up unmanaged resources.

**Key Characteristics:**

- **Automatic invocation** - Called by garbage collector, not manually
- **No control over timing** - GC decides when to call them
- **Performance impact** - Objects with finalizers take longer to collect
- **Inheritance chain** - Calls base class finalizers automatically
- **One per class** - Cannot be overloaded or inherited

**Basic Finalizer Syntax:**

```csharp
class ResourceManager
{
    private IntPtr unmanagedResource;

    // Finalizer (destructor)
    ~ResourceManager()
    {
        // Cleanup unmanaged resources
        if (unmanagedResource != IntPtr.Zero)
        {
            // Free unmanaged resource
            FreeUnmanagedResource(unmanagedResource);
            unmanagedResource = IntPtr.Zero;
        }
    }

    private void FreeUnmanagedResource(IntPtr resource)
    {
        // Platform-specific cleanup code
        // e.g., CloseHandle, Marshal.FreeHGlobal, etc.
    }
}
```

**Expression Body Finalizer:**

```csharp
public class SimpleFinalizer
{
    ~SimpleFinalizer() => Console.WriteLine("Finalizer executing");
}
```

**Finalizer Execution Chain:**

```csharp
class First
{
    ~First()
    {
        Console.WriteLine("First's finalizer called");
    }
}

class Second : First
{
    ~Second()
    {
        Console.WriteLine("Second's finalizer called");
    }
}

class Third : Second
{
    ~Third()
    {
        Console.WriteLine("Third's finalizer called");
    }
}

// When Third object is collected:
// Output: Third's finalizer called
//         Second's finalizer called
//         First's finalizer called
```

**IDisposable Interface:**

IDisposable provides explicit control over resource cleanup and should be used for deterministic cleanup.

**Basic IDisposable Implementation:**

```csharp
public class ResourceManager : IDisposable
{
    private IntPtr unmanagedResource;
    private bool disposed = false;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Prevent finalizer from running
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!disposed)
        {
            if (disposing)
            {
                // Dispose managed resources
                // e.g., close files, database connections
            }

            // Dispose unmanaged resources
            if (unmanagedResource != IntPtr.Zero)
            {
                FreeUnmanagedResource(unmanagedResource);
                unmanagedResource = IntPtr.Zero;
            }

            disposed = true;
        }
    }

    ~ResourceManager()
    {
        Dispose(false); // Only cleanup unmanaged resources
    }
}
```

**Key Differences:**

| Aspect          | Finalizers                             | IDisposable                        |
| --------------- | -------------------------------------- | ---------------------------------- |
| **Timing**      | Non-deterministic (GC decides)         | Deterministic (developer controls) |
| **Performance** | Slower (objects in finalization queue) | Faster (immediate cleanup)         |
| **Control**     | No control over when called            | Full control over when called      |
| **Reliability** | Not guaranteed to run                  | Guaranteed if called properly      |
| **Use Case**    | Safety net for unmanaged resources     | Primary cleanup mechanism          |

**When to Use Each:**

**Use Finalizers when:**

- You have unmanaged resources that must be cleaned up
- As a safety net in case Dispose() is not called
- Working with native handles or unmanaged memory

```csharp
public class FileHandler
{
    private IntPtr fileHandle;

    public FileHandler(string fileName)
    {
        fileHandle = CreateFile(fileName); // P/Invoke call
    }

    ~FileHandler()
    {
        // Safety net - cleanup if Dispose wasn't called
        if (fileHandle != IntPtr.Zero)
        {
            CloseHandle(fileHandle);
        }
    }
}
```

**Use IDisposable when:**

- You need deterministic cleanup
- Working with expensive resources (files, database connections)
- Implementing the using pattern
- Need immediate resource release

```csharp
public class DatabaseConnection : IDisposable
{
    private SqlConnection connection;

    public DatabaseConnection(string connectionString)
    {
        connection = new SqlConnection(connectionString);
        connection.Open();
    }

    public void Dispose()
    {
        connection?.Close();
        connection?.Dispose();
    }
}

// Usage with using statement
using (var db = new DatabaseConnection(connectionString))
{
    // Work with database
} // Automatically calls Dispose()
```

**Best Practices:**

**1. Implement Both for Unmanaged Resources:**

```csharp
public class UnmanagedResource : IDisposable
{
    private IntPtr handle;
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
                // Cleanup managed resources
            }

            // Cleanup unmanaged resources
            if (handle != IntPtr.Zero)
            {
                CloseHandle(handle);
                handle = IntPtr.Zero;
            }

            disposed = true;
        }
    }

    ~UnmanagedResource()
    {
        Dispose(false);
    }
}
```

**2. Use SafeHandle for Unmanaged Resources:**

```csharp
public class SafeFileHandle : SafeHandleZeroOrMinusOneIsInvalid
{
    public SafeFileHandle(IntPtr handle) : base(true)
    {
        SetHandle(handle);
    }

    protected override bool ReleaseHandle()
    {
        return CloseHandle(handle);
    }
}
```

**3. Avoid Empty Finalizers:**

```csharp
// ❌ Bad - Empty finalizer causes performance issues
class BadExample
{
    ~BadExample() { } // Don't do this
}

// ✅ Good - Only add finalizer if you have unmanaged resources
class GoodExample
{
    private IntPtr unmanagedResource;

    ~GoodExample()
    {
        if (unmanagedResource != IntPtr.Zero)
        {
            // Cleanup code
        }
    }
}
```

**4. Use Using Statements:**

```csharp
// ✅ Good - Automatic disposal
using (var resource = new ResourceManager())
{
    // Use resource
} // Dispose() called automatically

// ❌ Bad - Manual disposal (easy to forget)
var resource = new ResourceManager();
// Use resource
resource.Dispose(); // Might be forgotten
```

**5. Handle Application Termination:**

```csharp
public class Application
{
    static void Main()
    {
        // Register cleanup handler
        AppDomain.CurrentDomain.ProcessExit += (sender, e) =>
        {
            // Ensure all IDisposable objects are disposed
            // before application exits
        };

        // Application code...
    }
}
```

**Important Notes:**

1. **.NET Framework vs .NET 5+**: Finalizers behave differently on application termination

   - .NET Framework: Calls finalizers on app exit
   - .NET 5+: Does NOT call finalizers on app exit

2. **Performance Impact**: Objects with finalizers take longer to garbage collect

3. **Reliability**: Never rely solely on finalizers for critical cleanup

4. **SuppressFinalize**: Always call `GC.SuppressFinalize(this)` in Dispose() to prevent finalizer execution

**Summary:**

- **Finalizers**: Safety net for unmanaged resources, non-deterministic
- **IDisposable**: Primary cleanup mechanism, deterministic
- **Best Practice**: Use both for unmanaged resources, prefer IDisposable for managed resources
- **Pattern**: Implement IDisposable.Dispose() for immediate cleanup, finalizer as backup

---

## ⚠️ Exception Handling

### Q11: What is the difference between `throw` and `throw ex`?

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

### Collection Interfaces

### Q12: What are the different collection interfaces in .NET and their hierarchy?

**Answer:**
Understanding the collection interface hierarchy is crucial for choosing the right abstraction level. Based on the [.NET collection interfaces comparison](https://bool.dev/blog/detail/sravnenie-kollektsiy-v-net), here's the complete hierarchy:

**Interface Hierarchy:**

```
IEnumerable (non-generic)
    ↓
IEnumerable<T> (generic)
    ↓
ICollection<T>
    ↓
IList<T>
```

**1. IEnumerable<T> - Read-Only Iteration:**

```csharp
public interface IEnumerable<out T> : IEnumerable
{
    IEnumerator<T> GetEnumerator();
}
```

- **Purpose**: Enables foreach iteration and LINQ operations
- **Characteristics**: Read-only access, forward-only iteration
- **Use when**: You only need to iterate through elements
- **Performance**: Lazy evaluation, deferred execution

```csharp
IEnumerable<int> numbers = new[] {1, 2, 3, 4, 5};
foreach (var number in numbers)
{
    Console.WriteLine(number);
}

// LINQ operations
var evens = numbers.Where(n => n % 2 == 0);
```

**2. ICollection<T> - Collection Manipulation:**

```csharp
public interface ICollection<T> : IEnumerable<T>, IEnumerable
{
    int Count { get; }
    bool IsReadOnly { get; }

    void Add(T item);
    void Clear();
    bool Contains(T item);
    void CopyTo(T[] array, int arrayIndex);
    bool Remove(T item);
}
```

- **Purpose**: Adds collection modification capabilities
- **Characteristics**: Can add, remove, clear, and check count
- **Use when**: You need to modify the collection
- **Performance**: O(1) for Count, varies for other operations

```csharp
ICollection<string> names = new List<string>();
names.Add("John");
names.Add("Jane");
Console.WriteLine($"Count: {names.Count}");
names.Remove("John");
```

**3. IList<T> - Indexed Access:**

```csharp
public interface IList<T> : ICollection<T>, IEnumerable<T>, IEnumerable
{
    T this[int index] { get; set; }

    int IndexOf(T item);
    void Insert(int index, T item);
    void RemoveAt(int index);
}
```

- **Purpose**: Adds indexed access and positional operations
- **Characteristics**: Can access elements by index, insert at specific positions
- **Use when**: You need random access to elements
- **Performance**: O(1) for indexed access, O(n) for insertions/removals

```csharp
IList<int> numbers = new List<int> {1, 2, 3, 4, 5};
int first = numbers[0];           // Indexed access
numbers.Insert(2, 10);           // Insert at position
int index = numbers.IndexOf(3);  // Find position
```

**4. IQueryable<T> - Database Querying:**

```csharp
public interface IQueryable<out T> : IEnumerable<T>, IEnumerable, IQueryable
{
    // Expression tree-based queries
}
```

- **Purpose**: Enables database query translation
- **Characteristics**: Expression trees, server-side filtering
- **Use when**: Working with databases (Entity Framework, LINQ to SQL)
- **Performance**: Queries translated to SQL, only filtered data returned

```csharp
// Entity Framework example
IQueryable<Employee> employees = context.Employees
    .Where(e => e.Name.StartsWith("S"))
    .Take(10);

// Generates optimized SQL:
// SELECT TOP 10 [e].[Id], [e].[Name] FROM [Employees] AS [e]
// WHERE [e].[Name] LIKE 'S%'
```

**Key Differences:**

| Interface          | Read-Only | Modification | Indexed Access | Database Query | Use Case                                |
| ------------------ | --------- | ------------ | -------------- | -------------- | --------------------------------------- |
| **IEnumerable<T>** | ✅        | ❌           | ❌             | ❌             | Iteration, LINQ to Objects              |
| **ICollection<T>** | ❌        | ✅           | ❌             | ❌             | Collection manipulation                 |
| **IList<T>**       | ❌        | ✅           | ✅             | ❌             | Random access, ordered collections      |
| **IQueryable<T>**  | ✅        | ❌           | ❌             | ✅             | Database queries, server-side filtering |

**When to Use Each:**

**Use IEnumerable<T> when:**

- Only need to iterate through elements
- Working with LINQ to Objects
- Want to protect against unintended modifications
- Performance is critical (lazy evaluation)

**Use ICollection<T> when:**

- Need to add/remove items
- Want to know collection size
- Don't need indexed access
- Working with sets, bags, or other non-indexed collections

**Use IList<T> when:**

- Need random access to elements
- Working with ordered collections
- Need to insert/remove at specific positions
- Performance of indexed access is important

**Use IQueryable<T> when:**

- Working with databases
- Need server-side filtering
- Want to optimize database queries
- Working with large datasets

**Best Practices:**

```csharp
// ✅ Good - Use the most specific interface needed
public void ProcessItems(IEnumerable<string> items)
{
    foreach (var item in items)
    {
        ProcessItem(item);
    }
}

// ✅ Good - Use ICollection when you need to modify
public void AddItems(ICollection<string> collection, string[] newItems)
{
    foreach (var item in newItems)
    {
        collection.Add(item);
    }
}

// ✅ Good - Use IList when you need indexed access
public string GetItemAt(IList<string> list, int index)
{
    return list[index];
}

// ❌ Avoid - Don't use concrete types in method signatures
public void ProcessItems(List<string> items) // Too specific
{
    // Implementation
}
```

### Collection Types

### Q13: What are the main collection types in C# and when to use each?

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

## 🧵 Threading and Concurrency

For comprehensive coverage of threading concepts, multithreading, async/await patterns, synchronization, and parallel programming, please refer to our dedicated [C# Threading and Concurrency Guide](csharp-threading.md).

This guide covers:

- Multithreading vs Multiprocessing
- Thread Management
- Synchronization and Thread Safety
- Async/Await Pattern
- Parallel Programming
- Thread Pool and Task Scheduler
- Best Practices

---

[⬆️ Back to Top](#c-basics-interview-questions--answers-)
