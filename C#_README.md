# C\# Comprehensive Study Guide

## Table of Contents

1. [C\# Fundamentals](#c-fundamentals)  
2. [.NET Architecture & Compilation](#net-architecture--compilation)  
3. [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)  
4. [Advanced C\# Concepts](#advanced-c-concepts)  
5. [Collections & Generics](#collections--generics)  
6. [Delegates, Events & Lambda Expressions](#delegates-events--lambda-expressions)  
7. [Exception Handling](#exception-handling)  
8. [File I/O & Serialization](#file-io--serialization)  
10. [Multi-Threading & Asynchronous Programming](#multi-threading--asynchronous-programming)  
11. [Design Patterns](#design-patterns)  
12. [SOLID Principles](#solid-principles)  
13. [Caching Strategies](#caching-strategies)  
20. [Testing & Development Practices](#testing--development-practices)  
21. [Performance Optimization](#performance-optimization)  
22. [Deployment & DevOps](#deployment--devops)  
23. [Search Algorithms](#search-algorithms)  
24. [Interview Questions](#interview-questions)  
25. [Quick Reference](#quick-reference)

---

## C\# Fundamentals

### Basic Syntax & Data Types

- **Variables are case sensitive and strongly typed**  
    
- **Explicit vs Implicit Typing:**  
    
  int i = 100;   // explicitly typed variable  
    
  var j = 100;   // implicitly typed local variable  
    
- **Default Values:**  
    
  int i = default;       // 0  
    
  float f = default;     // 0  
    
  decimal d = default;   // 0  
    
  bool b = default;      // false  
    
  char c = default;      // '\\0'  
    
- **String Handling:**  
    
  // Escape characters  
    
  string path = "test@gmail\\".com";  
    
  // Verbatim string literal  
    
  string path = @"test@gmail"".com";  
    
  // String interpolation  
    
  string fullName = $"Mr. {firstName} {lastName}, Code: {code}";  
    
- **Nullable Types:**  
    
  int? i = null;              
    
  int j = i ?? 0; // null coalescing operator

### DateTime Operations

DateTime currentDateTime = DateTime.Now;      // current date & time

DateTime todaysDate = DateTime.Today;         // today's date

DateTime currentDateTimeUTC = DateTime.UtcNow;// current UTC date & time

DateTime maxDateTimeValue = DateTime.MaxValue;

DateTime minDateTimeValue = DateTime.MinValue;

// Date parsing

var str = "5/12/2020";

DateTime dt;

var isValidDate = DateTime.TryParse(str, out dt);

if(isValidDate)

    Console.WriteLine(dt);

else

    Console.WriteLine($"{str} is not a valid date string");

### Type Conversions

| Method | Use Case | Notes |
| :---- | :---- | :---- |
| `(int) cast` | Compatible numeric types | Not valid for strings |
| `Convert.ToInt32` | General-purpose conversion | Handles null gracefully, accepts object/string/bool |
| `int.Parse` | Valid integer strings | Fast but unforgiving, only accepts strings |
| `int.TryParse` | User input/uncertain data | Prevents crashes, handles failures gracefully |

string str = "123";

int a = Convert.ToInt32(str);     // ✅ Works

int b = int.Parse(str);           // ✅ Works

bool success = int.TryParse(str, out int c); // ✅ Works, success = true

string badStr = "abc";

int d = Convert.ToInt32(badStr);  // ❌ Throws FormatException

int e = int.Parse(badStr);        // ❌ Throws FormatException

bool ok = int.TryParse(badStr, out int f); // ✅ ok = false, f = 0

### Control Flow

// Switch statement

switch(variable){

    case 1:

        //code 

        break;

    case 2:

        //code 

        break;

    default:

        break;

}

// Ternary operator

var result = x \> y ? "x is greater than y" : "x is less than y";

var test = value ?? 0; // if value is there else 0 value

### Stack vs Heap Memory
--------------------------------     -------------------------------------

When Example() finishes, the stack frame (with x, name, and p) is cleared.

The heap memory for "Alice" and the Person object remains until garbage-collected.

- **Stack**: faster, LIFO, for primitive datatype, handle by CPU, lifespan only for func end  
- **Heap**: slower compare to stack, collected by garbage collector, for reference type variable

      STACK FRAME                              MANAGED HEAP
   (Stores values & ptrs)                   (Stores objects)
+-------------------------+              +-------------------------+
|                         |              |                         |
|  int test = 100;        |              |                         |
|  [ 100 ]                |              |                         |
|                         |              |                         |
|  Person person;         |              |  [ Person Object ]      |
|  [ 0x00A1 ] -------------------------> |  +-------------------+  |
|  (Memory Address)       |              |  | Name: "Akshay"    |  |
|                         |              |  | Age: 30           |  |
|                         |              |  +-------------------+  |
|                         |              |                         |
+-------------------------+              +-------------------------+

### Value Types Examples (Stack Storage)

```csharp
int x = 42;           // Stored directly on stack
bool isActive = true; // Stored directly on stack
double price = 19.99; // Stored directly on stack
DateTime now = DateTime.Now; // Stored directly on stack
```

### Reference Types Examples (Stack + Heap)

```csharp
string name = "Alice";     // Reference on stack, object on heap
Person person = new Person(); // Reference on stack, object on heap
int[] numbers = new int[5];   // Reference on stack, array on heap
```

### String Special Cases

```csharp
string str1 = "Hello";     // Stored on heap
string str2 = "Hello";     // Points to same heap location (interning)
string str3 = new string("Hello"); // Creates new heap allocation
```


--------------------------------     -------------------------------------
- **Stack**: faster, LIFO, for primitive datatype, handle by CPU, lifespan only for func end  
- **Heap**: slower compare to stack, collected by garbage collector, for reference type variable

A common misconception is that for a value type like `int test = 100;`, both the variable name (`test`) and its value (`100`) are on the stack. This is incorrect.

**At runtime, the variable *name* is not stored on the stack.** The name is a compile-time concept used by the programmer and the compiler. The compiler translates the name `test` into a memory address offset from the stack pointer.

The stack only stores the value (`100`).

**How it works:**

1.  **Compile Time:** The compiler sees `int test = 100;`. It creates a symbol table that maps the name `test` to a specific memory offset, like `StackPointer - 4 bytes`.
2.  **Runtime:** The CPU executes instructions to move the value `100` into that specific memory address on the stack. It doesn't know or store the string "test".

*(Note: Variable names are stored in separate "PDB" files for debugging purposes, but not on the execution stack itself).*

**Stack Frame Diagram Example:**

Here is a visualization of what the Stack Frame looks like for a method containing `int test = 100;`:

```text
      High Memory Address
      |                    |
      |--------------------|
      |   Return Address   | <--- Where to go back after function ends
      |--------------------|
      |                    |
      |    (Value: 100)    | <--- This 4-byte slot is what you called "test".
      |   [ 00000064 ]     |      The compiler mapped "test" to this specific
      |                    |      offset relative to the stack pointer.
      |--------------------|
      |                    |
      |   Other Variables  |
      |                    |
      |--------------------| <--- Current Stack Pointer (SP)
      |                    |
      Low Memory Address
```

**Summary:**

*   **Variable Name (`test`):** Exists only in source code and debug symbols. It is converted to a memory offset by the compiler and takes up **zero** space on the stack at runtime.
*   **Variable Value (`100`):** Stored physically on the stack.
*   **Continuous Memory:** The *value* occupies a continuous block of memory (e.g., 4 bytes for an `int`), but the *name* is not stored with it.

--------------------------------     -------------------------------------
### Boxing and Unboxing

### Boxing and Unboxing Examples

```csharp
// Boxing: Value type -> Reference type
int value = 42;
object boxed = value; // Creates object on heap, copies value

// Unboxing: Reference type -> Value type
int unboxed = (int)boxed; // Copies value back to stack
```

int x = 10;

object y = x;        // Boxing 

int z = (int)y;      // Unboxing

// In old C#, ArrayList was stored using boxing, but with generics introduction this issue is solved.

// boxing and unboxing reduces system performance, hence avoid to use it, use generics instead

### Checked and Unchecked Keywords

```csharp
int.MaxValue = 2147483647

int a = 2147483647

int b = 2147483647

int c = a + b

output = -2 //Unexpected (this will be unchecked default behavior)

int c = checked(a+b) // throws error overflow exception 

int c = unchecked(a+b) // output = -2
```

- **Checked Keyword**: enable overflow checking for int type arithmetic operations and conversion  
- **Unchecked Keyword**: suppress overflow-checking  
- **Overflow**: when the value of any int type exceeds its range

### StringBuilder

```csharp
using System.Text; // include at the top       

StringBuilder sb = new StringBuilder("Start with this text");
sb.Append(" and add more");
string result = sb.ToString(); // "Start with this text and add more"
```

StringBuilder is designed for dynamic string manipulation. It maintains an internal character buffer (an array).

What happens when you modify it? When you use .Append(), it simply adds the characters to the empty space in its existing buffer.
Resizing: If the buffer fills up, it automatically allocates a larger buffer (usually doubling the size), copies the content once, and continues. This happens rarely compared to the constant reallocation of strings.
Impact: Significantly faster for loops or heavy manipulation because it minimizes memory allocation and GC overhead.

### Anonymous Types

```csharp
var student = new { Id = 1, FirstName = "James", LastName = "Bond" };

// Only Public read only properties, var is used to hold values
```

### Enumerations

- Enumeration type assigns constant names to numeric integer values  
- Makes constant values more readable  
- Example: `enumWeekDays.Monday`

```csharp
public enum OrderStatus
{
    Pending,   // 0
    Shipped,   // 1
    Delivered, // 2
    Cancelled  // 3
}
```

---

## .NET Architecture & Compilation

### .NET Framework Evolution

- **Before .NET**: COM Framework  
    
  - **Component Object Model**  
  - VB language for windows applications  
  - ASP (Active Server Pages) language for web applications  
  - **Disadvantages**: No total OOPs implementation & Windows OS only supported


- **.NET**: Network Enabled Technology (Internet)  
    
  - **BCL (Base class library)**: framework provided libraries  
  - **CLR (Common Language Runtime)**: MSIL (Microsoft Intermediate Language) Code ---\> Native Machine language code  
    - provides functions like thread management, garbage collections, type safety, exception handling, etc

### Compilation Process

Source Code (.cs)

    ↓  \[C\# Compiler (csc.exe)\]

CIL/IL Code (.exe/.dll with metadata)

    ↓  \[Loaded by CLR\]

    ↓  \[JIT Compiler (RyuJIT)\]

Native Machine Code (CPU-specific)

    ↓

Execution on the Machine

When code runs on machine, when method calls, CLR will get method to JIT compiler from IL or dll and then convert it into machine code, but for AOT IL to machine code will happen on build time only, so AOT app will be faster.

**AOT Limitations:**

- Reflection and dynamic features has limited support  
- Increased build times  
- Large native executables

### .NET Components

- **COM Framework - VB**  
- **NET**: through Object-Oriented we can implement internet-based applications  
- **BCL (Base Class Libraries)**: provides types for strings, dates, numbers, etc. The Class Library includes APIs for reading and writing files, connecting to databases, drawing, and more.  
- **CLR (Common Language Runtime)**: the execution engine that handles running applications. It provides services like thread management, garbage collection, type safety, exception handling, and more.

### Viewing IL Code

In CMD: `ILDASM` - this is used to see Intermediate Language Code (IL) for any DLL or EXE

---

## Object-Oriented Programming (OOP)

OOPs provide 4 principles. They are:

- **Encapsulation**
- **Inheritance**
- **Polymorphism**
- **Abstraction**

### OOP Principles

**Benefits**: Reusability, Extensibility, Simplicity, and Maintainability

#### 1\. Encapsulation

- Process of defining a class by "hiding its internal data members from outside the class" and accessing those internal data members only through publicly exposed methods or properties  
- Also called data hiding

**Access Modifiers:**

| Modifier | Accessibility |
|----------|---------------|
| `Private` | Only within class |
| `Public` | Accessible to anyone |
| `Protected` | Within class and subclass |
| `Internal` | Within same assembly or library |
| `Protected Internal` | Within same assembly + from derived classes in any assembly |
| `Private Protected` | Within same assembly and from derived classes inside same assembly |

#### 2\. Inheritance

- Process by which the members of one class are transferred to another class  
- **Parent/Base/Superclass**: class from which members are transferred  
- **Derived/Child/Subclass**: class that inherits the Parent/Base/Superclass members  
- Achieve code extensibility through inheritance

#### 3\. Polymorphism

- Ability to take more than one form  
- Same function/operator shows different behaviors by taking different types of values or with different number of values

**Types:**

1. **Static polymorphism/compile-time polymorphism/Early binding**  
    - Static polymorphism is achieved by function overloading and operator overloading
2. **Dynamic polymorphism/Run time polymorphism/Late binding**
    - Dynamic polymorphism is achieved by function overriding

#### 4\. Abstraction

- Process of "representing the essential features" without including the background details  
- Achieved using Interfaces and Abstract Classes.

- **Abstraction and Encapsulation are related**: Abstraction is logical thinking, whereas Encapsulation is its physical implementation

- In short: Abstraction is about **what an object does**, while Encapsulation is about **how it does it** (and keeping the "how" hidden).

### Special Class Types

1. **Abstract Class**  
2. **Sealed Class**  
3. **Partial Class**  
4. **Static Class**

### Constructors

- Special type of method  
- Called when we create instance of class  
- Used to initialize variables  
- Cannot return value  
- Same name as class  
- If we don't define parameterless constructor but have one with parameter, default parameterless constructor will not be created by compiler

#### Copy Constructor

```csharp
public class Person
{
    public string Name;
    public int Age;

    // Parameterized constructor
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    // Copy constructor
    public Person(Person other)
    {
        Name = other.Name;
        Age = other.Age;
    }
}

Person original = new Person("Akshay", 30);

Person copy = new Person(original);
```

#### Static Constructors

- Responsible for initializing static variables  
- Never called explicitly - called Implicitly  
- First to execute in any class  
- Cannot be parameterized, so overloading is not possible

#### Private Constructor

- When a class contains a private constructor, we cannot create an object for the class outside of the class  
- Used to create an object for the class within the same class  
- Using Private Constructor in C\# we can implement the singleton design pattern  
- Not possible to create an instance from outside the class

#### Destructor (Finalizer)

```csharp
~ClassName(){
    // Cleanup code
}
```
- Implicitly Called when the object of the class is destroyed  
- Do not have access modifier

### Fields vs Properties

```csharp
public class Car
{
    private int speed;  // Backing field
    public int Speed {get; set;}   // Property --> preferred one
}
```

### Static Classes

- Can have static constructor, parameterless, access-modifier less  
- Cannot inherit or do OOPs concept on static class  
- Have all properties and methods as static

### String Null Handling

```csharp
string testStr = null;

testStr.ToString()        // will throw NullValueException

Convert.ToString(testStr) // will not throw error
```

### Method Parameters

#### ref and out keywords

In C#, both `ref` and `out` are used to pass arguments by **reference** rather than by **value**.

By default, when you pass a variable to a method, C# makes a copy of it. If you change that copy inside the method, the original variable remains untouched. Using `ref` or `out` tells C# to pass the "address" of the variable, so the method can modify the original value directly.

---

##### 1. The `ref` Keyword

The `ref` keyword is used when you want to pass a variable that **already has a value** into a method to be modified.

* **Requirement:** The variable must be initialized before it is passed.
* **Use Case:** When the method needs to read the current value and potentially update it.

```csharp
void DoubleIt(ref int val) {
    val = val * 2;
}

int myNum = 5; // Must be initialized
DoubleIt(ref myNum);
Console.WriteLine(myNum); // Output: 10
```

---

##### 2. The `out` Keyword

The `out` keyword is used when you want a method to **return multiple values**.

* **Requirement:** The variable does not need to be initialized before being passed, but the method **must** assign a value to it before it returns.
* **Use Case:** Returning a "success" boolean along with the actual data (like `int.TryParse`).

```csharp
void GetUser(out int id, out string name) {
    id = 101;          // Must be assigned inside the method
    name = "Alice";    // Must be assigned inside the method
}

GetUser(out int userId, out string userName);
```

---

###### Key Differences at a Glance

| Feature | `ref` | `out` |
| --- | --- | --- |
| **Initial Value** | Must be initialized before calling. | Not required before calling. |
| **Method Responsibility** | Can read or write the variable. | **Must** assign a value before returning. |
| **Primary Goal** | Modify an existing value. | Return multiple "extra" values. |
| **Compiler Check** | Error if passed uninitialized. | Error if method fails to assign a value. |

---

###### A Common Example: `int.TryParse`

You’ve likely used `out` without realizing it. When converting a string to a number, `TryParse` returns a `bool` (to tell you if it worked) and uses an `out` parameter to give you the actual number.

```csharp
string input = "123";
if (int.TryParse(input, out int result)) {
    // result is now 123
}
```

> **Quick Tip:** Since C# 7.0, you can declare the `out` variable right in the method call (like `out int result` above), which keeps your code much cleaner!

#### Named Parameters

employee.AddEmployeeInfo(IsPermanent: true, Name: "Pranaya", EmpID: 1001, Department:"IT", Mobile:"1234567890" );

public void AddEmployeeInfo(long EmpID, string Name, bool IsPermanent, string Mobile, string Department)

---

## Advanced C\# Concepts

### Extension Methods

- Add functionality to existing types without modifying them  
- Must be static methods in static classes  
- Use `this` keyword for the first parameter

### Partial Classes and Methods

#### **Interview Perspective: Partial Types**

**Core Concept**: Partial types allow you to split the definition of a class, struct, interface, or method across multiple files. This is particularly useful for code generation and large team collaboration.

#### **Partial Classes**

```csharp
// File: Person.Core.cs
public partial class Person
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    
    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}

// File: Person.Validation.cs
public partial class Person
{
    public bool IsValid()
    {
        return !string.IsNullOrWhiteSpace(FirstName) && 
               !string.IsNullOrWhiteSpace(LastName);
    }
    
    public string GetFullName()
    {
        return $"{FirstName} {LastName}";
    }
}

// File: Person.Generated.cs (auto-generated)
public partial class Person
{
    public int Id { get; set; }
    public DateTime CreatedDate { get; set; }
}
```

**Use Cases for Partial Classes:**
- **Code Generation**: Separate generated code from handwritten code
- **Large Teams**: Multiple developers can work on different parts
- **Organization**: Group related functionality in separate files
- **Designer Files**: Windows Forms, WPF, Entity Framework models

#### **Partial Methods**

```csharp
// File: DataAccess.Base.cs
public partial class DataAccess
{
    partial void OnBeforeExecute(string query);
    partial void OnAfterExecute(string query);
    
    public void ExecuteQuery(string query)
    {
        OnBeforeExecute(query); // Optional implementation
        
        // Execute query
        Console.WriteLine($"Executing: {query}");
        
        OnAfterExecute(query); // Optional implementation
    }
}

// File: DataAccess.Logging.cs
public partial class DataAccess
{
    partial void OnBeforeExecute(string query)
    {
        Console.WriteLine($"About to execute: {query}");
    }
    
    partial void OnAfterExecute(string query)
    {
        Console.WriteLine($"Executed successfully: {query}");
    }
}

// If OnBeforeExecute/OnAfterExecute are not implemented,
// the calls are removed by compiler (no runtime overhead)
```

**Partial Method Rules:**
- Must be private
- Cannot return a value (void return type only)
- Cannot have out parameters
- Can have ref parameters
- Implementation is optional

#### **Common Interview Questions**

1. **Q: What's the difference between partial classes and inheritance?**
   - Partial classes: Same class split across files
   - Inheritance: Parent-child relationship between different classes

2. **Q: When would you use partial methods?**
   - For optional hooks in generated code
   - For performance-critical scenarios (no overhead if not implemented)
   - For extensibility points in frameworks

3. **Q: Can partial classes be in different assemblies?**
   - No, all parts must be in the same assembly and namespace

4. **Q: What are the limitations of partial methods?**
   - Must be private, void return type, no out parameters
   - Cannot be virtual, abstract, or override

#### **Best Practices**

- Use partial classes for auto-generated code separation
- Consider partial methods for optional extensibility
- Keep related functionality together when possible
- Document which files contain which parts of the class

### Dynamic vs Var vs Reflection

| Feature | var | dynamic | reflection |
| :---- | :---- | :---- | :---- |
| Type checking | Compile-time | Runtime | Runtime |
| Type safety | Type safe | No type safety | No type safety |
| IntelliSense | Full support | Limited | Limited |
| Performance | Faster | Slower (late binding) | Slowest |
| LINQ support | Excellent | Not ideal | Can be used |

- **var**: compile-time, type safe, intellisense full support, faster performance, excellent for anonymous types in LINQ  
- **dynamic**: runtime, errors only at runtime, no type safety, limited intellisense support, slower due to late binding, not ideal for LINQ

### Reflection

- Determines or inspects the content of an assembly  
- Can dynamically create an instance of a type, bind the type to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties  
- Useful when we don't know if a method is present or not

### Volatile Keyword

- Rarely used in multi-threaded applications for better concurrency handling  
- Indicates that a field might be modified by multiple threads executing at the same time  
- Fields declared volatile are excluded from certain kinds of optimizations

### Indexers

- Access class properties in array-like style  
- Useful when class looks like collection or map-like structure

public class StudentRegistry

{

    private Dictionary\<int, string\> students = new();

    public string this\[int id\]

    {

        get =\> students.ContainsKey(id) ? students\[id\] : "Unknown";

        set =\> students\[id\] = value;

    }

}

StudentRegistry registry = new();

registry\[101\] = "Akshay";

Console.WriteLine(registry\[101\]);  // Output: Akshay

---

## Collections & Generics

### Why Generics?

- **Type Safety**: Compile-time type checking  
- **Performance**: No boxing/unboxing overhead  
- **Code Reusability**: Single implementation for multiple types

### Generics Deep Dive

#### **Interview Perspective: Why Generics Matter**

**Core Concept**: Generics allow you to write type-safe, reusable code that works with multiple data types without sacrificing performance or type safety.

**Key Benefits for Interviews:**
1. **Type Safety at Compile-Time**: Catch errors before runtime
2. **Performance**: Avoid boxing/unboxing overhead from non-generic collections
3. **Code Reusability**: Write once, use with multiple types
4. **IntelliSense Support**: Better development experience

#### **Generic Classes**

```csharp
// Generic class definition
public class GenericContainer<T>
{
    private T _item;
    
    public GenericContainer(T item)
    {
        _item = item;
    }
    
    public T GetItem()
    {
        return _item;
    }
    
    public void SetItem(T item)
    {
        _item = item;
    }
}

// Usage
var intContainer = new GenericContainer<int>(42);
var stringContainer = new GenericContainer<string>("Hello");
```

#### **Generic Constraints**

**Interview Question**: What are generic constraints and why are they useful?

```csharp
// Where clause constraints
public class DataProcessor<T> where T : class, new()
{
    public T CreateInstance()
    {
        return new T(); // Requires new() constraint
    }
}

public class MathOperations<T> where T : struct, IComparable<T>
{
    public T Max(T a, T b)
    {
        return a.CompareTo(b) > 0 ? a : b; // Requires IComparable<T>
    }
}

// Multiple constraints
public class Repository<T> where T : class, IEntity, new()
{
    public void Save(T entity)
    {
        // Can assume T has parameterless constructor and implements IEntity
    }
}
```

**Types of Constraints:**
- `where T : struct` - Value type
- `where T : class` - Reference type
- `where T : new()` - Parameterless constructor
- `where T : BaseClass` - Inheritance constraint
- `where T : InterfaceName` - Interface constraint
- `where T : U` - Naked type constraint

#### **Generic Methods**

```csharp
public class Utilities
{
    // Generic method
    public static void Swap<T>(ref T first, ref T second)
    {
        T temp = first;
        first = second;
        second = temp;
    }
    
    // Generic method with constraints
    public static T Max<T>(T a, T b) where T : IComparable<T>
    {
        return a.CompareTo(b) > 0 ? a : b;
    }
}

// Usage
int x = 10, y = 20;
Utilities.Swap(ref x, ref y);

int max = Utilities.Max(15, 25); // Returns 25
```

#### **Generic Interfaces**

```csharp
public interface IRepository<T>
{
    T GetById(int id);
    IEnumerable<T> GetAll();
    void Add(T entity);
    void Update(T entity);
    void Delete(T entity);
}

public class SqlRepository<T> : IRepository<T> where T : class, IEntity
{
    public T GetById(int id)
    {
        // Database logic
        return default(T);
    }
    
    public IEnumerable<T> GetAll()
    {
        // Database logic
        return new List<T>();
    }
    
    public void Add(T entity) { /* Implementation */ }
    public void Update(T entity) { /* Implementation */ }
    public void Delete(T entity) { /* Implementation */ }
}
```

#### **Generic Collections Performance**

**Interview Question**: Why are generic collections faster than ArrayList?

```csharp
// Non-generic (ArrayList) - causes boxing/unboxing
ArrayList list = new ArrayList();
list.Add(42); // Boxing: int -> object
int value = (int)list[0]; // Unboxing: object -> int

// Generic (List<T>) - no boxing/unboxing
List<int> genericList = new List<int>();
genericList.Add(42); // Direct storage
int value = genericList[0]; // Direct access
```

**Performance Impact:**
- **Boxing**: Value type -> Reference type (heap allocation)
- **Unboxing**: Reference type -> Value type (type checking)
- **Memory Overhead**: Additional objects in heap
- **GC Pressure**: More objects to garbage collect

#### **Covariance and Contravariance in Generics**

```csharp
// Covariance (out) - can return derived types
public interface IEnumerable<out T>
{
    IEnumerator<T> GetEnumerator();
}

// Contravariance (in) - can accept base types
public interface IComparer<in T>
{
    int Compare(T x, T y);
}

// Usage
IEnumerable<string> strings = new List<string>();
IEnumerable<object> objects = strings; // Covariance works

IComparer<object> objectComparer = new MyComparer();
IComparer<string> stringComparer = objectComparer; // Contravariance works
```

#### **Common Generic Interview Questions**

1. **Q: What's the difference between `List<T>` and `ArrayList`?**
   - `List<T>` is type-safe, no boxing/unboxing
   - `ArrayList` stores objects, requires casting
   - `List<T>` is faster and more memory efficient

2. **Q: When would you use generic constraints?**
   - When you need to call methods on the type parameter
   - When you need to create instances of the type
   - When you need to ensure certain capabilities

3. **Q: What is the difference between `where T : class` and `where T : struct`?**
   - `class`: T must be a reference type
   - `struct`: T must be a value type

4. **Q: Can you have multiple generic constraints?**
   - Yes: `where T : class, new(), IEntity`

#### **Best Practices**

- Use generic collections over non-generic ones
- Apply constraints when necessary
- Consider `IEnumerable<T>` for return types
- Use `IList<T>` for mutable collections
- Prefer generic interfaces for public APIs

### Collection Types

- **Array vs ArrayList**: Arrays are fixed-size, type-safe; ArrayList is dynamic but not type-safe  
- **Collection vs Generics**: Generics provide type safety and better performance  
- **Why avoid HashTables**: Not type-safe, performance issues with boxing/unboxing

### IEnumerable vs IEnumerator vs IQueryable

| Feature | IEnumerable | IEnumerator | IQueryable |
| :---- | :---- | :---- | :---- |
| Purpose | Forward-only cursor | Interface for iterating | Out-of-memory data sources |
| Execution | In-memory | Collection iteration | Database queries |
| Filtering | Client-side | N/A | Server-side |
| Performance | Load all data | N/A | Load filtered data |

- **IEnumerable**: Forward-only cursor, in-memory filtering  
- **IEnumerator**: Interface for iterating through collection  
- **IQueryable**: Designed for out-of-memory data sources (like databases), supports expression trees

### Common Collection Classes

- \*\*List\*\*: Dynamic array  
- **Dictionary\<TKey, TValue\>**: Key-value pairs  
- \*\*HashSet\*\*: Unique elements  
- \*\*Queue\*\*: FIFO  
- \*\*Stack\*\*: LIFO

### Non-Generic Collections (Legacy)

- **ArrayList**: Object-based, requires boxing  
- **Hashtable**: Key-value pairs with object types

### Immutable Types

- **Immutable**: objects whose state cannot be modified after creation  
- **Advantages**: Thread safety, predictable behavior  
- **Disadvantages**: Performance overhead for frequent modifications

---

## Delegates, Events & Lambda Expressions

### Delegates

- Type-safe function pointers  
- Hold reference to methods with specific signatures  
- Can invoke methods through delegates

Event \<----\>(through delegates pipeline) \<----\> Event Handler (Event Listener)

#### Custom Delegate

public delegate void WorkPerformedHandler(int hours, WorkType workType);

#### Types of Delegates

1. **Single Cast Delegate**: invoking single method  
2. **Multicast Delegate**: invoke multiple methods

#### Generic Delegates

| Delegate | Parameters | Return Type | Use Case |
| :---- | :---- | :---- | :---- |
| Func | 0–16 | Required | When a method returns a value |
| Action | 0–16 | void | When a method performs an action but returns nothing |
| Predicate | 1 | bool | When a method returns a boolean (often used for filtering) |

Func\<int, int, int\> add = (a, b) =\> a \+ b;

Console.WriteLine(add(3, 4)); // Output: 7

Action\<string\> greet = name =\> Console.WriteLine($"Hello, {name}\!");

greet("Alice"); // Output: Hello, Alice\!

Predicate\<string\> isLong = s =\> s.Length \> 5;

Console.WriteLine(isLong("Copilot")); // Output: True

### Events

- **EventArgs**: containers for passing data  
- **Event Handler**: method responsible for receiving and processing data from delegate  
- Has two parameters: Sender (object) and EventArgs object

this.button1.Click \+= new System.EventHandler(this.button1\_Click);  //delegate 

private void button1\_Click(object sender, EventArgs e){            //Event handler 

    // Handle event

}

### Anonymous Methods

#### **Interview Perspective: Anonymous Methods**

**Core Concept**: Anonymous methods were introduced in C# 2.0 as a way to create inline delegate instances without naming the method. They were the precursor to lambda expressions.

#### **Anonymous Methods vs Lambda Expressions**

```csharp
// Anonymous Method (C# 2.0 style)
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

var evensOld = numbers.FindAll(delegate(int number) {
    return number % 2 == 0;
});

// Lambda Expression (C# 3.0+ style - preferred)
var evensNew = numbers.Where(n => n % 2 == 0);
```

#### **Anonymous Method Syntax and Features**

```csharp
// Basic anonymous method
delegate void MyDelegate(string message);

MyDelegate del = delegate(string msg) {
    Console.WriteLine(msg);
};

// Anonymous method with no parameters
delegate void SimpleDelegate();

SimpleDelegate simpleDel = delegate {
    Console.WriteLine("No parameters!");
};

// Anonymous method accessing outer variables (closure)
string message = "Hello";
MyDelegate closureDel = delegate(string msg) {
    Console.WriteLine($"{message} {msg}"); // Captures outer variable
};
```

#### **Key Characteristics**

1. **No Method Name**: Defined inline using `delegate` keyword
2. **Parameter List**: Optional if no parameters needed
3. **Closure Support**: Can access outer variables
4. **Delegate Compatibility**: Can be assigned to any compatible delegate

#### **Common Use Cases**

```csharp
// Event handling
button.Click += delegate(object sender, EventArgs e) {
    MessageBox.Show("Button clicked!");
};

// Thread start
Thread thread = new Thread(delegate() {
    Console.WriteLine("Thread running");
});
thread.Start();

// Collection operations
List<string> names = new List<string> { "Alice", "Bob", "Charlie" };
var shortNames = names.FindAll(delegate(string name) {
    return name.Length <= 4;
});
```

#### **Interview Questions**

1. **Q: What is the difference between anonymous methods and lambda expressions?**
   - Anonymous methods: `delegate keyword`, more verbose
   - Lambda expressions: `=> operator`, more concise, preferred
   - Both can create closures and access outer variables

2. **Q: When would you still use anonymous methods over lambdas?**
   - When you need to omit parameter list completely
   - Legacy codebases (C# 2.0)
   - Specific delegate type inference scenarios

3. **Q: Can anonymous methods access outer variables?**
   - Yes, they create closures like lambda expressions
   - Outer variables are captured by reference

4. **Q: What is the performance difference?**
   - Generally similar performance
   - Lambda expressions may have slight compiler optimizations
   - Difference is usually negligible

#### **Evolution Path**

```csharp
// 1. Named method
bool IsEven(int number) { return number % 2 == 0; }
var result1 = numbers.FindAll(IsEven);

// 2. Anonymous method (C# 2.0)
var result2 = numbers.FindAll(delegate(int number) { 
    return number % 2 == 0; 
});

// 3. Lambda expression (C# 3.0+ - preferred)
var result3 = numbers.Where(n => n % 2 == 0);
```

#### **Best Practices**

- Prefer lambda expressions for new code
- Use anonymous methods only when specifically needed
- Understand both for legacy code maintenance
- Consider readability and team familiarity


### Lambda Expressions

- Modern, concise way to define methods without names  
- Preferred for delegates, events, or LINQ queries

List\<int\> numbers = new List\<int\> { 1, 2, 3, 4, 5 };

// Using lambda to filter even numbers

var evens = numbers.Where(n =\> n % 2 == 0);

// Using anonymous method (older style)

var odds = numbers.Where(delegate(int n) {

    return n % 2 \!= 0;

});

---

## Exception Handling

### Custom Exceptions

// Creating our own Exception Class by inheriting Exception class

public class OddNumberException : Exception

{

    // Overriding the Message property

    public override string Message

    {

        get

        {

            return "Divisor Cannot be Odd Number";

        }

    }

}

// Usage

throw new OddNumberException();

### Exception Handling Best Practices

- Use specific exception types  
- Handle exceptions at appropriate levels  
- Use finally blocks for cleanup  
- Log exceptions for debugging

### throw vs throw ex

- **throw**: Preserves original stack trace  
- **throw ex**: Resets stack trace to current location

---

## File I/O & Serialization

### Common File Operations

// Read all text

string content = File.ReadAllText("path.txt");

// Write all text

File.WriteAllText("path.txt", "content");

// Read lines

string\[\] lines = File.ReadAllLines("path.txt");

// Stream operations

using (StreamReader reader = new StreamReader("path.txt"))

{

    string line;

    while ((line = reader.ReadLine()) \!= null)

    {

        Console.WriteLine(line);

    }

}

### Directory Operations

// Create directory

Directory.CreateDirectory("path");

// Check if exists

bool exists = Directory.Exists("path");

// Get files

string\[\] files = Directory.GetFiles("path");

### Serialization and Deserialization

- **JSON Serialization**: Convert objects to JSON strings and back  
- **Binary Serialization**: Convert objects to binary format  
- **XML Serialization**: Convert objects to XML format

// JSON serialization example

string json = JsonSerializer.Serialize(obj);

MyClass obj = JsonSerializer.Deserialize\<MyClass\>(json);

---

## Multi-Threading & Asynchronous Programming

### Thread vs Async Await

- **Thread**: Physical OS thread, more resource-intensive  
- **Async/Await**: Cooperative multitasking, more efficient for I/O operations

### Task Parallel Library (TPL)

// Creating and running tasks

Task task = Task.Run(() =\> {

    // Background work

});

// Waiting for completion

await task;

// Multiple tasks

var tasks = new List\<Task\> {

    Task.Run(() =\> Method1()),

    Task.Run(() =\> Method2())

};

await Task.WhenAll(tasks);

// Parallel.For

Parallel.For(0, 100, i =\> {

    ProcessItem(i);

});

// Parallel.ForEach

Parallel.ForEach(items, item =\> {

    ProcessItem(item);

});

### async/await Pattern

public async Task\<string\> GetDataAsync()

{

    var result = await httpClient.GetStringAsync(url);

    return result;

}

### Thread Safety

- Lock statements  
- Monitor class  
- SemaphoreSlim  
- Concurrent collections

---

## Design Patterns

### Design Pattern Categories

- **Creational Patterns**: Object creation mechanisms  
- **Structural Patterns**: Class and object composition  
- **Behavioral Patterns**: Communication between objects

### Gang of Four Patterns

#### Creational Patterns

- **Abstract Factory**: Create families of related objects  
- **Factory Method**: Create objects without specifying exact class  
- **Singleton**: Ensure only one instance exists  
- **Builder**: Construct complex objects step by step  
- **Prototype**: Create new objects by copying existing ones

#### Structural Patterns

- **Adapter**: Convert interface of one class to another  
- **Bridge**: Decouple abstraction from implementation  
- **Composite**: Compose objects into tree structures  
- **Decorator**: Add new functionality to objects dynamically  
- **Facade**: Provide simplified interface to complex system  
- **Flyweight**: Reduce memory usage by sharing objects  
- **Proxy**: Provide surrogate or placeholder for another object

#### Behavioral Patterns

- **Chain of Responsibility**: Pass request along chain of handlers  
- **Command**: Encapsulate request as an object  
- **Iterator**: Provide sequential access to elements  
- **Mediator**: Define centralized communication between objects  
- **Memento**: Capture and restore object state  
- **Observer**: Define one-to-many dependency between objects  
- **State**: Allow object to change behavior when state changes  
- **Strategy**: Define family of algorithms, encapsulate each one  
- **Template Method**: Define skeleton of algorithm, let subclasses fill in steps  
- **Visitor**: Add new operations to existing class structures  
- **Interpreter**: Define grammar for language interpretation

### Common Design Principles

#### DRY (Don't Repeat Yourself)

- **Principle**: Every piece of knowledge must have a single, unambiguous, authoritative representation within a system  
- **Benefits**: Reduces code duplication, easier maintenance, consistent behavior  
- **Implementation**: Extract common functionality into reusable methods/classes

#### KISS (Keep It Simple, Stupid)

- **Principle**: Most systems work best if they are kept simple rather than made complicated  
- **Benefits**: Easier to understand, maintain, and debug  
- **Implementation**: Avoid unnecessary complexity, choose simple solutions over complex ones

#### MVC (Model-View-Controller)

- **Pattern**: Separates application into three interconnected components  
  - **Model**: Data and business logic  
  - **View**: User interface representation  
  - **Controller**: Handles user input and coordinates Model and View  
- **Benefits**: Separation of concerns, testability, parallel development  
- **Usage**: Web applications, desktop applications, mobile apps

### Factory vs Abstract Factory

- **Factory**: Creates single product  
- **Abstract Factory**: Creates families of related products

### Singleton Implementation

public sealed class Singleton

{

    private static readonly Lazy\<Singleton\> \_instance = 

        new Lazy\<Singleton\>(() =\> new Singleton());

    

    public static Singleton Instance =\> \_instance.Value;

    

    private Singleton() { }

}

### Builder Pattern

public class ProductBuilder

{

    private Product \_product = new Product();

    

    public ProductBuilder WithName(string name)

    {

        \_product.Name = name;

        return this;

    }

    

    public ProductBuilder WithPrice(decimal price)

    {

        \_product.Price = price;

        return this;

    }

    

    public Product Build() =\> \_product;

}

### Repository Pattern

public interface IRepository\<T\> where T : class

{

    Task\<T\> GetByIdAsync(int id);

    Task\<IEnumerable\<T\>\> GetAllAsync();

    Task AddAsync(T entity);

    Task UpdateAsync(T entity);

    Task DeleteAsync(T entity);

}

public class Repository\<T\> : IRepository\<T\> where T : class

{

    private readonly DbContext \_context;

    private readonly DbSet\<T\> \_dbSet;

    public Repository(DbContext context)

    {

        \_context = context;

        \_dbSet = context.Set\<T\>();

    }

    public async Task\<T\> GetByIdAsync(int id)

    {

        return await \_dbSet.FindAsync(id);

    }

    public async Task\<IEnumerable\<T\>\> GetAllAsync()

    {

        return await \_dbSet.ToListAsync();

    }

    public async Task AddAsync(T entity)

    {

        await \_dbSet.AddAsync(entity);

        await \_context.SaveChangesAsync();

    }

    public async Task UpdateAsync(T entity)

    {

        \_dbSet.Update(entity);

        await \_context.SaveChangesAsync();

    }

    public async Task DeleteAsync(T entity)

    {

        \_dbSet.Remove(entity);

        await \_context.SaveChangesAsync();

    }

}

### Unit of Work Pattern

public interface IUnitOfWork : IDisposable

{

    IRepository\<T\> Repository\<T\>() where T : class;

    Task\<int\> SaveChangesAsync();

}

public class UnitOfWork : IUnitOfWork

{

    private readonly DbContext \_context;

    private readonly Dictionary\<Type, object\> \_repositories = new Dictionary\<Type, object\>();

    public UnitOfWork(DbContext context)

    {

        \_context = context;

    }

    public IRepository\<T\> Repository\<T\>() where T : class

    {

        if (\_repositories.ContainsKey(typeof(T)))

        {

            return \_repositories\[typeof(T)\] as IRepository\<T\>;

        }

        var repository = new Repository\<T\>(\_context);

        \_repositories.Add(typeof(T), repository);

        return repository;

    }

    public async Task\<int\> SaveChangesAsync()

    {

        return await \_context.SaveChangesAsync();

    }

    public void Dispose()

    {

        \_context.Dispose();

    }

}

---

## SOLID Principles

### Single Responsibility Principle (SRP)

- A class should have only one reason to change

### Open/Closed Principle (OCP)

- Software entities should be open for extension, closed for modification

### Liskov Substitution Principle (LSP)

- Derived classes must be substitutable for their base classes

### Interface Segregation Principle (ISP)

- Clients should not be forced to depend on interfaces they don't use

### Dependency Inversion Principle (DIP)

- High-level modules should not depend on low-level modules; both should depend on abstractions

---

## Caching Strategies

### In-Memory Cache

- **Description**: Stores data in the application's memory  
- **Best suited for**: Small/medium, not for distributed datasets  
- **Default caching strategy** for most ASP.NET Core projects  
- Each server has their own in memory cache  
- Uses IMemoryCache
- Package: `Microsoft.Extensions.Caching.Memory` (ASP.NET Core)

// Program.cs

builder.Services.AddMemoryCache();

builder.Services.AddSingleton\<ICacheService, MemoryCacheService\>();

// MemoryCacheService.cs

public class MemoryCacheService : ICacheService

{

    private readonly IMemoryCache \_memoryCache;

    private readonly IConfiguration \_config;

    public MemoryCacheService(IMemoryCache memoryCache, IConfiguration config)

    {

        \_memoryCache = memoryCache;

        \_config = config;

    }

    public T Get\<T\>(string key)

    {

        if (\_memoryCache.TryGetValue(key, out T value))

        {

            return value;

        }

        return default;

    }

    public void Set\<T\>(string key, T value, TimeSpan? expirationTime = null)

    {

        var options = new MemoryCacheEntryOptions();

        if (expirationTime.HasValue)

        {

            options.SetAbsoluteExpiration(expirationTime.Value);

        }

        else

        {

            var mins = \_config\["ColumnFilterCacheExpiry"\];

            if (int.TryParse(mins, out int expiryMins) && expiryMins \> 0\)

            {

                options.SetAbsoluteExpiration(TimeSpan.FromMinutes(expiryMins));

            }

            else

            {

                options.SetAbsoluteExpiration(TimeSpan.FromMinutes(30));

            }

        }

        options.SetPriority(CacheItemPriority.High);

        \_memoryCache.Set(key, value, options);

    }

}

### Distributed Caching

- **Description**: Stores cache data outside the application's process, often in a shared location  
- **Common Use**: Useful for web farms or cloud applications  
- **Examples**: Redis, SQL Server, NCache  
- **.NET Example**: IDistributedCache interface
- Optionally, register your cache service to use `IDistributedCache` instead of `IMemoryCache`

```csharp
public class MyService
{
    private readonly IDistributedCache _cache;
    public MyService(IDistributedCache cache) => _cache = cache;

    public async Task SetCacheAsync(string key, string value)
    {
        await _cache.SetStringAsync(key, value);
    }
}
```

API Flow

```
Client
  |
  v
API Controller/Service
  |
  v
Distributed Cache (Redis/SQL Server)
```

#### Redis Implementation

// Install package

dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis

// Program.cs

builder.Services.AddStackExchangeRedisCache(options =\>

{

    options.Configuration = builder.Configuration.GetConnectionString("RedisConnection");

    options.InstanceName = "Test:";

});

// appsettings.json

"ConnectionStrings": {

    "RedisConnection": "localhost:6379"

}

Replace with your Redis server address.

// DistributedCacheService.cs

public class DistributedCacheService : ICacheService

{

    private readonly IDistributedCache \_cache;

    public DistributedCacheService(IDistributedCache cache) =\> \_cache = cache;

    

    public async Task SetAsync(string key, string value, TimeSpan? expiry = null)

    {

        var options = new DistributedCacheEntryOptions();

        if (expiry.HasValue)

            options.AbsoluteExpirationRelativeToNow = expiry.Value;

        await \_cache.SetStringAsync(key, value, options);

    }

    public async Task\<string?\> GetAsync(string key)

    {

        return await \_cache.GetStringAsync(key);

    }

}

#### Redis Distribution Configuration

- **Redis Clustering**: Distribute data across multiple Redis nodes (shards), providing horizontal scaling and fault tolerance  
- **Redis Sentinel**: High availability solution with automatic failover

To configure distribution (scalability and high availability) in Redis, you typically use Redis clustering or Redis Sentinel.

**Redis Clustering**

Redis Cluster allows you to distribute data across multiple Redis nodes (shards), providing horizontal scaling and fault tolerance.
- Deploy multiple Redis nodes (at least 6 for production: 3 masters, 3 replicas).
- Configure each node with a unique port and cluster settings in `redis.conf`.

**Redis Sentinel**

- Deploy multiple Redis instances (1 master, 1+ replicas).
- Deploy 3 Sentinel processes.
- Configure Sentinel and point it to master.
- Sentinel will monitor the master and promote a replica if the master fails.

#### Distributed Cache Hit/Miss Flow

```
Client
  |
  v
API Controller/Service
  |
  v
Distributed Cache (Redis/SQL Server)
        (hit) \ (miss)
         v      v
      Return   Database/Other Source
      Data         |
                  v
             Store in Cache
                  |
                  v
              Return Data
```

```csharp
public class MyService
{
    private readonly IDistributedCache _cache;
    public MyService(IDistributedCache cache) => _cache = cache;

    public async Task<string> GetDataAsync(string key)
    {
        var cached = await _cache.GetStringAsync(key);
        if (cached != null)
            return cached;

        // Simulate data fetch (e.g., from DB)
        var data = "fetched from database";

        await _cache.SetStringAsync(key, data, new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
        });

        return data;
    }
}

```

### Response Caching

- **Description**: Caches HTTP responses to reduce server load and improve performance  
- **Common Use**: Web APIs and ASP.NET Core MVC  
- **Example**: \[ResponseCache\] attribute in ASP.NET Core

```csharp
\[ResponseCache(Duration = 60)\]

public IActionResult Get()

{

    //......

}

// In Program.cs

builder.Services.AddResponseCaching();

app.UseResponseCaching();
```
### Output Caching

- **Description**: Caches the output of controller actions  
- **Common Use**: ASP.NET MVC and Web Forms  
- **Example**: \[OutputCache\] attribute (ASP.NET MVC)

```csharp
\[OutputCache(Duration = 60)\]

public ActionResult Index()

{

    //....

}
```
**Key Differences:**

- **Output Caching**: Used in older .NET, done on server side  
- **Response Caching**: Used in .NET Core, done on browser side

#### MVC Lifecycle

1. **Request**: User makes request  
2. **Routing**: Route table determines controller/action  
3. **Controller**: Controller instantiated, action executed  
4. **Model**: Data preparation  
5. **View**: Response rendered  
6. **Response**: Sent to client

#### Key Components

- **Controller**: Handles user input, interacts with model  
- **View**: Renders UI  
- **Model**: Represents data and business logic

#### Data Passing

- **Controller to View**: ViewBag, ViewData, TempData, Model  
- **View to Controller**: Form collection, route parameters, query strings

#### TempData

- **Peek**: Retrieve value without removing it  
- **Keep**: Preserve value for next request

#### Filters

- **Authorization Filters**: Authentication and authorization  
- **Action Filters**: Before/after action execution  
- **Result Filters**: Before/after result execution  
- **Exception Filters**: Handle unhandled exceptions

#### Custom Filters

public class CustomActionFilter : ActionFilterAttribute

{

    public override void OnActionExecuting(ActionExecutingContext context)

    {

        // Before action execution

        base.OnActionExecuting(context);

    }

    

    public override void OnActionExecuted(ActionExecutedContext context)

    {

        // After action execution

        base.OnActionExecuted(context);

    }

}

### Routing

#### Convention Routing vs Attribute Routing

- **Convention Routing**: Defined in RouteConfig  
- **Attribute Routing**: Defined on controllers/actions with attributes

#### Route Configuration

// Convention routing

routes.MapRoute(

    name: "Default",

    url: "{controller}/{action}/{id}",

    defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }

);

// Attribute routing

\[Route("api/\[controller\]")\]

public class ProductsController : Controller

{

    \[HttpGet("{id}")\]

    public IActionResult GetProduct(int id)

    {

        // Implementation

    }

}

### Areas

- Organize large applications into functional modules  
- Each area has its own controllers, views, and models

### Exception Handling

- **OnException**: Handle exceptions at controller level  
- **HandleError Attribute**: Global exception handling  
- **Application\_Error Event**: Application-level error handling

### State Management

- **Session**: Per-user data storage  
- **Application**: Application-wide data storage  
- **Cache**: Performance optimization

### Anti-Forgery Tokens

@Html.AntiForgeryToken()

// In controller

\[ValidateAntiForgeryToken\]

public ActionResult PostAction(FormCollection form)

{

    // Implementation

}

### Web API Security

- **Authentication**: Verify identity  
- **Authorization**: Verify permissions  
- **OWIN Package**: Security middleware

### HTTP Methods

- **GET**: Retrieve data  
- **POST**: Create data  
- **PUT**: Update entire resource  
- **PATCH**: Partial update  
- **DELETE**: Remove resource

### REST vs gRPC

| Feature | REST (HTTP/1.1) | gRPC (HTTP/2) |
| :---- | :---- | :---- |
| Data Format | Text (JSON/XML) - bulky | Binary (Protobuf) - tiny and fast |
| Payload Size | Large (includes keys) | Small (uses numeric tags) |
| Multiplexing | Requests are sequential (blocking) | Parallel (multiple requests on 1 connection) |
| Streaming | Client-to-Server only | Bi-directional (real-time flow) |
| Contract | Optional (Swagger/OpenAPI) | Strict (Defined in .proto files) |

---
## Testing & Development Practices

### Testing Frameworks

- **MSTest**: Microsoft's testing framework  
- **NUnit**: Open-source testing framework  
- **xUnit**: Modern, extensible testing framework

### Test-Driven Development (TDD)

1. **Red**: Write failing test  
2. **Green**: Write minimal code to pass test  
3. **Refactor**: Improve code while keeping tests green

### Assertion Functions

// Example assertion signature

Assert.AreEqual(expected, actual);

Assert.IsTrue(condition);

Assert.IsNotNull(object);

### EF In-Memory Databases

- Useful for unit testing database operations  
- Fast, isolated test environment

// Setup

var options = new DbContextOptionsBuilder\<MyDbContext\>()

    .UseInMemoryDatabase(databaseName: "TestDb")

    .Options;

using (var context = new MyDbContext(options))

{

    // Arrange & Act

    var service = new MyService(context);

    var result = await service.GetDataAsync();

    

    // Assert

    Assert.NotNull(result);

}

### Integration Testing

- Test multiple components working together  
- Use test databases or mock services

### Unit Testing Frameworks

- **MSTest**: Microsoft's testing framework  
- **NUnit**: Open-source testing framework  
- **xUnit**: Free, open-source, community-focused

---

## Performance Optimization

### LINQ Performance

- **Deferred Execution**: Queries execute when enumerated  
- **Immediate Execution**: Use ToList(), ToArray() to execute immediately  
- **Optimization**: Use appropriate methods, avoid N+1 queries

### Memory Management

- **Garbage Collection**: Automatic memory management  
- **IDisposable**: Proper cleanup of unmanaged resources  
- **Using Statement**: Ensure resource disposal

### Async Programming Benefits

- **Responsiveness**: UI remains responsive during I/O operations  
- **Scalability**: Better resource utilization  
- **Performance**: More efficient than blocking calls

### Caching Strategies

- **In-Memory Cache**: Fast, single-server  
- **Distributed Cache**: Scalable, multi-server  
- **Response Caching**: Reduce server load

### Performance Tips

1. **Use `StringBuilder`** for string concatenation in loops  
2. **Prefer `List<T>`** over `ArrayList` for type safety  
3. **Use `yield return`** for custom iterators  
4. **Implement `IDisposable`** for unmanaged resources  
5. **Use `async/await`** for I/O operations  
6. **Cache frequently accessed data**  
7. **Use appropriate collections** for specific scenarios  
8. **Avoid boxing/unboxing** with generics  
9. **Profile before optimizing**  
10. **Use connection pooling** for database operations

---

## Deployment & DevOps

### Git Version Control

- **Undo Changes**: `git checkout -- file` or `git reset --hard HEAD`  
- **Branching**: Feature branches, release branches  
- **Merging**: Combine changes from different branches

### Deployment Pipeline

1. **Source Control**: Git repository  
2. **Build**: Compile code, run tests  
3. **Package**: Create deployment artifacts  
4. **Deploy**: Push to various environments  
5. **Monitor**: Track application health

### Cloud Services

#### Azure Benefits

- **Scalability**: Easy scaling up/down  
- **Reliability**: High availability, backup services  
- **Managed Services**: Reduced maintenance overhead  
- **Integration**: Seamless integration with Microsoft tools

#### Cloud Computing Models

- **IaaS**: Infrastructure as a Service  
- **PaaS**: Platform as a Service  
- **SaaS**: Software as a Service

### Docker Containerization

- **Benefits**: Consistent environments, easy deployment  
- **Dockerfile**: Instructions for building image  
- **Docker Compose**: Multi-container applications

### CI/CD Practices

- **Continuous Integration**: Automated builds and tests  
- **Continuous Deployment**: Automated releases  
- **Infrastructure as Code**: Manage infrastructure with code

---

## Search Algorithms

### Basic Search Algorithms

#### 1\. Linear Search

- **Description**: Checks each element sequentially  
- **Time Complexity**: O(n)  
- **Use Case**: Unsorted lists or small datasets

#### 2\. Binary Search

- **Description**: Divides search space in half repeatedly  
- **Time Complexity**: O(log n)  
- **Use Case**: Large, sorted datasets

#### 3\. Depth-First Search (DFS)

- **Description**: Explores as far as possible along branches  
- **Use Case**: Graph/tree traversal, maze-solving

#### 4\. Breadth-First Search (BFS)

- **Description**: Explores all neighbors at current depth  
- **Use Case**: Finding shortest paths

### Advanced Search Algorithms

#### 5\. Two Pointers Technique

- **Description**: Uses two pointers moving coordinately  
- **Use Case**: Pair-finding problems

#### 6\. Interpolation Search

- **Description**: Estimates position based on value distribution  
- **Use Case**: Uniformly distributed data

#### 7\. Exponential Search

- **Description**: Binary search variant for unbounded arrays  
- **Use Case**: Unknown array size

#### 8\. Jump Search

- **Description**: Block-based searching for ordered lists  
- **Use Case**: Balances linear and binary search

#### 9\. Fibonacci Search

- **Description**: Uses Fibonacci numbers for division  
- **Use Case**: Fewer comparisons than binary search

### AI Search Algorithms

#### k-Nearest Neighbors (kNN)

- **Description**: Finds closest data points based on similarity  
- **Use Case**: Recommendation systems, classification

#### A\* Search

- **Description**: Combines BFS and DFS with heuristics  
- **Use Case**: Pathfinding, games

### Google Search Algorithm

The Google Search algorithm is a complex system used to rank web pages based on their relevance to a user's query. It involves:

1. **Crawling**: Discovering web pages  
2. **Indexing**: Analyzing and categorizing content  
3. **Ranking**: Displaying pages based on relevance and other factors like backlinks and page speed

---

## Interview Questions

### C\# Fundamentals

- What is the difference between `ref` and `out` keywords?  
- Explain boxing and unboxing with examples  
- What is the difference between `string` and `StringBuilder`?  
- What are access modifiers in C\#?  
- Explain the difference between `abstract class` and `interface`  
- What is the difference between `var` and `dynamic`?  
- Explain the difference between `const` and `readonly`?  
- What is the difference between `==` and `Equals()`?  
- What is garbage collection?  
- Explain exception handling best practices

### OOP Concepts

- What are the four principles of OOP?  
- Explain method overloading vs method overriding  
- What is method hiding?  
- Explain encapsulation vs abstraction  
- What is polymorphism and its types?  
- What is the difference between a class and an object?  
- Explain constructor types in C\#  
- What is a static class and when to use it?

### ASP.NET & Web Development

- What is the MVC request lifecycle?  
- Explain the difference between ViewBag, ViewData, and TempData  
- What are MVC filters and their types?  
- What is the difference between PUT and PATCH?  
- How does authentication work in Web API?  
- What is the difference between WebForms and MVC?  
- What is routing in ASP.NET?  
- Explain TempData Peek vs Keep

### Database & Data Access

- What is the difference between stored procedures and functions?  
- Explain the difference between `IEnumerable` and `IQueryable`  
- What is Entity Framework and its approaches?  
- How do you optimize database queries?  
- What is the difference between DataSet and DataReader?  
- What is the difference between Code First and Database First?

### Advanced Concepts

- What is dependency injection and its benefits?  
- Explain SOLID principles with examples  
- What are design patterns and give examples  
- What is the difference between async and multithreading?  
- Explain the Circuit Breaker pattern  
- What is reflection and when to use it?  
- Explain delegates and events  
- What are generic delegates (Func, Action, Predicate)?

### Modern .NET

- What is the difference between .NET Core and .NET Framework?  
- Explain microservices architecture  
- What is gRPC and when to use it?  
- What is Polly and its resilience patterns?  
- Explain rate limiting and its implementation  
- What is Docker and containerization?  
- Explain API Gateway and its benefits

### Performance & Optimization

- What are caching strategies in .NET?  
- How do you implement distributed caching?  
- What is the difference between in-memory and distributed cache?  
- Explain rate limiting algorithms  
- What is an API Gateway and its benefits?  
- What is the difference between Stack and Heap?

### General Programming

- Explain the difference between Stack and Heap  
- What is garbage collection?  
- Explain the difference between `==` and `Equals()`  
- What is the difference between `const` and `readonly`?  
- Explain exception handling best practices  
- What is the difference between process and thread?  
- Explain the difference between interface and abstract class

---

## Quick Reference

### Common NuGet Packages

\# Caching

dotnet add package Microsoft.Extensions.Caching.Memory

dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis

\# Resilience

dotnet add package Microsoft.Extensions.Http.Resilience

dotnet add package Polly

\# API Gateway

dotnet add package Yarp.ReverseProxy

dotnet add package Ocelot

\# AutoMapper

dotnet add package AutoMapper

dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection

\# Entity Framework

dotnet add package Microsoft.EntityFrameworkCore

dotnet add package Microsoft.EntityFrameworkCore.SqlServer

dotnet add package Microsoft.EntityFrameworkCore.InMemory

### Common Using Statements

using Microsoft.Extensions.Caching.Memory;

using Microsoft.Extensions.Caching.Distributed;

using Microsoft.AspNetCore.Mvc;

using Microsoft.EntityFrameworkCore;

using AutoMapper;

using System.Threading.Tasks;

using System.Linq;

### Configuration Templates

// appsettings.json

{

  "ConnectionStrings": {

    "DefaultConnection": "Server=(localdb)\\\\mssqllocaldb;Database=MyDb;",

    "RedisConnection": "localhost:6379"

  },

  "ReverseProxy": {

    "Routes": {},

    "Clusters": {}

  }

}

### C\# Keywords Summary

| Keyword | Purpose | Example |
| :---- | :---- | :---- |
| `var` | Implicit typing | `var x = 5;` |
| `dynamic` | Runtime typing | `dynamic x = GetObject();` |
| `ref` | Pass by reference | `void Method(ref int x)` |
| `out` | Output parameter | `void Method(out int x)` |
| `this` | Current instance | `this.Property = value;` |
| `base` | Base class | `base.Method();` |
| `sealed` | Prevent inheritance | `sealed class MyClass` |
| `abstract` | Must be inherited | `abstract class MyClass` |
| `virtual` | Can be overridden | `virtual void Method()` |
| `override` | Override base method | `override void Method()` |
| `static` | Class-level member | `static int Count;` |
| `readonly` | Runtime constant | `readonly int Value;` |
| `const` | Compile-time constant | `const int Max = 100;` |
| `async` | Asynchronous method | `async Task Method()` |
| `await` | Wait for async | `var result = await Method();` |
| `yield` | Iterator support | `yield return item;` |
| `typeof` | Get type | `Type t = typeof(string);` |
| `is` | Type checking | `if (obj is string)` |
| `as` | Safe casting | `var s = obj as string;` |
| `checked` | Overflow checking | `checked(x + y)` |
| `unchecked` | No overflow check | `unchecked(x + y)` |
| `lock` | Thread synchronization | `lock(obj) { ... }` |
| `volatile` | Thread-safe field | `volatile int flag;` |
| `unsafe` | Pointer code | `unsafe void Method()` |
| `fixed` | Pin managed object | `fixed(int* p = &array[0])` |

### Common Design Patterns Quick Reference

| Pattern | Purpose | When to Use |
| :---- | :---- | :---- |
| **Singleton** | One instance only | Configuration managers, logging |
| **Factory** | Object creation | When creation logic is complex |
| **Observer** | Event notification | UI updates, event systems |
| **Strategy** | Algorithm selection | When multiple algorithms exist |
| **Command** | Encapsulate request | Undo/redo, macro operations |
| **Adapter** | Interface conversion | Legacy system integration |
| **Decorator** | Add functionality | Dynamic feature addition |
| **Facade** | Simplify interface | Complex subsystems |
| **Repository** | Data access abstraction | Database operations |
| **Unit of Work** | Transaction management | Multiple repository operations |

### HTTP Status Codes

- **2xx**: Success (200, 201, 204\)  
- **3xx**: Redirection (301, 302\)  
- **4xx**: Client errors (400, 401, 404, 429\)  
- **5xx**: Server errors (500, 502, 503\)

### Development Methodologies

- **Waterfall**: Sequential, linear development  
- **Agile**: Iterative, flexible approach  
- **Scrum**: Agile framework with sprints  
- **Kanban**: Visual workflow management

---

*This comprehensive study guide covers all major C\# and .NET concepts from basic fundamentals to advanced topics like microservices, caching strategies, and modern development practices. Use this as a reference for interview preparation, project development, or learning new concepts.*

### Basic Concepts

#### Fields vs Properties vs Auto-Implemented Properties

- **Fields**: Private variables that store data  
- **Properties**: Public accessors with get/set methods  
- **Auto-Implemented Properties**: `{ get; set; }` - compiler creates backing field automatically

#### Constructors

- Can have private, protected, public access modifiers  
- Only one parameterless constructor per class  
- Cannot return values  
- Default parameterless constructor created automatically if none specified

#### Namespace

- Clubs related code together  
- Avoids naming conflicts  
- Organizes code into logical groups

#### Variable Declaration

- **Explicit**: `int a = 5;`  
- **Implicit**: `var a = 5;` (must be initialized before using)  
- `var` cannot be used as function input parameter  
- `var` can be used in foreach loops

#### AOT vs JIT Compilation

When code runs on machine, method calls trigger CLR to get method to JIT compiler from IL/dll and convert to machine code.

**AOT (Ahead-of-Time)**: IL to machine code conversion happens at build time only, so AOT apps are faster. **AOT Limitations**: Limited reflection and dynamic features support, increased build times, large native executables.

#### Variables and Types

- Variables are case-sensitive and strongly typed  
- `int i = default(int); // 0`  
- `float f = default(float); // 0`

#### String Handling

- `\` is used as escape characters for strings  
- Use `@` symbol for verbatim strings: `string path = @"test@gmail"".com"`  
- String interpolation: `string fullName = $"Mr. {firstName} {lastName}, Code: {code}";`

#### Stack vs Heap Memory

STACK                                 HEAP

--------------------------------     -------------------------------------

| int x = 42            |           |                                     |

|----------------------|           |                                     |

| string name ---------|----------\>|  "Alice" (immutable string object)  |

|                      |           |                                     |

| Person p ------------|----------\>|  Person object                      |

|                      |           |   ├─ Name: "Bob"                    |

|                      |           |                                     |

--------------------------------     -------------------------------------

When Example() finishes, the stack frame (with x, name, and p) is cleared. The heap memory for "Alice" and the Person object remains until garbage-collected—if nothing references them anymore.

#### DateTime Operations

DateTime currentDateTime = DateTime.Now;      // Returns current date and time

DateTime todaysDate = DateTime.Today;         // Returns today's date

DateTime currentDateTimeUTC = DateTime.UtcNow; // Returns current UTC date and time

// Parsing dates safely

var str = "5/12/2020";

DateTime dt;

var isValidDate = DateTime.TryParse(str, out dt);

if(isValidDate)

    Console.WriteLine(dt);

else

    Console.WriteLine($"{str} is not a valid date string");

#### Enumerations

- Enumeration type assigns constant names to numeric integer values  
- Makes constant values more readable  
- Example: `enumWeekDays.Monday`

#### StringBuilder

using System.Text; // include at the top       

StringBuilder sb = new StringBuilder(); // string will be appended later

sb.Append("Hello ");

#### Anonymous Types

- Only Public read-only properties, `var` is used to hold values

var student = new { Id = 1, FirstName = "James", LastName = "Bond" };

#### Nullable Types

int? i = null;            

int j = i ?? 0; // null coalescing operator

var result = x \> y ? "x is greater than y" : "x is less than y";

var test = value ?? 0; // if value is there else 0 value

### Type Conversion

| Method | Use Case | Notes |
| :---- | :---- | :---- |
| `(int) cast` | Compatible numeric types | Not valid for strings |
| `Convert.ToInt32` | General-purpose conversion | Handles null gracefully, accepts object/string/bool |
| `int.Parse` | Valid integer strings | Fast but unforgiving, only accepts strings |
| `int.TryParse` | User input/uncertain data | Prevents crashes, handles failures gracefully |

string str = "123";

int a = Convert.ToInt32(str);     // ✅ Works

int b = int.Parse(str);           // ✅ Works

bool success = int.TryParse(str, out int c); // ✅ Works, success = true

string badStr = "abc";

int d = Convert.ToInt32(badStr);  // ❌ Throws FormatException

int e = int.Parse(badStr);        // ❌ Throws FormatException

bool ok = int.TryParse(badStr, out int f); // ✅ ok = false, f = 0

### Control Flow

#### Switch Statement

switch(variable){

    case 1:

        //code 

        break;

    case 2:

        //code 

        break;

    case default:

        break;

}

#### Console I/O

Console.WriteLine("{0} \+ {1} = {2}", number1, number2, sum);

str = Console.ReadLine();

Console.WriteLine($"You entered {str}");

### Keywords and Modifiers

#### Static Class

- Can have static constructor (parameterless, access-modifier less)  
- Cannot inherit or use OOPs concepts  
- All properties and methods are static

#### Fields vs Properties

public class Car

{

    private int speed;  // Backing field

    public int Speed {get; set;}   // Property --\> preferred one

}

#### String Null Handling

- `testStr.ToString()` - will throw NullValueException  
- `Convert.ToString(testStr)` - will not throw error

#### Checked/Unchecked Keywords

- **Checked**: Enable overflow checking for int type arithmetic operations  
- **Unchecked**: Suppress overflow-checking  
- **Overflow**: When value exceeds int range

int.MaxValue = 2147483647

int a = 2147483647

int b = 2147483647

int c = a \+ b // output = -2 (unchecked default behavior)

int c = checked(a+b) // throws error overflow exception 

int c = unchecked(a+b) // output = -2

#### Switch Statement

switch(variable){

    case 1:

        //code 

        break;

    case 2:

        //code 

        break;

    case default:

        break;

}

#### Call by Reference

static void Main(string\[\] args)

{

    int a = 15;

    UpdateValue(ref a);

    Console.WriteLine(a); // prints 30

}

static void UpdateValue(ref int b)  //---\> ref keyword

{

    b = 30;

}

#### Console I/O

Console.WriteLine("{0} \+ {1} = {2}", number1, number2, sum);

str = Console.ReadLine();

Console.WriteLine($"You entered {str}");

#### Stack vs Heap

- **Stack**: Faster, LIFO, for primitive datatype, handled by CPU, lifespan only for function end  
- **Heap**: Slower compared to stack, collected by garbage collector, for reference type variables

#### ILDASM

- In CMD: `ILDASM` - used to see Intermediate Language Code (IL) for any DLL or EXE

### Advanced Keywords

#### Volatile Keyword

- Rarely used in multi-threaded applications  
- Indicates field might be modified by multiple threads executing simultaneously  
- Excludes field from certain optimizations  
- Used for better concurrency handling

#### Named Parameters

employee.AddEmployeeInfo(IsPermanent: true, Name: "Pranaya", EmpID: 1001, Department:"IT", Mobile:"1234567890" );

public void AddEmployeeInfo(long EmpID, string Name, bool IsPermanent, string Mobile, string Department)

#### Boxing and Unboxing

- **Boxing**: Converting value type to reference Object type  
- **Unboxing**: Reference Object type to specific value type conversion  
- Reduces system performance, avoid when possible

int x = 10;

object y = x;    // Boxing 

int z = (int)y;  // Unboxing

#### ILDASM

- In CMD: `ILDASM` - used to see Intermediate Language Code (IL) for any DLL or EXE

---

## Object-Oriented Programming

### OOP Principles

- **Reusability, Extensibility, Simplicity, and Maintainability**

#### 1\. Encapsulation

- Process of defining a class by hiding internal data members from outside the class  
- Access internal data only through publicly exposed methods or properties  
- Also called data hiding

**Access Modifiers:** | Modifier | Accessibility | |----------|---------------| | `Private` | Only within class | | `Public` | Accessible to anyone | | `Protected` | Within class and subclass | | `Internal` | Within same assembly or library | | `Protected Internal` | Within same assembly \+ from derived classes in any assembly | | `Private Protected` | Within same assembly and from derived classes inside same assembly |

#### 2\. Inheritance

- Process of transferring members of one class to another class  
- **Parent/Base/Superclass**: Class from which members are transferred  
- **Derived/Child/Subclass**: Class that inherits Parent class members  
- Achieves code extensibility

#### 3\. Polymorphism

- Ability to take more than one form  
- Same function/operator shows different behaviors with different types/number of values

**Types:**

1. **Static polymorphism/compile-time polymorphism/Early binding**  
2. **Dynamic polymorphism/Runtime polymorphism/Late binding**

#### 4\. Abstraction

- Process of representing essential features without background details  
- **Abstraction vs Encapsulation**: Abstraction is logical thinking, Encapsulation is physical implementation

### Special Classes

#### 1\. Abstract Class

#### 2\. Sealed Class

#### 3\. Partial Class

#### 4\. Static Class

### Constructors

#### General Properties

- Special type of method  
- Called when creating instance of class  
- Used to initialize variables  
- It should not contain a return type even void also
- As part of the constructor body return statemetn with a value is not allowed
- Same name as class  
- Parameterless constructor not created if parameterized constructor exists
- responsible for two things - Object initialization and memory allocation
- Can have exception throws clause

#### Types of constructors - 
##### 1. Default / Parameterless / Implicit constructor:
        - default constructor
        - parameterless constructor
        - initialize variables with default values
        - public 
        - two types - system defined AND user defined
##### 2. Explicit / Parameterized constructor
##### 3. Copy constructor
##### 4. Static constructor 
##### 5. Private constructor

#### Copy Constructor

- Special constructor to create new object by copying values from existing object

```csharp
public class Person
{

    public string Name;
    public int Age;

    // Parameterized constructor
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    // Copy constructor
    public Person(Person other)
    {
        Name = other.Name;
        Age = other.Age;
    }
}

Person original = new Person("Akshay", 30);

Person copy = new Person(original);
```

#### Static Constructor

- Responsible for initializing static variables  
- Called implicitly, first to execute in any class  
- Cannot be parameterized, overloading not possible
- Constructor is declared explicitly by using the static modifier
- there can be only one static constructor in a class
- It can only access the static members of the class
- Triggered by: accessing a static member, calling a static method, or creating the first instance.
- It is the first block of code to run in a class.

```csharp
public class Test{
    //static constructor 
    static Test(){

    }
}
```

#### Private Constructor

- Cannot create object outside the class  
- Used to implement singleton design pattern  
- Instance creation only possible within the same class
- External Inheritance: A class with only a private constructor cannot be inherited by an external child class. This is because the child class needs to call a publicly accessible parent constructor to initialize.

#### Destructor (Finalizer)
```csharp
~ClassName(){
    // Cleanup code
}
```

##### 1. What are Destructors?

* **Definition:** Also known as **Finalizers**, they are used to perform necessary final clean-up when a class instance is being collected by the Garbage Collector (GC).
* **Naming:** They have the same name as the class but are prefixed with a **tilde (~)** symbol (e.g., `~Sample()`).
* **Invocation:** Unlike constructors (called explicitly with `new`), destructors are **called implicitly** by the Garbage Collector.

##### 2. When is a Destructor Called?

A destructor is called automatically in these scenarios:

1. **End of Program:** When the program finishes, all associated objects are destroyed.
2. **Memory Pressure:** When the GC identifies unused objects to free up space.
3. **Explicit GC Request:** When a programmer manually calls `GC.Collect()`, though the exact timing of the destructor execution is still managed by the CLR.

##### 3. Key Rules & Constraints

* **Classes Only:** Destructors cannot be defined in `structs`; they are only for `classes`.
* **No Modifiers/Parameters:** You cannot use access modifiers (like `public`) or parameters.
* **No Overloading:** Since they don't take parameters, a class can have **only one** destructor.
* **No Explicit Calls:** You cannot manually call a destructor; only the GC can invoke it.

##### 4. Internal Implementation (IL Code)

C# destructors are internally translated by the compiler into a **Finalize** method. The code you write inside a destructor is wrapped in a `try` block, and the base class's `Finalize` method is called in the `finally` block:

```csharp
protected override void Finalize() {
    try {
        // Your destructor code here
    }
    finally {
        base.Finalize();
    }
}
```

##### 5. Why use them? (Unmanaged Resources)

While the Garbage Collector automatically manages memory for "managed" objects, it doesn't know how to clean up **unmanaged resources** like:

* File handles
* Database connections
* Network sockets
* COM objects
Destructors provide a "safety net" to ensure these resources are released if a developer forgets to call `Dispose()`.

##### Summary Comparison: Constructor vs. Destructor

| Feature | Constructor | Destructor (Finalizer) |
| --- | --- | --- |
| **Syntax** | `ClassName()` | `~ClassName()` |
| **Purpose** | Initialize object state | Perform clean-up before destruction |
| **Invocation** | Explicitly (via `new`) | Implicitly (by Garbage Collector) |
| **Parameters** | Can have parameters | No parameters allowed |
| **Overloading** | Supported | Not supported |
| **Frequency** | Once per object creation | Once per object destruction |

##### Pro-Tip for Principal Engineer Interviews:

Mention the **Dispose Pattern**. While destructors exist, they are non-deterministic (you don't know *exactly* when they will run). For critical resources, implementing the `IDisposable` interface and the `Dispose()` method is the preferred way to provide immediate, deterministic clean-up. A destructor should be a fallback mechanism.

- Implicitly called when object is destroyed  
- No access modifier

### Singleton Design Pattern
This is the most common use case. A Singleton ensures only one instance of a class exists globally.

Implementation Steps:

- Declare a single private parameterless constructor (prevents external instantiation).
- Mark the class as sealed (prevents inheritance).
- Create a private static variable to hold the single instance.
- Provide a public static property/method (like GetInstance) that creates the instance once and returns it.

### Method Parameters

#### ref and out Keywords

- Used for passing arguments as reference type

// ref requires initialization

int addition = 0;

int substr = 0;

public static void MathRef(int a, int b, ref int addition, ref int substr){

    addition = a \+ b;

}

// out requires assignment within method

public static void MathOut(int a, int b, out int addition, out int substr){

    addition = a \+ b;

    // substr must be assigned before control leaves

}

#### Named Parameters

employee.AddEmployeeInfo(IsPermanent: true, Name: "Pranaya", EmpID: 1001, Department:"IT", Mobile:"1234567890" );

public void AddEmployeeInfo(long EmpID, string Name, bool IsPermanent, string Mobile, string Department)

---

## Advanced C\# Concepts

### Exception Handling

#### Custom Exception

namespace ExceptionHandlingDemo

{

    // Creating our own Exception Class by inheriting Exception class

    public class OddNumberException : Exception

    {

        // Overriding the Message property

        public override string Message

        {

            get

            {

                return "Divisor Cannot be Odd Number";

            }

        }

    }

}

// Usage:

throw new OddNumberException();

### Indexers

- Access class properties in array-like style  
- Useful for collection or map-like structures

public class StudentRegistry

{

    private Dictionary\<int, string\> students = new();

    public string this\[int id\]

    {

        get =\> students.ContainsKey(id) ? students\[id\] : "Unknown";

        set =\> students\[id\] = value;

    }

}

StudentRegistry registry = new();

registry\[101\] = "Akshay";

Console.WriteLine(registry\[101\]);  // Output: Akshay

### Reflection

- Determines or inspects content of an assembly  
- Dynamically create instances, bind types, get types from existing objects  
- Invoke methods or access fields and properties

### var vs dynamic vs reflection

| Feature | var | dynamic | reflection |
| :---- | :---- | :---- | :---- |
| Type checking | Compile-time | Runtime | Runtime |
| Type safety | Type safe | No type safety | No type safety |
| IntelliSense | Full support | Limited | Limited |
| Performance | Faster | Slower (late binding) | Slowest |
| LINQ support | Excellent | Not ideal | Can be used |

### Volatile Keyword

- Rarely used in multi-threaded applications  
- Indicates field might be modified by multiple threads executing simultaneously  
- Excludes field from certain optimizations

### Events, Delegates, and Lambda Expressions

#### Event Flow

Event \<----\>(through delegates pipeline) \<----\> Event Handler (Event Listener)

- **EventArgs**: Containers for passing data  
- **Delegate**: Function pointer, glue between event raiser and event handler  
- **MulticastDelegate**: Special class that tracks event listeners  
- **Event Handler**: Method that receives and processes data from delegate

#### Basic Event Example

this.button1.Click \+= new System.EventHandler(this.button1\_click);  // delegate

private void button1\_Click(object sender, EventArgs e){            // Event handler

    // Handle event

}

#### Custom Delegates

public delegate void WorkPerformedHandler(int hours, WorkType workType);

**Types of delegates:**

1. **Single Cast Delegate** - invoking single method  
2. **Multicast Delegate** - invoke multiple methods

#### Generic Delegates

| Delegate | Parameters | Return Type | Use Case |
| :---- | :---- | :---- | :---- |
| `Func<T>` | 0–16 | Required | When method returns a value |
| `Action<T>` | 0–16 | void | When method performs action but returns nothing |
| `Predicate<T>` | 1 | bool | When method returns boolean (filtering) |

Func\<int, int, int\> add = (a, b) =\> a \+ b;

Console.WriteLine(add(3, 4)); // Output: 7

Action\<string\> greet = name =\> Console.WriteLine($"Hello, {name}\!");

greet("Alice"); // Output: Hello, Alice\!

Predicate\<string\> isLong = s =\> s.Length \> 5;

Console.WriteLine(isLong("Copilot")); // Output: True

#### Lambda Expressions

- Modern, concise way to define methods without names  
- Preferred for delegates, events, LINQ queries

List\<int\> numbers = new List\<int\> { 1, 2, 3, 4, 5 };

// Using lambda to filter even numbers

var evens = numbers.Where(n =\> n % 2 == 0);

// Using anonymous method (older style)

var odds = numbers.Where(delegate(int n) {

    return n % 2 \!= 0;

});

- Lambda is more modern, less code, more concise and preferred, used to define methods without giving them a name. They're super handy when you need quick, inline behavior—especially for delegates, events, or LINQ queries.

---

## Generics, Collections

### Why Generics?

- **Type Safety**: Compile-time type checking  
- **Performance**: No boxing/unboxing overhead  
- **Code Reusability**: Single implementation for multiple types

### Common Generic Collections

- `List<T>`: Dynamic array  
- `Dictionary<TKey, TValue>`: Key-value pairs  
- `HashSet<T>`: Unique elements  
- `Queue<T>`: FIFO collection  
- `Stack<T>`: LIFO collection

### Non-Generic Collections (Legacy)

- `ArrayList`: Object-based, requires boxing  
- `Hashtable`: Key-value pairs with object types

---

## Multi-Threading, Asynchronous Programming, Parallel Programming

### Threading Basics

- **Thread**: Basic unit of CPU execution  
- **ThreadPool**: Managed thread pool for short tasks  
- **Task**: Higher-level abstraction over ThreadPool

### Async/Await Pattern

public async Task\<string\> GetDataAsync()

{

    var result = await httpClient.GetStringAsync("https://api.example.com");

    return result;

}

### Parallel Programming

// Parallel.For

Parallel.For(0, 100, i =\> {

    ProcessItem(i);

});

// Parallel.ForEach

Parallel.ForEach(items, item =\> {

    ProcessItem(item);

});

---

## File Handling

### Common File Operations

// Read all text

string content = File.ReadAllText("path.txt");

// Write all text

File.WriteAllText("path.txt", "content");

// Read lines

string\[\] lines = File.ReadAllLines("path.txt");

// Stream operations

using (StreamReader reader = new StreamReader("path.txt"))

{

    string line;

    while ((line = reader.ReadLine()) \!= null)

    {

        Console.WriteLine(line);

    }

}

### Directory Operations

// Create directory

Directory.CreateDirectory("path");

// Check if exists

bool exists = Directory.Exists("path");

// Get files

string\[\] files = Directory.GetFiles("path");

---

## AutoMapper

### What is AutoMapper?

- Used to map one object to another object directly, without manually assigning each property  
- NuGet package: AutoMapper

### Basic Configuration

var config = new MapperConfiguration(cfg =\>

    cfg.CreateMap\<TSource, TDestination\>()

);

var mapper = new Mapper(config);

Employee emp = new Employee();

var empDTO = mapper.Map\<EmployeeDTO\>(emp);

### Custom Property Mapping

cfg.CreateMap\<Employee, EmployeeDTO\>()

    // Provide Mapping Configuration of FullName and Name Property

    .ForMember(dest =\> dest.FullName, act =\> act.MapFrom(src =\> src.Name))

    

    // Provide Mapping Dept of FullName and Department Property

    .ForMember(dest =\> dest.Dept, act =\> act.MapFrom(src =\> src.Department));

---

## LINQ (Language Integrated Query)

### Query Syntax vs Method Syntax

// Query Syntax

var query = from student in students

            where student.Age \> 18

            orderby student.Name

            select student;

// Method Syntax

var result = students

    .Where(s =\> s.Age \> 18\)

    .OrderBy(s =\> s.Name)

    .Select(s =\> s);

### Common LINQ Operations

- **Where**: Filtering  
- **Select**: Projection  
- **OrderBy**: Sorting  
- **GroupBy**: Grouping  
- **Join**: Joining collections  
- **First/FirstOrDefault**: Getting first element  
- **Single/SingleOrDefault**: Getting single element

---

## Unit Testing

### Testing Frameworks

- **MSTest**: Microsoft's testing framework  
- **NUnit**: Open-source testing framework  
- **xUnit**: Free, open-source, community-focused

### EF In-Memory Databases

// Setup

var options = new DbContextOptionsBuilder\<MyDbContext\>()

    .UseInMemoryDatabase(databaseName: "TestDb")

    .Options;

using (var context = new MyDbContext(options))

{

    // Arrange & Act

    var service = new MyService(context);

    var result = await service.GetDataAsync();

    

    // Assert

    Assert.NotNull(result);

}

---

## ASP.NET & Web Development

### MVC Architecture

#### Request-Response Lifecycle

1. **Routing**: Route.config determines controller and action  
2. **Controller**: Action method executes  
3. **Model**: Data preparation  
4. **View**: Response rendering

#### Key Components

##### Controllers

- **Action Methods**: Handle requests  
- **RedirectToAction**, **Redirect**, **RedirectToRoute**: Navigation methods  
- **Non-Action Methods**: Not accessible via URL

##### Views

- **Partial View vs Normal View**: Reusability  
- **Razor View Engine**: Server-side rendering  
- **HTML Helpers**: Generate HTML elements

##### Data Passing

- **Controller to View**: ViewBag, ViewData, TempData, Model  
- **View to Controller**: Form collection, request parameters

##### TempData

- **Peek vs Keep**:  
  - `Peek`: Read without marking for deletion  
  - `Keep`: Mark to persist for next request

#### MVC Filters

- **Authorization Filters**: Authentication/authorization  
- **Action Filters**: Before/after action execution  
- **Result Filters**: Before/after result execution  
- **Exception Filters**: Handle exceptions

#### Routing

- **Convention Routing**: Pattern-based routes  
- **Attribute Routing**: Decorator-based routes

#### Security

- **Anti-Forgery Tokens**: `@Html.AntiForgeryToken()`  
- **CORS**: Cross-Origin Resource Sharing  
- **SQL Injection Prevention**: Parameterized queries  
- **Authentication & Authorization**: JWT tokens, OAuth

#### State Management

- **Session**: User-specific data  
- **Cookies**: Client-side storage  
- **Cache**: Application-level caching

### Web API

#### REST vs gRPC

| Feature | REST (HTTP/1.1) | gRPC (HTTP/2) |
| :---- | :---- | :---- |
| Data Format | Text (JSON/XML) - bulky | Binary (Protobuf) - tiny and fast |
| Payload Size | Large (includes keys) | Small (uses numeric tags) |
| Multiplexing | Requests are sequential (blocking) | Parallel (multiple requests on 1 connection) |
| Streaming | Client-to-Server only | Bi-directional (real-time flow) |
| Contract | Optional (Swagger/OpenAPI) | Strict (Defined in .proto files) |

#### gRPC Implementation

##### Define Contract (.proto file)

syntax = "proto3";

option csharp\_namespace = "MyMicroservice.Grpc";

service Greeter {

  rpc SayHello (HelloRequest) returns (HelloReply);

}

message HelloRequest {

  string name = 1;

}

message HelloReply {

  string message = 1;

}

##### Server Implementation

public class GreeterService : Greeter.GreeterBase

{

    public override Task\<HelloReply\> SayHello(HelloRequest request, ServerCallContext context)

    {

        return Task.FromResult(new HelloReply { 

            Message = "Hello " \+ request.Name 

        });

    }

}

##### Register in Program.cs

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddGrpc();

var app = builder.Build();

app.MapGrpcService\<GreeterService\>();

app.Run();

#### HTTP Methods

- **GET**: Retrieve data  
- **POST**: Create data  
- **PUT**: Update entire resource  
- **PATCH**: Partial update  
- **DELETE**: Remove resource

#### Authentication & Authorization

- **JWT Tokens**: Stateless authentication  
- **OWIN Package**: Security middleware  
- **Custom Filters**: Authorization filters

---

## Design Patterns & Architecture

### Design Pattern Categories

- **Creational Patterns**: Object creation mechanisms  
- **Structural Patterns**: Class and object composition  
- **Behavioral Patterns**: Communication between objects

### Gang of Four Patterns

- **Abstract Factory**: Create families of related objects  
- **Factory**: Create objects without specifying exact class  
- **Singleton**: Ensure single instance  
- **Command**: Encapsulate requests as objects

### Common Design Principles

#### GOF 23 Design Patterns

The Gang of Four (GoF) patterns are 23 classic design patterns categorized into:

- **Creational (5)**: Singleton, Factory Method, Abstract Factory, Builder, Prototype  
- **Structural (7)**: Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy  
- **Behavioral (11)**: Chain of Responsibility, Command, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor, Interpreter

#### DRY (Don't Repeat Yourself)

- **Principle**: Every piece of knowledge must have a single, unambiguous, authoritative representation within a system  
- **Benefits**: Reduces code duplication, easier maintenance, consistent behavior  
- **Implementation**: Extract common functionality into reusable methods/classes

#### KISS (Keep It Simple, Stupid)

- **Principle**: Most systems work best if they are kept simple rather than made complicated  
- **Benefits**: Easier to understand, maintain, and debug  
- **Implementation**: Avoid unnecessary complexity, choose simple solutions over complex ones

#### MVC (Model-View-Controller)

- **Pattern**: Separates application into three interconnected components  
  - **Model**: Data and business logic  
  - **View**: User interface representation  
  - **Controller**: Handles user input and coordinates Model and View  
- **Benefits**: Separation of concerns, testability, parallel development  
- **Usage**: Web applications, desktop applications, mobile apps

#### Repository Pattern

- **Pattern**: Mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects  
- **Benefits**: Centralized data access logic, improved testability, separation of concerns  
- **Implementation**:

public interface IRepository\<T\> where T : class

{

    Task\<T\> GetByIdAsync(int id);

    Task\<IEnumerable\<T\>\> GetAllAsync();

    Task AddAsync(T entity);

    Task UpdateAsync(T entity);

    Task DeleteAsync(T entity);

}

public class Repository\<T\> : IRepository\<T\> where T : class

{

    private readonly DbContext \_context;

    private readonly DbSet\<T\> \_dbSet;

    public Repository(DbContext context)

    {

        \_context = context;

        \_dbSet = context.Set\<T\>();

    }

    public async Task\<T\> GetByIdAsync(int id)

    {

        return await \_dbSet.FindAsync(id);

    }

    public async Task\<IEnumerable\<T\>\> GetAllAsync()

    {

        return await \_dbSet.ToListAsync();

    }

    public async Task AddAsync(T entity)

    {

        await \_dbSet.AddAsync(entity);

        await \_context.SaveChangesAsync();

    }

    public async Task UpdateAsync(T entity)

    {

        \_dbSet.Update(entity);

        await \_context.SaveChangesAsync();

    }

    public async Task DeleteAsync(T entity)

    {

        \_dbSet.Remove(entity);

        await \_context.SaveChangesAsync();

    }

}

#### Unit of Work Pattern

- **Pattern**: Maintains a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems  
- **Benefits**: Transaction management, consistency, reduced database calls  
- **Implementation**:

public interface IUnitOfWork : IDisposable

{

    IRepository\<T\> Repository\<T\>() where T : class;

    Task\<int\> SaveChangesAsync();

}

public class UnitOfWork : IUnitOfWork

{

    private readonly DbContext \_context;

    private readonly Dictionary\<Type, object\> \_repositories = new Dictionary\<Type, object\>();

    public UnitOfWork(DbContext context)

    {

        \_context = context;

    }

    public IRepository\<T\> Repository\<T\>() where T : class

    {

        if (\_repositories.ContainsKey(typeof(T)))

        {

            return \_repositories\[typeof(T)\] as IRepository\<T\>;

        }

        var repository = new Repository\<T\>(\_context);

        \_repositories.Add(typeof(T), repository);

        return repository;

    }

    public async Task\<int\> SaveChangesAsync()

    {

        return await \_context.SaveChangesAsync();

    }

    public void Dispose()

    {

        \_context.Dispose();

    }

}

### SOLID Principles

1. **S** - Single Responsibility Principle  
2. **O** - Open/Closed Principle  
3. **L** - Liskov Substitution Principle  
4. **I** - Interface Segregation Principle  
5. **D** - Dependency Inversion Principle

### Architecture Patterns

#### 3-Tier Architecture

- **Presentation Layer**: UI  
- **Business Logic Layer**: Processing  
- **Data Access Layer**: Database operations

#### Microservices Architecture

##### Core Components

- **Cloud providers**: Azure, AWS, etc.  
- **Docker and containerization**: Container-based deployment  
- **Kubernetes for container orchestration**: K8s for scaling and management  
- **Message buses and event-driven architecture**: Asynchronous communication  
- **API gateways and service discovery**: Centralized routing and service location

##### API Gateway Options

- **Ocelot**: Community-driven API gateway  
- **Azure API Management**: Cloud-based API management solution  
- **YARP**: Microsoft's recommended reverse proxy solution

##### Service Communication

###### *Synchronous Communication*

- **HTTP**: Traditional REST API calls  
- **gRPC**: High-performance RPC communication (detailed in gRPC section)

###### *Asynchronous Communication (Event Bus)*

- **RabbitMQ**: Open-source message broker  
- **Azure Service Bus**: Cloud-based messaging service  
- **Amazon SQS**: AWS queue service  
- **Libraries**: MassTransit or Rebus for .NET integration

##### Data Management per Service

- **SQL databases**: Using Entity Framework Core  
- **NoSQL databases**: MongoDB, Redis for different data models

##### Containerization and Orchestration

- **Docker**: Container creation and management  
- **Kubernetes (K8s)**: Container orchestration and scaling

##### Challenges to Consider

###### *Complexity*

- Managing 20 services is harder than managing one  
- Requires sophisticated monitoring and deployment strategies

###### *Data Consistency*

- Each service has its own database  
- Must use **Saga Pattern** or **Eventual Consistency** instead of traditional SQL transactions  
- No ACID transactions across services

###### *Observability*

- Need centralized logging (ELK Stack or Seq)  
- Track single requests across multiple services  
- Distributed tracing and monitoring essential

### Dependency Injection

- **Inversion of Control**: Design principle  
- **DI Container**: Manages dependencies  
- **Lifetime Management**: Singleton, Scoped, Transient

---

## Data Access & Databases

### ADO.NET

- **Connected Architecture**: DataReader, Connection, Command  
- **Disconnected Architecture**: DataSet, DataAdapter

### Entity Framework

- **Code First**: Database from model  
- **Database First**: Model from database  
- **Model First**: Visual model design

### Database Operations

#### Stored Procedures vs Functions

| Feature | Stored Procedure | Function |
| :---- | :---- | :---- |
| Return | Multiple result sets | Single value/table |
| Transaction | Can use transactions | Cannot use transactions |
| DML Operations | Can perform INSERT/UPDATE/DELETE | Cannot modify data |

#### Performance Optimization

- **SQL Profiler**: Query analysis  
- **Indexes**: Improve query performance  
- **Triggers**: Automated actions  
- **Table Variables vs Temp Tables**: Memory vs disk

### Data Structures

#### Collections vs Generics

- **Collections**: Object-based, boxing/unboxing  
- **Generics**: Type-safe, better performance

#### Array vs ArrayList

| Feature | Array | ArrayList |
| :---- | :---- | :---- |
| Type | Strongly typed | Object-based |
| Performance | Faster | Slower (boxing) |
| Size | Fixed | Dynamic |

#### IEnumerable vs IQueryable

| Feature | IEnumerable | IQueryable |
| :---- | :---- | :---- |
| Execution | In-memory | Database |
| Filtering | Client-side | Server-side |
| Performance | Load all data | Load filtered data |

---

## Performance & Optimization

### Caching Strategies

#### In-Memory Cache

- **Description**: Stores data in the application's memory.  
- **Best suited for**: Small/medium, not for distributed datasets.  
- This is the default caching strategy for most ASP.NET Core projects. Each server has their own in memory cache.  
- Uses IMemoryCache (System.Runtime.Caching.MemoryCache for Microsoft.Extensions.Caching.Memory in ASP.NET Core).

var cache = new MemoryCache(new MemoryCacheOptions());

cache.Set("key", "value", TimeSpan.FromMinutes(60));

var value = cache.Get("key");

##### Program.cs Configuration

builder.Services.AddMemoryCache(); // Microsoft.Extensions.Caching.Memory library

builder.Services.AddSingleton\<ICacheService, MemoryCacheService\>();

##### MemoryCacheService.cs Implementation

using Microsoft.Extensions.Caching.Memory;

namespace Test.Common

{

    public class MemoryCacheService : ICacheService

    {

        private readonly IMemoryCache \_memoryCache;

        private readonly IConfiguration \_config;

        public MemoryCacheService(IMemoryCache memoryCache, IConfiguration config)

        {

            \_memoryCache = memoryCache;

            \_config = config;

        }

        public T Get\<T\>(string key)

        {

            if (\_memoryCache.TryGetValue(key, out T value))

            {

                return value;

            }

            return default;

        }

        public void Set\<T\>(string key, T value, TimeSpan? expirationTime = null)

        {

            var options = new MemoryCacheEntryOptions();

            if (expirationTime.HasValue)

            {

                options.SetAbsoluteExpiration(expirationTime.Value);

            }

            else

            {

                var mins = \_config\["ColumnFilterCacheExpiry"\];

                if (int.TryParse(mins, out int expiryMins) && expiryMins \> 0\)

                {

                    options.SetAbsoluteExpiration(TimeSpan.FromMinutes(expiryMins));

                }

                else

                {

                    options.SetAbsoluteExpiration(TimeSpan.FromMinutes(30));

                }

            }

            options.SetPriority(CacheItemPriority.High);

            \_memoryCache.Set(key, value, options);

        }

    }

}

**Usage**: Call MemoryCacheService Get and Set Method wherever required

---

#### Distributed Caching:

- **Description**: Stores cache data outside the application's process, often in a shared location.  
- **Common Use**: Useful for web farms or cloud applications.  
- **Examples**: Redis, SQL Server, NCache.  
- **.NET Example**: IDistributedCache interface.

##### Basic Implementation

public class MyService

{

    private readonly IDistributedCache \_cache;

    public MyService(IDistributedCache cache) =\> \_cache = cache;

    public async Task SetCacheAsync(string key, string value)

    {

        await \_cache.SetStringAsync(key, value);

    }

}

##### API Flow Diagram

Client

  |

  v

API Controller/Service

  |

  v

Distributed Cache (Redis/SQL Server)

  |          |

 (hit)     (miss)

  |          v

  |    Database/Other Source

  |          |

  |     Return Data

  |          |

  |     Store in Cache

  |          |

  \+----------+

       |

  Return Data

##### Redis Implementation

public class MyService

{

    private readonly IDistributedCache \_cache;

    public MyService(IDistributedCache cache) =\> \_cache = cache;

    public async Task\<string\> GetDataAsync(string key)

    {

        var cached = await \_cache.GetStringAsync(key);

        if (cached \!= null)

            return cached;

        // Simulate data fetch (e.g., from DB)

        var data = "fetched from database";

        await \_cache.SetStringAsync(key, data, new DistributedCacheEntryOptions

        {

            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)

        });

        return data;

    }

}

##### Redis Configuration

\# Install NuGet Package

dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis

##### Program.cs Setup

// Add Redis distributed cache

builder.Services.AddStackExchangeRedisCache(options =\>

{

    options.Configuration = builder.Configuration.GetConnectionString("RedisConnection"); // or your Redis connection string

    options.InstanceName = "Test:";

});

// Optionally, register your cache service to use IDistributedCache instead of IMemoryCache 

builder.Services.AddSingleton\<ICacheService, DistributedCacheService\>();

##### appsettings.json

{

  "ConnectionStrings": {

    "RedisConnection": "localhost:6379" // Replace with your Redis server address

  }

}

##### DistributedCacheService Implementation

public class DistributedCacheService : ICacheService

{

    private readonly IDistributedCache \_cache;

    public DistributedCacheService(IDistributedCache cache) =\> \_cache = cache;

    

    public async Task SetAsync(string key, string value, TimeSpan? expiry = null)

    {

        var options = new DistributedCacheEntryOptions();

        if (expiry.HasValue)

            options.AbsoluteExpirationRelativeToNow = expiry.Value;

        await \_cache.SetStringAsync(key, value, options);

    }

    public async Task\<string?\> GetAsync(string key)

    {

        return await \_cache.GetStringAsync(key);

    }

}

##### Redis Distribution Configuration

To configure distribution (scalability and high availability) in Redis, you typically use Redis clustering or Redis Sentinel:

**Redis Clustering**

- Redis Cluster allows you to distribute data across multiple Redis nodes (shards), providing horizontal scaling and fault tolerance.  
- Deploy multiple Redis nodes (at least 6 for production: 3 masters, 3 replicas).  
- Configure each node with a unique port and cluster settings in redis.conf

**Redis Sentinel**

- Deploy multiple Redis instances (1 master, 1+ replicas).  
- Deploy 3+ Sentinel processes.  
- Configure sentinel.conf for each Sentinel.  
- Sentinel will monitor the master and promote a replica if the master fails

---

#### Response Caching

- **Description**: Caches HTTP responses to reduce server load and improve performance  
- **Common Use**: Web APIs and ASP.NET Core MVC.  
- **Example**: \[ResponseCache\] attribute in ASP.NET Core.

\[ResponseCache(Duration = 60)\]

public IActionResult Get()

{

    //......

}

// In Program.cs

builder.Services.AddResponseCaching();

app.UseResponseCaching();

---

#### Output Caching:

- **Description**: Caches the output of controller actions  
- **Common Use**: ASP.NET MVC and Web Forms.  
- **Example**: \[OutputCache\] attribute (ASP.NET MVC).

\[OutputCache(Duration = 60)\]

public ActionResult Index()

{

    //....

}

**Key Differences:**

- Output Caching is used in Older .NET, Response Caching is used in .NET Core  
- Output cache is done on server side  
- Response cache is done on browser side, meaning when new API hits server will tell to utilize cached response from browser.

### Rate Limiting

#### Overview

- Built-in middleware that restricts the number of requests a user or client can make to your API within a specific timeframe  
- Critical security and stability feature to prevent "noisy neighbors," brute-force attacks, and server resource exhaustion

#### How It Works

When a request hits your API, the Rate Limiting middleware checks if the "bucket" for that client is full. If the limit is exceeded, the API returns a **429 Too Many Requests** status code instead of processing the logic.

#### The Four Core Algorithms

##### Fixed Window

- **Behavior**: Uses a fixed time frame (e.g., 10 requests every 1 minute). The counter resets exactly at the end of that window.  
- **Best For**: Simple API limits where "bursts" at the very end of a window aren't a major concern.

##### Sliding Window

- **Behavior**: Similar to Fixed Window, but divides the time frame into segments. It "slides" forward, preventing a double-burst of traffic that can occur at the boundary of a fixed reset.  
- **Best For**: Preventing spikes at the turn of the minute and providing a smoother user experience.

##### Token Bucket

- **Behavior**: Tokens are added to a "bucket" at a steady rate. Each request consumes one token. If the bucket is empty, requests are rejected until more tokens are added.  
- **Best For**: Allowing occasional bursts of high traffic while maintaining a consistent average rate over the long term.

##### Concurrency

- **Behavior**: Does not look at time at all; it simply limits the number of simultaneous active requests. Once a request finishes, a "slot" opens up for a new one.  
- **Best For**: Protecting heavy-processing endpoints, such as image manipulation, PDF generation, or long-running database exports.

#### Implementation

##### Step 1: Configure the Policy

using System.Threading.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRateLimiter(options =\>

{

    // Define a "Fixed Window" policy named "fixed"

    options.AddFixedWindowLimiter(policyName: "fixed", opt =\>

    {

        opt.PermitLimit = 5;            // Max 5 requests

        opt.Window = TimeSpan.FromSeconds(10); // Per 10 seconds

        opt.QueueLimit = 2;             // Queue up to 2 extra requests

        opt.QueueProcessingOrder = QueueProcessingOrder.OldestFirst;

    });

    // Optional: Customize the rejection response

    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

});

##### Step 2: Use the Middleware

var app = builder.Build();

app.UseRateLimiter(); // Must be called after UseRouting

app.MapGet("/api/data", () =\> Results.Ok("Success\!"))

   .RequireRateLimiting("fixed"); // Apply the policy here

app.Run();

#### Global vs. Partitioned Limits

- **Global Limiter**: Limits every single request coming to the server, regardless of who is calling.  
- **Partitioned Limiter**: The most common approach. It allows you to limit requests per User ID or per IP Address. This ensures that if one user spams your API, everyone else can still use it.

**Tip**: In production, you usually want to partition by an API Key or a JWT Claim:

partitionKey: httpContext =\> httpContext.User.Identity?.Name ?? httpContext.Connection.RemoteIpAddress?.ToString()

#### Why Use This Over Polly?

- **Polly**: Handles outgoing calls (your API calling someone else)  
- **.NET Rate Limiter**: Handles incoming calls (users calling you)

#### Advanced Implementation: Tiered Rate Limiting

Here's how to set up a system that gives Premium users more bandwidth than Free users:

builder.Services.AddRateLimiter(options =\>

{

    options.AddPolicy("TieredPolicy", httpContext =\>

    {

        // 1\. Identify the user (e.g., via a claim in a JWT)

        var userTier = httpContext.User.FindFirst("tier")?.Value ?? "Free";

        var userName = httpContext.User.Identity?.Name ?? "Anonymous";

        // 2\. Return a partition based on the user's name/ID

        if (userTier == "Premium")

        {

            return RateLimitPartition.GetFixedWindowLimiter(

                partitionKey: userName,

                factory: \_ =\> new FixedWindowRateLimiterOptions

                {

                    PermitLimit = 100, // High limit

                    Window = TimeSpan.FromMinutes(1)

                });

        }

        return RateLimitPartition.GetFixedWindowLimiter(

            partitionKey: userName,

            factory: \_ =\> new FixedWindowRateLimiterOptions

            {

                PermitLimit = 10, // Low limit

                Window = TimeSpan.FromMinutes(1)

            });

    });

    // Custom response for blocked users

    options.OnRejection = async (context, token) =\>

    {

        context.HttpContext.Response.StatusCode = 429;

        await context.HttpContext.Response.WriteAsync("Rate limit exceeded. Upgrade to Premium for more requests\!", token);

    };

});

#### User Tier Comparison

| User Tier | Requests Allowed | Period | Benefit |
| :---- | :---- | :---- | :---- |
| Free | 10 | 1 Minute | Basic access, prevents server abuse |
| Premium | 100 | 1 Minute | High-speed integration for power users |
| Anonymous | 5 | 1 Minute | Highly restrictive to prevent bot scraping |

#### Best Practices for API Rate Limiting

- **Use Redis for Distributed Apps**: If you have multiple server instances (a web farm), the standard middleware limits per instance. To limit across the whole cluster, use Redis-based rate limiting options.  
- **Inform the Client**: Send headers back so the developer knows their status (e.g., X-RateLimit-Remaining).  
- **Combine with Polly**: Use .NET Rate Limiting on incoming requests to protect your server, and use Polly on outgoing HttpClient calls to respect external API rate limits.

### Resilience Patterns (Polly)

#### Overview

- **Polly v8**: Resilience Pipelines for .NET 8/9  
- More performant and integrates natively through "Microsoft.Extensions.Http.Resilience" package

#### Core Patterns

##### Retry Pattern

- **Purpose**: Automatically tries an operation again when it fails  
- **Best for**: "Blips" - temporary network glitches, 503 errors  
- **Analogy**: Try calling again in a second

##### Circuit Breaker Pattern

- **Purpose**: "Trips" and stops all requests to a failing system for a set time  
- **Best for**: Total service outages, high latency  
- **Analogy**: Stop calling; the line is down

#### Comparison Table

| Feature | Retry | Circuit Breaker |
| :---- | :---- | :---- |
| **Goal** | Fixes transient errors | Prevents cascading failures |
| **Analogy** | Try calling again in a second | Stop calling; the line is down |
| **When to use** | Network glitches, 503 errors | Total service outages, high latency |
| **Risk** | Can hammer a dying service | Prevents any calls (even if it might work) |

#### Implementation

##### Step 1: Install NuGet Package

dotnet add package Microsoft.Extensions.Http.Resilience

##### Step 2: Register in Program.cs

builder.Services.AddHttpClient("MyService")

.AddStandardResilienceHandler(options =\>

{

    // Customize the Retry strategy

    options.Retry.MaxRetryAttempts = 3;

    options.Retry.BackoffType = DelayBackoffType.Exponential;

    

    // Customize the Circuit Breaker strategy

    options.CircuitBreaker.FailureRatio = 0.5; // Break if 50% of requests fail

    options.CircuitBreaker.SamplingDuration = TimeSpan.FromSeconds(30);

});

#### Circuit Breaker State Machine

The Circuit Breaker operates in three states:

1. **Closed**: Normal operation; requests flow through  
2. **Open**: Failure threshold reached; requests fail fast immediately without calling the service  
3. **Half-Open**: Waiting period ended; allows a trial request to see if the service recovered

### API Gateway

#### Overview

While many developers traditionally used Ocelot, Microsoft now actively supports and recommends **YARP (Yet Another Reverse Proxy)** for most new projects.

#### YARP vs Ocelot Comparison

| Feature | Ocelot | YARP |
| :---- | :---- | :---- |
| **Creator** | Open Source Community | Microsoft |
| **Performance** | Good (Standard Middleware) | Excellent (Built for high-speed proxying) |
| **Philosophy** | Feature Rich (Built-in Auth, Rate Limiting) | Extensible (Plugs into existing ASP.NET pipeline) |
| **Configuration** | JSON-based (ocelot.json) | .NET Configuration (appsettings.json or Code) |
| **Maintenance** | Community-driven (less active) | Actively maintained by Microsoft |

#### YARP Implementation (The Modern Way)

##### Installation

dotnet add package Yarp.ReverseProxy

##### Configuration

YARP uses the standard DI (Dependency Injection) container:

var builder = WebApplication.CreateBuilder(args);

// Load the gateway configuration from appsettings.json

builder.Services.AddReverseProxy()

    .LoadFromConfig(builder.Configuration.GetSection("ReverseProxy"));

var app = builder.Build();

app.MapReverseProxy(); // Enable the proxy middleware

app.Run();

##### appsettings.json Configuration

This tells the gateway: "When you see /orders, send it to the ordering-service."

{

  "ReverseProxy": {

    "Routes": {

      "orderRoute": {

        "ClusterId": "orderCluster",

        "Match": { "Path": "/orders/{\*\*catch-all}" }

      }

    },

    "Clusters": {

      "orderCluster": {

        "Destinations": {

          "destination1": { "Address": "http://ordering-service:8080/" }

        }

      }

    }

  }

}

#### Why Use an API Gateway in .NET?

- **Security (BFF Pattern)**: Centralize JWT validation at the gateway so internal microservices don't repeat the same security code  
- **Rate Limiting**: Protect internal services from being overwhelmed by limiting requests per IP  
- **Request Aggregation**: Gateway can call multiple services (User, Order, Product) and combine data into one response  
- **Protocol Translation**: Accept REST (HTTP/1.1) from browser but talk to internal services via gRPC (HTTP/2) for better performance

#### Ocelot Implementation (Alternative)

##### Installation

dotnet add package Ocelot

##### Configuration

In your Program.cs, register Ocelot and point to your configuration file:

// 1\. Add the configuration file

builder.Configuration.AddJsonFile("ocelot.json", optional: false, reloadOnChange: true);

// 2\. Add Ocelot services

builder.Services.AddOcelot();

// ... later in the pipeline

await app.UseOcelot();

##### ocelot.json Configuration File

Ocelot doesn't use controllers. It relies on ocelot.json to map "Upstream" (client-facing) requests to "Downstream" (internal) services:

{

  "Routes": \[

    {

      "UpstreamPathTemplate": "/gateway/writers",

      "UpstreamHttpMethod": \[ "Get" \],

      "DownstreamPathTemplate": "/api/writers",

      "DownstreamScheme": "https",

      "DownstreamHostAndPorts": \[

        {

          "Host": "localhost",

          "Port": 5002

        }

      \]

    },

    {

      "UpstreamPathTemplate": "/gateway/writers/{id}",

      "UpstreamHttpMethod": \[ "Get" \],

      "DownstreamPathTemplate": "/api/writers/{id}",

      "DownstreamScheme": "https",

      "DownstreamHostAndPorts": \[

        {

          "Host": "localhost",

          "Port": 5002

        }

      \]

    }

  \],

  "GlobalConfiguration": {

    "BaseUrl": "https://localhost:5003"

  }

}

##### Ocelot Key Features

Beyond simple routing, Ocelot provides several built-in features:

- **Rate Limiting**: Restrict client calls within time windows (e.g., 3 requests every 10 seconds)  
- **Caching**: With Ocelot.Cache.CacheManager package, cache responses to reduce load on internal microservices  
- **Request Aggregation**: Combine multiple microservice responses into a single JSON object  
- **Authentication**: Centralize JWT validation at the gateway

#### Why YARP Rather Than Ocelot?

##### 1\. Performance

YARP is built by Microsoft on the same high-performance infrastructure used by Azure Front Door and Bing:

- **Throughput**: YARP consistently outperforms Ocelot because it's a "streaming" proxy that passes data through as received  
- **Memory Management**: Highly optimized for modern .NET garbage collection, more stable under high-load

##### 2\. Maintenance and Future-Proofing

- **Active Support**: YARP is an official Microsoft project with dedicated team and regular updates  
- **Ocelot Status**: Community-driven with slowed development and potential breaking changes

##### 3\. Integration vs Configuration

- **YARP (Developer-First)**: Feels like standard ASP.NET Core with familiar DI, middleware, and configuration patterns  
- **Ocelot (Configuration-First)**: Relies heavily on ocelot.json file, can become a "JSON nightmare" for complex routing

#### Final Comparison

| Feature | YARP | Ocelot |
| :---- | :---- | :---- |
| **Ownership** | Microsoft | Community (Open Source) |
| **Philosophy** | Customizable Toolkit | Opinionated Gateway |
| **Performance** | Extremely High (Streaming) | Moderate (Buffering) |
| **Auth/Policies** | Uses standard .NET Middleware | Built-in (but less flexible) |
| **Best For** | High-traffic, custom logic | Small/Medium, simple setups |

---

## Modern .NET Features

### Compilation and Execution Process

#### .NET Compilation Flow Diagram

Source Code → Language Compiler → MSIL/IL/Managed Code → CLR (JIT) → Native Code → Execute

The diagram illustrates the typical compilation and execution flow for managed code in environments like .NET:

#### Step-by-Step Process

1. **Source Code**  
     
   - Human-readable code written by developers in languages like C\#, VB.NET, F\#  
   - Example: `.cs` files containing C\# code

   

2. **Language Compiler**  
     
   - Language-specific compiler (e.g., Roslyn for C\#, VB.NET compiler)  
   - Translates source code into intermediate language  
   - Performs syntax checking and basic optimizations

   

3. **MSIL/IL/Managed Code**  
     
   - Microsoft Intermediate Language (MSIL), also called Common Intermediate Language (CIL) or IL  
   - CPU-independent instruction set  
   - Stored in assemblies (.dll or .exe files)  
   - Contains both code and metadata

   

4. **CLR (JIT)**  
     
   - **Common Language Runtime (CLR)**: Virtual machine component that manages .NET program execution  
   - **Just-In-Time (JIT) Compiler**: Converts IL code to native machine code at runtime  
   - Provides runtime services: garbage collection, type safety, security  
   - Optimizes code for specific hardware and operating system

   

5. **Native Code**  
     
   - CPU-specific machine instructions (x86, x64, ARM)  
   - Directly executable by the processor  
   - Optimized for the current execution environment

   

6. **Execute**  
     
   - CPU executes the native code  
   - CLR continues to manage execution  
   - Application performs its intended operations

#### Benefits of This Process

- **Platform Independence**: Write code once, run on any platform with compatible CLR  
- **Language Interoperability**: Different .NET languages can work together seamlessly  
- **Performance Optimization**: JIT compiler optimizes for specific hardware at runtime  
- **Managed Execution**: Automatic memory management, type safety, and security features  
- **Flexibility**: Code can be optimized based on actual usage patterns at runtime

#### Key Points

- IL code is the same regardless of the source language (C\#, VB.NET, F\# all compile to IL)  
- JIT compilation happens on demand, method by method  
- Compiled native code is often cached to avoid recompilation  
- The CLR provides a "sandbox" environment for safe code execution

### Apache Kafka

#### Overview

- **Event-driven approach** for distributed streaming  
- **Distributed streaming platform** with aggregated capabilities  
- **Publish-subscribe model** for real-time data streaming  
- **High throughput**, fault tolerance, and event sequencing capabilities

#### Architecture Flow

UI (Angular) --\> API Gateway --\> .NET Producer Service (sends to Kafka) --\> Kafka ---\> Consumer Services

#### Core Components

##### Producer-Consumer Model

Producer | ---------------------\> | Kafka Topic | ---------------------\> | Consumer(s)

##### Kafka Cluster Structure

Producer(s)               Producer(s)

(send messages)          (send messages)

     |                            |

     |                            |

     v                            v

---------------- Kafka Cluster -------------------

     |          |              |                |              |

\[Broker\]   \[Broker\]   \[Broker\]   ....   \[Broker\]

     |               |                          |

Topic A      Topic B                Topic C

(Partition 0\)  (Partition 0\)         (Partition 0\)

(Partition 1\)  (Partition 1\)         (Partition 1\)

(across brokers)  (across brokers)   (across brokers)

#### Key Components Explained

##### Producers

- Create new events or data messages  
- Publish messages to specific topics in Kafka  
- Continuously push information to the cluster

##### Kafka Cluster

- **Central component** consisting of multiple brokers with load balancing  
- Receives and stores messages from producers  
- Acts as the control center for the streaming data platform  
- Distributes topics across multiple brokers for replication and parallelism

##### Topics and Partitions

- **Topics**: Categories where messages are published  
- **Partitions**: Divisions within each topic distributed across brokers  
- Enable parallel processing and fault tolerance

##### Zookeeper

- **Vital role** in managing and coordinating Kafka brokers  
- Manages broker topics  
- Tracks status of nodes  
- Orchestrates leader elections for partitions

##### Consumers

- Subscribe to one or more topics on the Kafka Cluster  
- Consume and process the data feed  
- Can be organized into **consumer groups** for load balancing  
- Act as the end receivers of the streaming channel

#### Benefits in .NET Applications

- **Scalability**: Handle high-volume data streams  
- **Fault Tolerance**: Built-in replication and partitioning  
- **Real-time Processing**: Event-driven architecture  
- **Microservices Communication**: Decoupled service communication  
- **Data Persistence**: Durable message storage

#### Use Cases

- Real-time analytics  
- Log aggregation  
- Event sourcing  
- Microservices communication  
- IoT data processing  
- Stream processing

---

### .NET Core vs .NET Framework

- **Cross-platform**: Runs on Windows, Linux, macOS  
- **Performance**: Improved runtime and JIT  
- **Modular**: NuGet-based dependencies  
- **Container-friendly**: Smaller footprint

### Async/Await Programming

- **Task-based Asynchronous Pattern (TAP)**  
- **Benefits**: Non-blocking UI, better scalability  
- **Keywords**: `async`, `await`, `Task`, `Task<T>`

public async Task\<string\> GetDataAsync()

{

    var result = await httpClient.GetStringAsync("https://api.example.com");

    return result;

}

### Task Parallel Library (TPL)

- **Parallel.For**: Parallel loops  
- **Parallel.ForEach**: Parallel enumeration  
- **Task.Run**: CPU-bound work

### LINQ (Language Integrated Query)

- **Query Syntax**: SQL-like syntax  
- **Method Syntax**: Lambda expressions  
- **Providers**: LINQ to Objects, SQL, XML

// Query Syntax

var query = from student in students

            where student.Age \> 18

            orderby student.Name

            select student;

// Method Syntax

var result = students

    .Where(s =\> s.Age \> 18\)

    .OrderBy(s =\> s.Name)

    .Select(s =\> s);

### AutoMapper

- **Object mapping**: Map one object to another  
- **Configuration**: Define mapping rules  
- **NuGet**: AutoMapper package

var config = new MapperConfiguration(cfg =\>

    cfg.CreateMap\<TSource, TDestination\>()

);

var mapper = new Mapper(config);

var destination = mapper.Map\<Destination\>(source);

---

## Search Algorithms

### Basic Search Algorithms

#### 1\. Linear Search

- **Description**: Checks each element sequentially  
- **Time Complexity**: O(n)  
- **Use Case**: Unsorted lists or small datasets

#### 2\. Binary Search

- **Description**: Divides search space in half repeatedly  
- **Time Complexity**: O(log n)  
- **Use Case**: Large, sorted datasets

#### 3\. Depth-First Search (DFS)

- **Description**: Explores as far as possible along branches  
- **Use Case**: Graph/tree traversal, maze-solving

#### 4\. Breadth-First Search (BFS)

- **Description**: Explores all neighbors at current depth  
- **Use Case**: Finding shortest paths

### Advanced Search Algorithms

#### 5\. Two Pointers Technique

- **Description**: Uses two pointers moving coordinately  
- **Use Case**: Pair-finding problems

#### 6\. Interpolation Search

- **Description**: Estimates position based on value distribution  
- **Use Case**: Uniformly distributed data

#### 7\. Exponential Search

- **Description**: Binary search variant for unbounded arrays  
- **Use Case**: Unknown array size

#### 8\. Jump Search

- **Description**: Block-based searching for ordered lists  
- **Use Case**: Balances linear and binary search

#### 9\. Fibonacci Search

- **Description**: Uses Fibonacci numbers for division  
- **Use Case**: Fewer comparisons than binary search

### AI Search Algorithms

#### k-Nearest Neighbors (kNN)

- **Description**: Finds closest data points based on similarity  
- **Use Case**: Recommendation systems, classification

#### A\* Search

- **Description**: Combines BFS and DFS with heuristics  
- **Use Case**: Pathfinding, games

### Google Search Algorithm

The Google Search algorithm is a complex system used to rank web pages based on their relevance to a user's query. It involves:

1. **Crawling**: Discovering web pages  
2. **Indexing**: Analyzing and categorizing content  
3. **Ranking**: Displaying pages based on relevance and other factors like backlinks and page speed

### Design Pattern Categories

- **Creation** = Creational design patterns  
- **Structural & Relations** = Structural Design Patterns  
- **Communication** = Behavioral Design pattern

---

## Interview Questions

### C\# Fundamentals

- What is the difference between `ref` and `out` keywords?  
- Explain boxing and unboxing with examples  
- What is the difference between `string` and `StringBuilder`?  
- What are access modifiers in C\#?  
- Explain the difference between `abstract class` and `interface`

### OOP Concepts

- What are the four principles of OOP?  
- Explain method overloading vs method overriding  
- What is method hiding?  
- Explain encapsulation vs abstraction  
- What is polymorphism and its types?

### ASP.NET & Web Development

- What is the MVC request lifecycle?  
- Explain the difference between ViewBag, ViewData, and TempData  
- What are MVC filters and their types?  
- What is the difference between PUT and PATCH?  
- How does authentication work in Web API?

### Database & Data Access

- What is the difference between stored procedures and functions?  
- Explain the difference between `IEnumerable` and `IQueryable`  
- What is Entity Framework and its approaches?  
- How do you optimize database queries?  
- What is the difference between DataSet and DataReader?

### Advanced Concepts

- What is dependency injection and its benefits?  
- Explain SOLID principles with examples  
- What are design patterns and give examples  
- What is the difference between async and multithreading?  
- Explain the Circuit Breaker pattern

### Modern .NET

- What is the difference between .NET Core and .NET Framework?  
- Explain microservices architecture  
- What is gRPC and when to use it?  
- What is Polly and its resilience patterns?  
- Explain rate limiting and its implementation

### Performance & Optimization

- What are caching strategies in .NET?  
- How do you implement distributed caching?  
- What is the difference between in-memory and distributed cache?  
- Explain rate limiting algorithms  
- What is an API Gateway and its benefits?

### General Programming

- Explain the difference between Stack and Heap  
- What is garbage collection?  
- Explain the difference between `==` and `Equals()`  
- What is the difference between `const` and `readonly`?  
- Explain exception handling best practices

---

## Quick Reference

### Common NuGet Packages

\# Caching

dotnet add package Microsoft.Extensions.Caching.Memory

dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis

\# Resilience

dotnet add package Microsoft.Extensions.Http.Resilience

dotnet add package Polly

\# API Gateway

dotnet add package Yarp.ReverseProxy

dotnet add package Ocelot

\# AutoMapper

dotnet add package AutoMapper

dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection

\# Entity Framework

dotnet add package Microsoft.EntityFrameworkCore

dotnet add package Microsoft.EntityFrameworkCore.SqlServer

dotnet add package Microsoft.EntityFrameworkCore.InMemory

### Common Using Statements

using Microsoft.Extensions.Caching.Memory;

using Microsoft.Extensions.Caching.Distributed;

using Microsoft.AspNetCore.Mvc;

using Microsoft.EntityFrameworkCore;

using AutoMapper;

using System.Threading.Tasks;

using System.Linq;

### Configuration Templates

// appsettings.json

{

  "ConnectionStrings": {

    "DefaultConnection": "Server=(localdb)\\\\mssqllocaldb;Database=MyDb;",

    "RedisConnection": "localhost:6379"

  },

  "ReverseProxy": {

    "Routes": {},

    "Clusters": {}

  }

}

---

## Study Points

This section contains a comprehensive list of topics to study for C\# and .NET development, organized by category for systematic learning.

### ADO.NET & Database

- ADO.NET in brief  
- Stored procedure vs function  
- When to use function vs stored procedure  
- Table variable vs temp table  
- Optimizing stored procedures  
- SQL profiler  
- Triggers and indexes  
- GetDate function  
- Why procedure cannot be called from function  
- DataSet vs DataReader  
- SQL Express DB

### C\# Fundamentals

- What is PDB file  
- DirectCast and CType  
- COM object  
- Helper/utility methods/classes  
- String data structure problems (reverse a string)  
- What is tree data structure  
- Extension method  
- Virtual method  
- Managed vs unmanaged code  
- How to achieve override  
- Partial class  
- Partial method  
- Static  
- Abstract  
- Virtual  
- Sealed class  
- Can a constructor call another constructor of same class  
- Delegates  
- Boxing and unboxing  
- String vs StringBuilder  
- Reflection  
- CLR  
- Shallow vs deep copy  
- Serialization and deserialization  
- How partial class gets combined  
- Throw vs throw exception  
- Jagged array - theater tickets  
- Copy array and clone array difference  
- .NET assembly types  
- is vs as keyword  
- Dispose vs Finalizer

### Advanced C\# Concepts

- IEnumerable vs IEnumerator vs IQueryable difference  
- Method hiding vs method override vs method overloading  
- Runtime polymorphism - interface  
- Multi-threading  
- If two interfaces have same function then how to resolve this  
- Immutable types - advantages and disadvantages  
- Collection vs generics  
- Array vs ArrayList  
- Why we shouldn't use hash tables  
- Collection classes and how to use in projects  
- Static vs Singleton class difference  
- Static constructor  
- Task vs BackgroundWorker difference  
- Set NOCOUNT disadvantages  
- Async await Task library  
- Task Parallel Library  
- Loading DLL using reflection  
- Dispatcher

### Web Development - ASP.NET

- State and state management in ASP.NET  
- Difference between WebForms and MVC  
- @Html.AntiForgeryToken  
- What is Hypertext  
- What is REST representation  
- JWT tokens  
- MVC  
- Inversion of Control  
- Request to response lifecycle  
- Route.config - parameters, workflow  
- Bundle.config  
- Scaffold holding  
- Controller - action method  
- RedirectToAction, Redirect, RedirectToRoute  
- Non-action method  
- Controller to view and vice versa data passing  
- TempData - Peek and Keep  
- View to controller - request form collection  
- Partial view vs normal view  
- HTML helpers  
- Html.Action vs Html.RenderAction  
- Razor view  
- Filters - how to create custom filters  
- MVC area  
- Exception handling  
- OnException page  
- Handle error attribute  
- Application error event  
- Model binding  
- MVC/ASP.NET lifecycle  
- How to maintain same state throughout application  
- ViewBag  
- Scaffolding  
- How to set starting page in application  
- Different types of MVC filters  
- Routing  
- Page life cycle  
- How session is maintained in MVC  
- HTML element vs tag  
- What is middleware in .NET Core  
- Why MVC  
- How to connect WebAPI to application  
- AJAX call in MVC

### Web API & Security

- Difference between PUT and PATCH, advantages  
- How to use Fiddler and Swagger in API application  
- Difference between Thread vs Async await  
- Difference between Web API vs Web Services  
- Authorization of WebAPI  
- Web API security mechanism - OWIN package  
- Validate anti-forgery  
- CORS  
- SQL injection  
- Convention routing vs attribute routing  
- Authentication and authorization in detail in Web API  
- HTTP codes  
- POST vs PUT exact difference  
- Can we update record using POST

### Design Patterns & Architecture

- Gang of Four patterns: Abstract Factory, Factory, Singleton, Command, etc.  
- 3-tier architecture  
- What is architecture  
- What is design pattern  
- Factory design pattern  
- Singleton  
- Dependency injection  
- Builder pattern  
- SOLID principle  
- Difference between factory design and abstract factory design  
- Inter-process communication  
- Unit testing framework  
- Waterfall vs Agile

### LINQ & Querying

- LINQ result type  
- LINQ lifecycle  
- LINQ slowness solutions  
- IEnumerable vs IQueryable  
- LINQ where vs any and their signature

### Testing & Quality

- TDD development, how it works?  
- Unit testing framework  
- Assertion function signature  
- EF In-Memory Databases

### WPF & Desktop Development

- Difference between WinForms and WPF  
- WPF button round  
- Binding properties  
- Give search suggestion in WPF application  
- Component lifecycle in Angular (relevant for WPF data binding)  
- Visibility:hidden vs visibility:None

### JavaScript & Web Concepts

- JavaScript string concat  
- Rest operator JavaScript  
- == vs === difference  
- Context (JavaScript context)

### Tools & DevOps

- GIT  
- Undo changes in GIT  
- Deployment pipeline  
- Azure why to use  
- Cloud  
- .NET Core  
- LocalDB

### Advanced Scenarios

- I do not want to create object then how to use class - runtime no instance creation  
- How multiple API calls and one database call for high volume application  
- If class A and class B have same method name and our class implements both, which method will get called, how to remove ambiguity?  
- Access variables in child class of parent  
- Overwriting vs overloading vs overriding  
- Custom class using block  
- Try catch finally block, if try not there finally can be there?  
- Abstract class constructor present?  
- Multiple inheritance constructor sequence

### SQL Advanced

- Rank vs dense rank vs row number  
- Let vs var (SQL)

### Performance & Optimization

- Deadlock  
- Timeout in deadlock  
- How to resolve deadlock

### Real-world Implementation

- Give examples of OOPs concept in your project  
- Define accessor  
- Collection classes and how to use in your project  
- Authorization filter implementation  
- Authentication implementation  
- Singleton implementation  
- Action filters  
- MVVM pattern

---

*This document serves as comprehensive study notes for C\# and .NET development, covering fundamental concepts through advanced modern practices.*

# C\# Comprehensive Study Guide

## Table of Contents

- [C\# Fundamentals](#c-fundamentals)  
- [.NET Architecture & Compilation](#net-architecture--compilation)  
- [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)  
- [Advanced C\# Concepts](#advanced-c-concepts)  
- [Collections & Generics](#collections--generics)  
- [Delegates, Events & Lambda Expressions](#delegates-events--lambda-expressions)  
- [Exception Handling](#exception-handling)  
- [File I/O & Serialization](#file-io--serialization)  
- [LINQ (Language Integrated Query)](#linq-language-integrated-query)  
- [Multi-Threading & Asynchronous Programming](#multi-threading--asynchronous-programming)  
- [Design Patterns](#design-patterns)  
- [SOLID Principles](#solid-principles)  
- [Caching Strategies](#caching-strategies)  
- [Microservices Architecture](#microservices-architecture)  
- [API Gateway](#api-gateway)  
- [gRPC Communication](#grpc-communication)  
- [Apache Kafka](#apache-kafka)  
- [Resilience & Rate Limiting](#resilience--rate-limiting)  
- [Web API & MVC](#web-api--mvc)  
- [Database & Entity Framework](#database--entity-framework)  
- [Testing & Development Practices](#testing--development-practices)  
- [Performance Optimization](#performance-optimization)  
- [Security](#security)  
- [Deployment & DevOps](#deployment--devops)

---

## C\# Fundamentals

### Basic Syntax & Data Types

- **Variables are case sensitive and strongly typed**  
    
- **Explicit vs Implicit Typing:**  
    
  int i = 100;   // explicitly typed variable  
    
  var j = 100;   // implicitly typed local variable  
    
- **Default Values:**  
    
  int i = default;       // 0  
    
  float f = default;     // 0  
    
  decimal d = default;   // 0  
    
  bool b = default;      // false  
    
  char c = default;      // '\\0'  
    
- **String Handling:**  
    
  // Escape characters  
    
  string path = "test@gmail\\".com";  
    
  // Verbatim string literal  
    
  string path = @"test@gmail"".com";  
    
  // String interpolation  
    
  string fullName = $"Mr. {firstName} {lastName}, Code: {code}";  
    
- **Nullable Types:**  
    
  int? i = null;              
    
  int j = i ?? 0; // null coalescing operator

### DateTime Operations

DateTime currentDateTime = DateTime.Now;      // current date & time

DateTime todaysDate = DateTime.Today;         // today's date

DateTime currentDateTimeUTC = DateTime.UtcNow;// current UTC date & time

DateTime maxDateTimeValue = DateTime.MaxValue;

DateTime minDateTimeValue = DateTime.MinValue;

// Date parsing

var str = "5/12/2020";

DateTime dt;

var isValidDate = DateTime.TryParse(str, out dt);

if(isValidDate)

    Console.WriteLine(dt);

else

    Console.WriteLine($"{str} is not a valid date string");

### Type Conversions

string str = "123";

int a = Convert.ToInt32(str);     // ✅ Works

int b = int.Parse(str);           // ✅ Works

bool success = int.TryParse(str, out int c); // ✅ Works, success = true

string badStr = "abc";

int d = Convert.ToInt32(badStr);  // ❌ Throws FormatException

int e = int.Parse(badStr);        // ❌ Throws FormatException

bool ok = int.TryParse(badStr, out int f); // ✅ ok = false, f = 0

### Control Flow

// Switch statement

switch(variable){

    case 1:

        //code 

        break;

    case 2:

        //code 

        break;

    default:

        break;

}

// Ternary operator

var result = x \> y ? "x is greater than y" : "x is less than y";

var test = value ?? 0; // if value is there else 0 value

### Stack vs Heap Memory

STACK                                 HEAP

--------------------------------     -------------------------------------

| int x = 42            |           |                                     |

|----------------------|           |                                     |

| string name ---------|----------\>|  "Alice" (immutable string object)  |

|                      |           |                                     |

| Person p ------------|----------\>|  Person object                      |

|                      |           |   ├─ Name: "Bob"                    |

|                      |           |                                     |

--------------------------------     -------------------------------------

When Example() finishes, the stack frame (with x, name, and p) is cleared.

The heap memory for "Alice" and the Person object remains until garbage-collected.

- **Stack**: faster, LIFO, for primitive datatype, handle by CPU, lifespan only for func end  
- **Heap**: slower compare to stack, collected by garbage collector, for reference type variable

### Boxing and Unboxing

int x = 10;

object y = x;        // Boxing 

int z = (int)y;      // Unboxing

// In old C\#, ArrayList was stored using boxing, but with generics introduction this issue is solved.

// boxing and unboxing reduces system performance, hence avoid to use it, use generics instead

### Checked and Unchecked Keywords

int.MaxValue = 2147483647

int a = 2147483647

int b = 2147483647

int c = a \+ b

output = -2 //Unexpected (this will be unchecked default behavior)

int c = checked(a+b) // throws error overflow exception 

int c = unchecked(a+b) // output = -2

- **Checked Keyword**: enable overflow checking for int type arithmetic operations and conversion  
- **Unchecked Keyword**: suppress overflow-checking  
- **Overflow**: when the value of any int type exceeds its range

---

## .NET Architecture & Compilation

### .NET Framework Evolution

- **Before .NET**: COM Framework  
    
  - **Component Object Model**  
  - VB language for windows applications  
  - ASP (Active Server Pages) language for web applications  
  - **Disadvantages**: No total OOPs implementation & Windows OS only supported


- **.NET**: Network Enabled Technology (Internet)  
    
  - **BCL (Base class library)**: framework provided libraries  
  - **CLR (Common Language Runtime)**: MSIL (Microsoft Intermediate Language) Code ---\> Native Machine language code  
    - provides functions like thread management, garbage collections, type safety, exception handling, etc

### Compilation Process

Source Code (.cs)

    ↓  \[C\# Compiler (csc.exe)\]

CIL/IL Code (.exe/.dll with metadata)

    ↓  \[Loaded by CLR\]

    ↓  \[JIT Compiler (RyuJIT)\]

Native Machine Code (CPU-specific)

    ↓

Execution on the Machine

When code runs on machine, when method calls, CLR will get method to JIT compiler from IL or dll and then convert it into machine code, but for AOT IL to machine code will happen on build time only, so AOT app will be faster.

**AOT Limitations:**

- Reflection and dynamic features has limited support  
- Increased build times  
- Large native executables

### .NET Components

- **COM Framework - VB**  
- **NET**: through Object-Oriented we can implement internet-based applications  
- **BCL (Base Class Libraries)**: provides types for strings, dates, numbers, etc. The Class Library includes APIs for reading and writing files, connecting to databases, drawing, and more.  
- **CLR (Common Language Runtime)**: the execution engine that handles running applications. It provides services like thread management, garbage collection, type safety, exception handling, and more.

### Viewing IL Code

In CMD: `ILDASM` - this is used to see Intermediate Language Code (IL) for any DLL or EXE

---

## Object-Oriented Programming (OOP)

### OOP Principles

**Benefits**: Reusability, Extensibility, Simplicity, and Maintainability

#### 1\. Encapsulation

- Process of defining a class by "hiding its internal data members from outside the class" and accessing those internal data members only through publicly exposed methods or properties  
- Also called data hiding

**Access Modifiers:**

- **Private**: only accessible within class  
- **Public**: accessible to anyone  
- **Protected**: accessible within class and subclass  
- **Internal**: within the same assembly or library  
- **Protected Internal**: within the same assembly (due to internal) \+ from derived classes in any assembly (from outside, but just to subclasses due to protected)  
- **Private Protected**: within the same assembly and from derived classes inside the same assembly

#### 2\. Inheritance

- Process by which the members of one class are transferred to another class  
- **Parent/Base/Superclass**: class from which members are transferred  
- **Derived/Child/Subclass**: class that inherits the Parent/Base/Superclass members  
- Achieve code extensibility through inheritance

#### 3\. Polymorphism

- Ability to take more than one form  
- Same function/operator shows different behaviors by taking different types of values or with different number of values

**Types:**

1. **Static polymorphism/compile-time polymorphism/Early binding**  
2. **Dynamic polymorphism/Run time polymorphism/Late binding**

#### 4\. Abstraction

- Process of "representing the essential features" without including the background details  
- **Abstraction and Encapsulation are related**: Abstraction is logical thinking, whereas Encapsulation is its physical implementation

### Special Class Types

1. **Abstract Class**  
2. **Sealed Class**  
3. **Partial Class**  
4. **Static Class**

### Constructors

- Special type of method  
- Called when we create instance of class  
- Used to initialize variables  
- Cannot return value  
- Same name as class  
- If we don't define parameterless constructor but have one with parameter, default parameterless constructor will not be created by compiler

#### Copy Constructor

public class Person

{

    public string Name;

    public int Age;

    // Parameterized constructor

    public Person(string name, int age)

    {

        Name = name;

        Age = age;

    }

    // Copy constructor

    public Person(Person other)

    {

        Name = other.Name;

        Age = other.Age;

    }

}

Person original = new Person("Akshay", 30);

Person copy = new Person(original);

#### Static Constructors

- Responsible for initializing static variables  
- Never called explicitly - called Implicitly  
- First to execute in any class  
- Cannot be parameterized, so overloading is not possible

#### Private Constructor

- When a class contains a private constructor, we cannot create an object for the class outside of the class  
- Used to create an object for the class within the same class  
- Using Private Constructor in C\# we can implement the singleton design pattern  
- Not possible to create an instance from outside the class

#### Destructor (Finalizer)

\~ClassName(){

    // Cleanup code

}

- Implicitly Called when the object of the class is destroyed  
- Do not have access modifier

### Fields vs Properties

public class Car

{

    private int speed;  // Backing field

    public int Speed {get; set;}   // Property --\> preferred one

}

### Static Classes

- Can have static constructor, parameterless, access-modifier less  
- Cannot inherit or do OOPs concept on static class  
- Have all properties and methods as static

### String Null Handling

string testStr = null;

testStr.ToString()        // will throw NullValueException

Convert.ToString(testStr) // will not throw error

---

## Advanced C\# Concepts

### Extension Methods

- Add functionality to existing types without modifying them  
- Must be static methods in static classes  
- Use `this` keyword for the first parameter

### Partial Classes and Methods

- **Partial Class**: Split class definition across multiple files  
- **Partial Method**: Method declaration in one part, implementation in another

### Anonymous Types

var student = new { Id = 1, FirstName = "James", LastName = "Bond" };

// Only Public read only properties, var is used to hold values

### Dynamic vs Var

- **var**: compile-time, type safe, intellisense full support, faster performance, excellent for anonymous types in LINQ  
- **dynamic**: runtime, errors only at runtime, no type safety, limited intellisense support, slower due to late binding, not ideal for LINQ

### Reflection

- Determines or inspects the content of an assembly  
- Can dynamically create an instance of a type, bind the type to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties  
- Useful when we don't know if a method is present or not

### Volatile Keyword

- Rarely used in multi-threaded applications for better concurrency handling  
- Indicates that a field might be modified by multiple threads executing at the same time  
- Fields declared volatile are excluded from certain kinds of optimizations

### Indexers

- Access class properties in array-like style  
- Useful when class looks like collection or map-like structure

public class StudentRegistry

{

    private Dictionary\<int, string\> students = new();

    public string this\[int id\]

    {

        get =\> students.ContainsKey(id) ? students\[id\] : "Unknown";

        set =\> students\[id\] = value;

    }

}

StudentRegistry registry = new();

registry\[101\] = "Akshay";

Console.WriteLine(registry\[101\]);  // Output: Akshay

### Parameters

#### ref and out keywords

int addition = 0;

int substr = 0; // initialization required for ref type 

// ref parameter

public static void MathRef(int a, int b, ref int addition, ref int substr){

    addition = a \+ b;

}

// out parameter

public static void MathOut(int a, int b, out int addition, out int substr){

    addition = a \+ b;

    // substr = a - b; // necessary in out, must be assigned before control leaves

}

#### Named Parameters

employee.AddEmployeeInfo(IsPermanent: true, Name: "Pranaya", EmpID: 1001, Department:"IT", Mobile:"1234567890" );

public void AddEmployeeInfo(long EmpID, string Name, bool IsPermanent, string Mobile, string Department)

---

## Collections & Generics

### Collection Types

- **Array vs ArrayList**: Arrays are fixed-size, type-safe; ArrayList is dynamic but not type-safe  
- **Collection vs Generics**: Generics provide type safety and better performance  
- **Why avoid HashTables**: Not type-safe, performance issues with boxing/unboxing

### IEnumerable vs IEnumerator vs IQueryable

- **IEnumerable**: Forward-only cursor, in-memory filtering  
- **IEnumerator**: Interface for iterating through collection  
- **IQueryable**: Designed for out-of-memory data sources (like databases), supports expression trees

### Common Collection Classes

- \*\*List\*\*: Dynamic array  
- **Dictionary\<TKey, TValue\>**: Key-value pairs  
- \*\*HashSet\*\*: Unique elements  
- \*\*Queue\*\*: FIFO  
- \*\*Stack\*\*: LIFO

### Immutable Types

- **Immutable**: objects whose state cannot be modified after creation  
- **Advantages**: Thread safety, predictable behavior  
- **Disadvantages**: Performance overhead for frequent modifications

---

## Delegates, Events & Lambda Expressions

### Delegates

- Type-safe function pointers  
- Hold reference to methods with specific signatures  
- Can invoke methods through delegates

Event \<----\>(through delegates pipeline) \<----\> Event Handler (Event Listener)

#### Custom Delegate

public delegate void WorkPerformedHandler(int hours, WorkType workType);

#### Types of Delegates

1. **Single Cast Delegate**: invoking single method  
2. **Multicast Delegate**: invoke multiple methods

#### Generic Delegates

| Delegate | Parameters | Return Type | Use Case |
| :---- | :---- | :---- | :---- |
| Func | 0–16 | Required | When a method returns a value |
| Action | 0–16 | void | When a method performs an action but returns nothing |
| Predicate | 1 | bool | When a method returns a boolean (often used for filtering) |

Func\<int, int, int\> add = (a, b) =\> a \+ b;

Console.WriteLine(add(3, 4)); // Output: 7

Action\<string\> greet = name =\> Console.WriteLine($"Hello, {name}\!");

greet("Alice"); // Output: Hello, Alice\!

Predicate\<string\> isLong = s =\> s.Length \> 5;

Console.WriteLine(isLong("Copilot")); // Output: True

### Events

- **EventArgs**: containers for passing data  
- **Event Handler**: method responsible for receiving and processing data from delegate  
- Has two parameters: Sender (object) and EventArgs object

this.button1.Click \+= new System.EventHandler(this.button1\_Click);  //delegate 

private void button1\_Click(object sender, EventArgs e){            //Event handler 

    // Handle event

}

### Anonymous Methods

#### **Interview Perspective: Anonymous Methods**

**Core Concept**: Anonymous methods were introduced in C# 2.0 as a way to create inline delegate instances without naming the method. They were the precursor to lambda expressions.

#### **Anonymous Methods vs Lambda Expressions**

```csharp
// Anonymous Method (C# 2.0 style)
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

var evensOld = numbers.FindAll(delegate(int number) {
    return number % 2 == 0;
});

// Lambda Expression (C# 3.0+ style - preferred)
var evensNew = numbers.Where(n => n % 2 == 0);
```

#### **Anonymous Method Syntax and Features**

```csharp
// Basic anonymous method
delegate void MyDelegate(string message);

MyDelegate del = delegate(string msg) {
    Console.WriteLine(msg);
};

// Anonymous method with no parameters
delegate void SimpleDelegate();

SimpleDelegate simpleDel = delegate {
    Console.WriteLine("No parameters!");
};

// Anonymous method accessing outer variables (closure)
string message = "Hello";
MyDelegate closureDel = delegate(string msg) {
    Console.WriteLine($"{message} {msg}"); // Captures outer variable
};
```

#### **Key Characteristics**

1. **No Method Name**: Defined inline using `delegate` keyword
2. **Parameter List**: Optional if no parameters needed
3. **Closure Support**: Can access outer variables
4. **Delegate Compatibility**: Can be assigned to any compatible delegate

#### **Common Use Cases**

```csharp
// Event handling
button.Click += delegate(object sender, EventArgs e) {
    MessageBox.Show("Button clicked!");
};

// Thread start
Thread thread = new Thread(delegate() {
    Console.WriteLine("Thread running");
});
thread.Start();

// Collection operations
List<string> names = new List<string> { "Alice", "Bob", "Charlie" };
var shortNames = names.FindAll(delegate(string name) {
    return name.Length <= 4;
});
```

#### **Interview Questions**

1. **Q: What is the difference between anonymous methods and lambda expressions?**
   - Anonymous methods: `delegate keyword`, more verbose
   - Lambda expressions: `=> operator`, more concise, preferred
   - Both can create closures and access outer variables

2. **Q: When would you still use anonymous methods over lambdas?**
   - When you need to omit parameter list completely
   - Legacy codebases (C# 2.0)
   - Specific delegate type inference scenarios

3. **Q: Can anonymous methods access outer variables?**
   - Yes, they create closures like lambda expressions
   - Outer variables are captured by reference

4. **Q: What is the performance difference?**
   - Generally similar performance
   - Lambda expressions may have slight compiler optimizations
   - Difference is usually negligible

#### **Evolution Path**

```csharp
// 1. Named method
bool IsEven(int number) { return number % 2 == 0; }
var result1 = numbers.FindAll(IsEven);

// 2. Anonymous method (C# 2.0)
var result2 = numbers.FindAll(delegate(int number) { 
    return number % 2 == 0; 
});

// 3. Lambda expression (C# 3.0+ - preferred)
var result3 = numbers.Where(n => n % 2 == 0);
```

#### **Best Practices**

- Prefer lambda expressions for new code
- Use anonymous methods only when specifically needed
- Understand both for legacy code maintenance
- Consider readability and team familiarity


### Lambda Expressions

- Modern, concise way to define methods without names  
- Preferred for delegates, events, or LINQ queries

List\<int\> numbers = new List\<int\> { 1, 2, 3, 4, 5 };

// Using lambda to filter even numbers

var evens = numbers.Where(n =\> n % 2 == 0);

// Using anonymous method (older style)

var odds = numbers.Where(delegate(int n) {

    return n % 2 \!= 0;

});

---

## Exception Handling

### Custom Exceptions

// Creating our own Exception Class by inheriting Exception class

public class OddNumberException : Exception

{

    // Overriding the Message property

    public override string Message

    {

        get

        {

            return "Divisor Cannot be Odd Number";

        }

    }

}

// Usage

throw new OddNumberException();

### Exception Handling Best Practices

- Use specific exception types  
- Handle exceptions at appropriate levels  
- Use finally blocks for cleanup  
- Log exceptions for debugging

### throw vs throw ex

- **throw**: Preserves original stack trace  
- **throw ex**: Resets stack trace to current location

---

## File I/O & Serialization

### File Operations

- Reading and writing text files  
- Binary file operations  
- Directory manipulation

### Serialization and Deserialization

- **JSON Serialization**: Convert objects to JSON strings and back  
- **Binary Serialization**: Convert objects to binary format  
- **XML Serialization**: Convert objects to XML format

// JSON serialization example

string json = JsonSerializer.Serialize(obj);

MyClass obj = JsonSerializer.Deserialize\<MyClass\>(json);

---

## LINQ (Language Integrated Query)

### LINQ Providers

- **LINQ to Objects**: In-memory collections  
- **LINQ to SQL**: Database queries  
- **LINQ to XML**: XML document manipulation  
- **LINQ to Entities**: Entity Framework

### Common LINQ Methods

// Filtering

var result = collection.Where(x =\> x.Property \> value);

// Projection

var result = collection.Select(x =\> new { x.Property1, x.Property2 });

// Aggregation

var count = collection.Count();

var sum = collection.Sum(x =\> x.Value);

// Ordering

var ordered = collection.OrderBy(x =\> x.Property);

### LINQ Performance Considerations

- Deferred execution  
- Immediate execution with ToList(), ToArray()  
- Performance optimization techniques

---

## Multi-Threading & Asynchronous Programming

### Thread vs Async Await

- **Thread**: Physical OS thread, more resource-intensive  
- **Async/Await**: Cooperative multitasking, more efficient for I/O operations

### Task Parallel Library (TPL)

// Creating and running tasks

Task task = Task.Run(() =\> {

    // Background work

});

// Waiting for completion

await task;

// Multiple tasks

var tasks = new List\<Task\> {

    Task.Run(() =\> Method1()),

    Task.Run(() =\> Method2())

};

await Task.WhenAll(tasks);

### async/await Pattern

public async Task\<string\> GetDataAsync()

{

    var result = await httpClient.GetStringAsync(url);

    return result;

}

### Thread Safety

- Lock statements  
- Monitor class  
- SemaphoreSlim  
- Concurrent collections

---

## Design Patterns

### Gang of Four Patterns

#### Creational Patterns

- **Abstract Factory**: Create families of related objects  
- **Factory Method**: Create objects without specifying exact class  
- **Singleton**: Ensure only one instance exists  
- **Builder**: Construct complex objects step by step  
- **Prototype**: Create new objects by copying existing ones

#### Structural Patterns

- **Adapter**: Convert interface of one class to another  
- **Decorator**: Add new functionality to objects dynamically  
- **Facade**: Provide simplified interface to complex system  
- **Proxy**: Provide surrogate or placeholder for another object

#### Behavioral Patterns

- **Command**: Encapsulate request as an object  
- **Iterator**: Provide sequential access to elements  
- **Observer**: Define one-to-many dependency between objects  
- **Strategy**: Define family of algorithms, encapsulate each one  
- **Template Method**: Define skeleton of algorithm, let subclasses fill in steps

### Factory vs Abstract Factory

- **Factory**: Creates single product  
- **Abstract Factory**: Creates families of related products

### Singleton Implementation

public sealed class Singleton

{

    private static readonly Lazy\<Singleton\> \_instance = 

        new Lazy\<Singleton\>(() =\> new Singleton());

    

    public static Singleton Instance =\> \_instance.Value;

    

    private Singleton() { }

}

### Builder Pattern

public class ProductBuilder

{

    private Product \_product = new Product();

    

    public ProductBuilder WithName(string name)

    {

        \_product.Name = name;

        return this;

    }

    

    public ProductBuilder WithPrice(decimal price)

    {

        \_product.Price = price;

        return this;

    }

    

    public Product Build() =\> \_product;

}

---

## SOLID Principles

### Single Responsibility Principle (SRP)

- A class should have only one reason to change

### Open/Closed Principle (OCP)

- Software entities should be open for extension, closed for modification

### Liskov Substitution Principle (LSP)

- Derived classes must be substitutable for their base classes

### Interface Segregation Principle (ISP)

- Clients should not be forced to depend on interfaces they don't use

### Dependency Inversion Principle (DIP)

- High-level modules should not depend on low-level modules; both should depend on abstractions

---

## Caching Strategies

### In-Memory Cache

- **Description**: Stores data in the application's memory  
- **Best suited for**: Small/medium, not for distributed datasets  
- **Default caching strategy** for most ASP.NET Core projects  
- Each server has their own in memory cache  
- Uses IMemoryCache

// Program.cs

builder.Services.AddMemoryCache();

builder.Services.AddSingleton\<ICacheService, MemoryCacheService\>();

// MemoryCacheService.cs

public class MemoryCacheService : ICacheService

{

    private readonly IMemoryCache \_memoryCache;

    private readonly IConfiguration \_config;

    public MemoryCacheService(IMemoryCache memoryCache, IConfiguration config)

    {

        \_memoryCache = memoryCache;

        \_config = config;

    }

    public T Get\<T\>(string key)

    {

        if (\_memoryCache.TryGetValue(key, out T value))

        {

            return value;

        }

        return default;

    }

    public void Set\<T\>(string key, T value, TimeSpan? expirationTime = null)

    {

        var options = new MemoryCacheEntryOptions();

        if (expirationTime.HasValue)

        {

            options.SetAbsoluteExpiration(expirationTime.Value);

        }

        else

        {

            var mins = \_config\["ColumnFilterCacheExpiry"\];

            if (int.TryParse(mins, out int expiryMins) && expiryMins \> 0\)

            {

                options.SetAbsoluteExpiration(TimeSpan.FromMinutes(expiryMins));

            }

            else

            {

                options.SetAbsoluteExpiration(TimeSpan.FromMinutes(30));

            }

        }

        options.SetPriority(CacheItemPriority.High);

        \_memoryCache.Set(key, value, options);

    }

}

### Distributed Caching

- **Description**: Stores cache data outside the application's process, often in a shared location  
- **Common Use**: Useful for web farms or cloud applications  
- **Examples**: Redis, SQL Server, NCache  
- **.NET Example**: IDistributedCache interface

#### Redis Implementation

// Install package

dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis

// Program.cs

builder.Services.AddStackExchangeRedisCache(options =\>

{

    options.Configuration = builder.Configuration.GetConnectionString("RedisConnection");

    options.InstanceName = "Test:";

});

// appsettings.json

"ConnectionStrings": {

    "RedisConnection": "localhost:6379"

}

// DistributedCacheService.cs

public class DistributedCacheService : ICacheService

{

    private readonly IDistributedCache \_cache;

    public DistributedCacheService(IDistributedCache cache) =\> \_cache = cache;

    

    public async Task SetAsync(string key, string value, TimeSpan? expiry = null)

    {

        var options = new DistributedCacheEntryOptions();

        if (expiry.HasValue)

            options.AbsoluteExpirationRelativeToNow = expiry.Value;

        await \_cache.SetStringAsync(key, value, options);

    }

    public async Task\<string?\> GetAsync(string key)

    {

        return await \_cache.GetStringAsync(key);

    }

}

#### Redis Distribution Configuration

- **Redis Clustering**: Distribute data across multiple Redis nodes (shards), providing horizontal scaling and fault tolerance  
- **Redis Sentinel**: High availability solution with automatic failover

### Response Caching

- **Description**: Caches HTTP responses to reduce server load and improve performance  
- **Common Use**: Web APIs and ASP.NET Core MVC  
- **Example**: \[ResponseCache\] attribute in ASP.NET Core

\[ResponseCache(Duration = 60)\]

public IActionResult Get()

{

    //......

}

// In Program.cs

builder.Services.AddResponseCaching();

app.UseResponseCaching();

### Output Caching

- **Description**: Caches the output of controller actions  
- **Common Use**: ASP.NET MVC and Web Forms  
- **Example**: \[OutputCache\] attribute (ASP.NET MVC)

\[OutputCache(Duration = 60)\]

public ActionResult Index()

{

    //....

}

**Key Differences:**

- **Output Caching**: Used in older .NET, done on server side  
- **Response Caching**: Used in .NET Core, done on browser side

---

## Microservices Architecture

### Core Concepts

- **Microservices**: Architectural style that structures an application as a collection of loosely coupled, independently deployable services  
- **Cloud providers**: Azure, AWS, etc.  
- **Containerization**: Docker and containerization  
- **Orchestration**: Kubernetes for container orchestration  
- **Event-driven architecture**: Message buses and event-driven architecture  
- **API Gateway**: API gateways and service discovery

### Service Communication

#### Synchronous Communication

- **HTTP**: RESTful APIs  
- **gRPC**: High-performance RPC framework

#### Asynchronous Communication (Event Bus)

- **RabbitMQ**: Message broker  
- **Azure Service Bus**: Cloud messaging service  
- **Amazon SQS**: Simple Queue Service  
- **Libraries**: MassTransit or Rebus libraries

### Data Management per Service

- **SQL**: Entity Framework Core  
- **NoSQL**: MongoDB/Redis

### Containerization and Orchestration

- **Docker**: Container platform  
- **Kubernetes (K8s)**: Container orchestration

### Challenges to Consider

- **Complexity**: Managing 20 services is harder than managing one  
- **Data Consistency**: Since each service has its own DB, use Saga Pattern or Eventual Consistency instead of traditional SQL transactions  
- **Observability**: Need centralized logging (ELK Stack or Seq) to track a single request as it travels through multiple services

---

## API Gateway

### YARP (Yet Another Reverse Proxy) - Modern Approach

Microsoft actively supports and recommends YARP for most new projects.

#### YARP vs Ocelot Comparison

| Feature | Ocelot | YARP |
| :---- | :---- | :---- |
| Creator | Open Source Community | Microsoft |
| Performance | Good (Standard Middleware) | Excellent (Built for high-speed proxying) |
| Philosophy | Feature Rich (Built-in Auth, Rate Limiting) | Extensible (Plugs into existing ASP.NET pipeline) |
| Configuration | JSON-based (ocelot.json) | .NET Configuration (appsettings.json or Code) |
| Maintenance | Community-driven (less active) | Actively maintained by Microsoft |

#### YARP Implementation

dotnet add package Yarp.ReverseProxy

// Program.cs

var builder = WebApplication.CreateBuilder(args);

// Load the gateway configuration from appsettings.json

builder.Services.AddReverseProxy()

    .LoadFromConfig(builder.Configuration.GetSection("ReverseProxy"));

var app = builder.Build();

app.MapReverseProxy(); // Enable the proxy middleware

app.Run();

// appsettings.json

{

  "ReverseProxy": {

    "Routes": {

      "orderRoute": {

        "ClusterId": "orderCluster",

        "Match": { "Path": "/orders/{\*\*catch-all}" }

      }

    },

    "Clusters": {

      "orderCluster": {

        "Destinations": {

          "destination1": { "Address": "http://ordering-service:8080/" }

        }

      }

    }

  }

}

#### Why Use API Gateway in .NET?

- **Security (BFF Pattern)**: Centralize JWT validation at the gateway  
- **Rate Limiting**: Protect internal services from being overwhelmed  
- **Request Aggregation**: Combine multiple service responses into one  
- **Protocol Translation**: Accept REST (HTTP/1.1) but talk to internal services via gRPC (HTTP/2)

### Ocelot - Traditional Approach

dotnet add package Ocelot

// Program.cs

builder.Configuration.AddJsonFile("ocelot.json", optional: false, reloadOnChange: true);

builder.Services.AddOcelot();

await app.UseOcelot();

// ocelot.json

{

  "Routes": \[

    {

      "UpstreamPathTemplate": "/gateway/writers",

      "UpstreamHttpMethod": \[ "Get" \],

      "DownstreamPathTemplate": "/api/writers",

      "DownstreamScheme": "https",

      "DownstreamHostAndPorts": \[

        {

          "Host": "localhost",

          "Port": 5002

        }

      \]

    }

  \],

  "GlobalConfiguration": {

    "BaseUrl": "https://localhost:5003"

  }

}

#### Ocelot Key Features

- **Rate Limiting**: Restrict client calls within time window  
- **Caching**: Cache responses to reduce load  
- **Request Aggregation**: Combine multiple microservice responses  
- **Authentication**: Centralize JWT validation

---

## gRPC Communication

### Overview

- **gRPC (Google Remote Procedure Call)**: Gold standard for internal service-to-service communication  
- **REST**: Great for public-facing APIs (browsers understand it easily)  
- **gRPC**: Designed for raw speed, efficiency, and strict contracts between services

### Performance Comparison

| Feature | REST (HTTP/1.1) | gRPC (HTTP/2) |
| :---- | :---- | :---- |
| Data Format | Text (JSON/XML) - bulky | Binary (Protobuf) - tiny and fast |
| Payload Size | Large (includes keys like "name") | Small (uses numeric tags instead of keys) |
| Multiplexing | Requests are sequential (blocking) | Parallel (multiple requests on 1 connection) |
| Streaming | Client-to-Server only | Bi-directional (real-time flow) |
| Contract | Optional (Swagger/OpenAPI) | Strict (Defined in .proto files) |

### HTTP/2 Advantages

- **Multiplexing**: Multiple requests/responses over single TCP connection  
- **Header Compression (HPACK)**: Compress repetitive headers  
- **Server Push**: Server can proactively send data to client's cache

### gRPC Implementation in .NET 8

#### Step 1: Define Contract (.proto file)

syntax = "proto3";

option csharp\_namespace = "MyMicroservice.Grpc";

service Greeter {

  // A Unary (Single request/response) call

  rpc SayHello (HelloRequest) returns (HelloReply);

}

message HelloRequest {

  string name = 1; // '1' is the binary tag, not the value

}

message HelloReply {

  string message = 1;

}

#### Step 2: Server Implementation

public class GreeterService : Greeter.GreeterBase

{

    public override Task\<HelloReply\> SayHello(HelloRequest request, ServerCallContext context)

    {

        return Task.FromResult(new HelloReply { 

            Message = "Hello " \+ request.Name 

        });

    }

}

#### Step 3: Register in Program.cs

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddGrpc(); // Register gRPC services

var app = builder.Build();

app.MapGrpcService\<GreeterService\>(); // Map the service

app.Run();

### When to Use Which?

- **Use gRPC for**: Internal communication between .NET microservices, high-performance real-time data (stock tickers, chat), environments with limited bandwidth (IoT/Mobile)  
- **Use REST for**: Public APIs, browser-based clients, scenarios where human-readability (JSON) is more important than raw speed

---

## Apache Kafka

### Overview

- **Event-driven approach**  
- **Distributed streaming platform**  
- **Publish-subscribe model**  
- **High throughput, fault tolerance, and event sequencing capabilities**

### Architecture Flow

UI (Angular) --\> API Gateway --\> .NET Producer Service (sends to Kafka) --\> Kafka --\> Consumer Services

Producer(s)               Producer(s)

(send messages)          (send messages)

    |                            |

    v                            v

---------------- Kafka Cluster -------------------

   |          |              |                |              |

 \[Broker\]   \[Broker\]   \[Broker\]   ....   \[Broker\]

    |               |                          |

 Topic A      Topic B                Topic C

(Partition 0\)  (Partition 0\)         (Partition 0\)

(Partition 1\)  (Partition 1\)         (Partition 1\)

(across brokers)  (across brokers)   (across brokers)

### Components

- **Producers**: Create events/data messages and publish to specific topics  
- **Kafka Cluster**: Multiple brokers with load balancing, stores messages  
- **Partitions**: Divide topics across multiple brokers for replication and parallelism  
- **Zookeeper**: Manages and coordinates Kafka brokers, handles leader elections  
- **Consumers**: Subscribe to topics, consume and process data, organized in consumer groups

### Design Patterns in Kafka

- **GOF 23 Design Pattern**  
- **DRY (Don't Repeat Yourself)**  
- **KISS (Keep It Simple, Stupid)**  
- **MVC (Model-View-Controller)**  
- **Repository pattern**  
- **Unit of Work pattern**

---

## Resilience & Rate Limiting

### Polly v8 - Resilience Pipelines

- More performant and integrates natively with .NET 8/9 through "Microsoft.Extensions.Http.Resilience" package

#### Core Patterns

- **Retry**: Automatically tries an operation again when it fails (best for "blips")  
- **Circuit Breaker**: "Trips" and stops all requests to a failing system for a set time to prevent overloading

#### Implementation

\# Install NuGet Package

dotnet add package Microsoft.Extensions.Http.Resilience

// Program.cs

builder.Services.AddHttpClient("MyService")

.AddStandardResilienceHandler(options =\>

{

    // Customize the Retry strategy

    options.Retry.MaxRetryAttempts = 3;

    options.Retry.BackoffType = DelayBackoffType.Exponential;

    

    // Customize the Circuit Breaker strategy

    options.CircuitBreaker.FailureRatio = 0.5; // Break if 50% of requests fail

    options.CircuitBreaker.SamplingDuration = TimeSpan.FromSeconds(30);

});

#### Retry vs Circuit Breaker

| Feature | Retry | Circuit Breaker |
| :---- | :---- | :---- |
| Goal | Fixes transient errors | Prevents cascading failures |
| Analogy | Try calling again in a second | Stop calling; the line is down |
| When to use | Network glitches, 503 errors | Total service outages, high latency |
| Risk | Can hammer a dying service | Prevents any calls (even if it might work) |

#### Circuit Breaker State Machine

- **Closed**: Normal operation; requests flow through  
- **Open**: Failure threshold reached; requests fail fast immediately without calling the service  
- **Half-Open**: Waiting period ended; allows a trial request to see if the service recovered

### Rate Limiting

- Built-in middleware that restricts the number of requests a user or client can make to your API within a specific timeframe  
- Critical security and stability feature to prevent "noisy neighbors," brute-force attacks, and server resource exhaustion

#### Four Core Algorithms

1. **Fixed Window**  
     
   - Uses a fixed time frame (e.g., 10 requests every 1 minute)  
   - Counter resets exactly at the end of that window  
   - Best for: Simple API limits where "bursts" at the very end aren't a major concern

   

2. **Sliding Window**  
     
   - Divides time frame into segments, "slides" forward  
   - Prevents double-burst of traffic at boundary of fixed reset  
   - Best for: Preventing spikes at the turn of the minute

   

3. **Token Bucket**  
     
   - Tokens added to "bucket" at steady rate, each request consumes one token  
   - Allows occasional bursts while maintaining consistent average rate  
   - Best for: Allowing bursts of high traffic over long term

   

4. **Concurrency**  
     
   - Limits number of simultaneous active requests  
   - Doesn't look at time at all  
   - Best for: Protecting heavy-processing endpoints (image manipulation, PDF generation)

#### Implementation

// Program.cs

using System.Threading.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRateLimiter(options =\>

{

    // Define a "Fixed Window" policy named "fixed"

    options.AddFixedWindowLimiter(policyName: "fixed", opt =\>

    {

        opt.PermitLimit = 5;            // Max 5 requests

        opt.Window = TimeSpan.FromSeconds(10); // Per 10 seconds

        opt.QueueLimit = 2;             // Queue up to 2 extra requests

        opt.QueueProcessingOrder = QueueProcessingOrder.OldestFirst;

    });

    // Optional: Customize the rejection response

    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

});

var app = builder.Build();

app.UseRateLimiter(); // Must be called after UseRouting

app.MapGet("/api/data", () =\> Results.Ok("Success\!"))

   .RequireRateLimiting("fixed"); // Apply the policy here

app.Run();

#### Global vs. Partitioned Limits

- **Global Limiter**: Limits every single request coming to the server, regardless of who is calling  
- **Partitioned Limiter**: Most common approach, limits requests per User ID or per IP Address

#### Tiered Rate Limiting Example

builder.Services.AddRateLimiter(options =\>

{

    options.AddPolicy("TieredPolicy", httpContext =\>

    {

        // 1\. Identify the user (e.g., via a claim in a JWT)

        var userTier = httpContext.User.FindFirst("tier")?.Value ?? "Free";

        var userName = httpContext.User.Identity?.Name ?? "Anonymous";

        // 2\. Return a partition based on the user's name/ID

        if (userTier == "Premium")

        {

            return RateLimitPartition.GetFixedWindowLimiter(

                partitionKey: userName,

                factory: \_ =\> new FixedWindowRateLimiterOptions

                {

                    PermitLimit = 100, // High limit

                    Window = TimeSpan.FromMinutes(1)

                });

        }

        return RateLimitPartition.GetFixedWindowLimiter(

            partitionKey: userName,

            factory: \_ =\> new FixedWindowRateLimiterOptions

            {

                PermitLimit = 10, // Low limit

                Window = TimeSpan.FromMinutes(1)

            });

    });

    // Custom response for blocked users

    options.OnRejection = async (context, token) =\>

    {

        context.HttpContext.Response.StatusCode = 429;

        await context.HttpContext.Response.WriteAsync("Rate limit exceeded. Upgrade to Premium for more requests\!", token);

    };

});

#### User Tier Comparison

| User | Requests Allowed | Period | Benefit |
| :---- | :---- | :---- | :---- |
| Free | 10 | 1 Minute | Basic access, prevents server abuse |
| Premium | 100 | 1 Minute | High-speed integration for power users |
| Anonymous | 5 | 1 Minute | Highly restrictive to prevent bot scraping |

#### Best Practices

- **Use Redis for Distributed Apps**: For multiple server instances, use Redis-based rate limiting  
- **Inform the Client**: Send headers back so developers know their status (X-RateLimit-Remaining)  
- **Combine with Polly**: Use .NET Rate Limiting for incoming requests, Polly for outgoing HttpClient calls

---

## Web API & MVC

### ASP.NET MVC Fundamentals

#### MVC Lifecycle

1. **Request**: User makes request  
2. **Routing**: Route table determines controller/action  
3. **Controller**: Controller instantiated, action executed  
4. **Model**: Data preparation  
5. **View**: Response rendered  
6. **Response**: Sent to client

#### Key Components

- **Controller**: Handles user input, interacts with model  
- **View**: Renders UI  
- **Model**: Represents data and business logic

#### Data Passing

- **Controller to View**: ViewBag, ViewData, TempData, Model  
- **View to Controller**: Form collection, route parameters, query strings

#### TempData

- **Peek**: Retrieve value without removing it  
- **Keep**: Preserve value for next request

#### Filters

- **Authorization Filters**: Authentication and authorization  
- **Action Filters**: Before/after action execution  
- **Result Filters**: Before/after result execution  
- **Exception Filters**: Handle unhandled exceptions

#### Custom Filters

public class CustomActionFilter : ActionFilterAttribute

{

    public override void OnActionExecuting(ActionExecutingContext context)

    {

        // Before action execution

        base.OnActionExecuting(context);

    }

    

    public override void OnActionExecuted(ActionExecutedContext context)

    {

        // After action execution

        base.OnActionExecuted(context);

    }

}

### Routing

#### Convention Routing vs Attribute Routing

- **Convention Routing**: Defined in RouteConfig  
- **Attribute Routing**: Defined on controllers/actions with attributes

#### Route Configuration

// Convention routing

routes.MapRoute(

    name: "Default",

    url: "{controller}/{action}/{id}",

    defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }

);

// Attribute routing

\[Route("api/\[controller\]")\]

public class ProductsController : Controller

{

    \[HttpGet("{id}")\]

    public IActionResult GetProduct(int id)

    {

        // Implementation

    }

}

### Areas

- Organize large applications into functional modules  
- Each area has its own controllers, views, and models

### Exception Handling

- **OnException**: Handle exceptions at controller level  
- **HandleError Attribute**: Global exception handling  
- **Application\_Error Event**: Application-level error handling

### State Management

- **Session**: Per-user data storage  
- **Application**: Application-wide data storage  
- **Cache**: Performance optimization

### Anti-Forgery Tokens

@Html.AntiForgeryToken()

// In controller

\[ValidateAntiForgeryToken\]

public ActionResult PostAction(FormCollection form)

{

    // Implementation

}

### Web API Security

- **Authentication**: Verify identity  
- **Authorization**: Verify permissions  
- **OWIN Package**: Security middleware

---

## Database & Entity Framework

### Entity Framework Core

#### Database First vs Code First

- **Database First**: Generate model from existing database  
- **Code First**: Generate database from model classes

#### Adding New Columns

// 1\. Add property to model

public class User

{

    public int Id { get; set; }

    public string Name { get; set; }

    public string NewColumn { get; set; } // New property

}

// 2\. Add migration

dotnet ef migrations add AddNewColumnToUser

// 3\. Apply migration

dotnet ef database update

### Stored Procedures vs Functions

#### Stored Procedures

- Can return multiple result sets  
- Can perform DML operations (INSERT, UPDATE, DELETE)  
- Better for complex business logic

#### Functions

- Must return single result set  
- Cannot perform DML operations  
- Better for calculations and data transformation

### Optimization Techniques

- **SQL Profiler**: Analyze query performance  
- **Indexes**: Improve query speed  
- **Triggers**: Automated actions  
- **SET NOCOUNT**: Reduce network traffic (but has disadvantages)

### Table Variables vs Temp Tables

- **Table Variables**: Scope limited to batch/procedure, less logging  
- **Temp Tables**: Can have indexes, better for large datasets

---

## Testing & Development Practices

### Unit Testing Frameworks

- **MSTest**: Microsoft's testing framework  
- **NUnit**: Open-source testing framework  
- **xUnit**: Modern, extensible testing framework

### Test-Driven Development (TDD)

1. **Red**: Write failing test  
2. **Green**: Write minimal code to pass test  
3. **Refactor**: Improve code while keeping tests green

### Assertion Functions

// Example assertion signature

Assert.AreEqual(expected, actual);

Assert.IsTrue(condition);

Assert.IsNotNull(object);

### EF In-Memory Databases

- Useful for unit testing database operations  
- Fast, isolated test environment

### Integration Testing

- Test multiple components working together  
- Use test databases or mock services

---

## Performance Optimization

### LINQ Performance

- **Deferred Execution**: Queries execute when enumerated  
- **Immediate Execution**: Use ToList(), ToArray() to execute immediately  
- **Optimization**: Use appropriate methods, avoid N+1 queries

### Memory Management

- **Garbage Collection**: Automatic memory management  
- **IDisposable**: Proper cleanup of unmanaged resources  
- **Using Statement**: Ensure resource disposal

### Async Programming Benefits

- **Responsiveness**: UI remains responsive during I/O operations  
- **Scalability**: Better resource utilization  
- **Performance**: More efficient than blocking calls

### Caching Strategies

- **In-Memory Cache**: Fast, single-server  
- **Distributed Cache**: Scalable, multi-server  
- **Response Caching**: Reduce server load

---

## Security

### Authentication vs Authorization

- **Authentication**: Who are you?  
- **Authorization**: What can you do?

### Web API Security Mechanisms

- **JWT Tokens**: Stateless authentication  
- **OAuth 2.0**: Authorization framework  
- **API Keys**: Simple authentication

### Common Security Threats

- **SQL Injection**: Parameterized queries, ORMs  
- **XSS (Cross-Site Scripting)**: Input validation, output encoding  
- **CSRF (Cross-Site Request Forgery)**: Anti-forgery tokens

### Security Best Practices

- **Principle of Least Privilege**: Minimum necessary permissions  
- **Input Validation**: Validate all user input  
- **Secure Communication**: HTTPS, secure headers

---

## Deployment & DevOps

### Git Version Control

- **Undo Changes**: `git checkout -- file` or `git reset --hard HEAD`  
- **Branching**: Feature branches, release branches  
- **Merging**: Combine changes from different branches

### Deployment Pipeline

1. **Source Control**: Git repository  
2. **Build**: Compile code, run tests  
3. **Package**: Create deployment artifacts  
4. **Deploy**: Push to various environments  
5. **Monitor**: Track application health

### Cloud Services

#### Azure Benefits

- **Scalability**: Easy scaling up/down  
- **Reliability**: High availability, backup services  
- **Managed Services**: Reduced maintenance overhead  
- **Integration**: Seamless integration with Microsoft tools

#### Cloud Computing Models

- **IaaS**: Infrastructure as a Service  
- **PaaS**: Platform as a Service  
- **SaaS**: Software as a Service

### Docker Containerization

- **Benefits**: Consistent environments, easy deployment  
- **Dockerfile**: Instructions for building image  
- **Docker Compose**: Multi-container applications

### CI/CD Practices

- **Continuous Integration**: Automated builds and tests  
- **Continuous Deployment**: Automated releases  
- **Infrastructure as Code**: Manage infrastructure with code

---

## Additional Study Topics

### Search Algorithms

1. **Linear Search**: O(n) - checks each element sequentially  
2. **Binary Search**: O(log n) - works on sorted datasets  
3. **Depth-First Search (DFS)**: explores branches before backtracking  
4. **Breadth-First Search (BFS)**: explores all neighbors at current depth  
5. **Hashing**: Direct key-to-index mapping for quick retrieval  
6. **Interpolation Search**: Estimates position based on uniform distribution  
7. **Best-First Search**: Uses heuristics to prioritize paths  
8. *A Search*\*: Combines BFS and DFS using heuristics

### Data Structures

- **Arrays**: Fixed-size, indexed collection  
- **Linked Lists**: Dynamic size, sequential access  
- **Stacks**: LIFO principle  
- **Queues**: FIFO principle  
- **Trees**: Hierarchical data structure  
- **Graphs**: Network of nodes and edges  
- **Hash Tables**: Key-value pairs with fast lookup

### JavaScript Integration

- **String Concatenation**: `+` operator or template literals  
- **Rest Operator**: `...` syntax for function parameters  
- **Equality**: `==` vs `===` (type coercion vs strict equality)  
- **Context**: `this` keyword behavior

### HTTP Methods

- **GET**: Retrieve data  
- **POST**: Create new resource  
- **PUT**: Update entire resource  
- **PATCH**: Partial update  
- **DELETE**: Remove resource

### HTTP Status Codes

- **2xx**: Success (200, 201, 204\)  
- **3xx**: Redirection (301, 302\)  
- **4xx**: Client errors (400, 401, 404, 429\)  
- **5xx**: Server errors (500, 502, 503\)

### Development Methodologies

- **Waterfall**: Sequential, linear development  
- **Agile**: Iterative, flexible approach  
- **Scrum**: Agile framework with sprints  
- **Kanban**: Visual workflow management

---

## Quick Reference

### C\# Keywords Summary

| Keyword | Purpose | Example |
| :---- | :---- | :---- |
| `var` | Implicit typing | `var x = 5;` |
| `dynamic` | Runtime typing | `dynamic x = GetObject();` |
| `ref` | Pass by reference | `void Method(ref int x)` |
| `out` | Output parameter | `void Method(out int x)` |
| `this` | Current instance | `this.Property = value;` |
| `base` | Base class | `base.Method();` |
| `sealed` | Prevent inheritance | `sealed class MyClass` |
| `abstract` | Must be inherited | `abstract class MyClass` |
| `virtual` | Can be overridden | `virtual void Method()` |
| `override` | Override base method | `override void Method()` |
| `static` | Class-level member | `static int Count;` |
| `readonly` | Runtime constant | `readonly int Value;` |
| `const` | Compile-time constant | `const int Max = 100;` |
| `async` | Asynchronous method | `async Task Method()` |
| `await` | Wait for async | `var result = await Method();` |
| `yield` | Iterator support | `yield return item;` |
| `typeof` | Get type | `Type t = typeof(string);` |
| `is` | Type checking | `if (obj is string)` |
| `as` | Safe casting | `var s = obj as string;` |
| `checked` | Overflow checking | `checked(x + y)` |
| `unchecked` | No overflow check | `unchecked(x + y)` |
| `lock` | Thread synchronization | `lock(obj) { ... }` |
| `volatile` | Thread-safe field | `volatile int flag;` |
| `unsafe` | Pointer code | `unsafe void Method()` |
| `fixed` | Pin managed object | `fixed(int* p = &array[0])` |

### Common Design Patterns Quick Reference

| Pattern | Purpose | When to Use |
| :---- | :---- | :---- |
| **Singleton** | One instance only | Configuration managers, logging |
| **Factory** | Object creation | When creation logic is complex |
| **Observer** | Event notification | UI updates, event systems |
| **Strategy** | Algorithm selection | When multiple algorithms exist |
| **Command** | Encapsulate request | Undo/redo, macro operations |
| **Adapter** | Interface conversion | Legacy system integration |
| **Decorator** | Add functionality | Dynamic feature addition |
| **Facade** | Simplify interface | Complex subsystems |
| **Repository** | Data access abstraction | Database operations |
| **Unit of Work** | Transaction management | Multiple repository operations |

### Performance Tips

1. **Use `StringBuilder`** for string concatenation in loops  
2. **Prefer `List<T>`** over `ArrayList` for type safety  
3. **Use `yield return`** for custom iterators  
4. **Implement `IDisposable`** for unmanaged resources  
5. **Use `async/await`** for I/O operations  
6. **Cache frequently accessed data**  
7. **Use appropriate collections** for specific scenarios  
8. **Avoid boxing/unboxing** with generics  
9. **Profile before optimizing**  
10. **Use connection pooling** for database operations


Generics
Partial types
Anonymous methods
Iterators
Nullable types
Private setters (properties)
Method group conversions (delegates)
Covariance and Contra-variance
Static classes
Implicitly typed local variables
Object and collection initializers
Auto-Implemented properties
Anonymous types
Extension methods
Query expressions
Lambda expressions
Expression trees
Partial Methods
Dynamic binding (late binding)
Named and optional arguments
Generic co- and contravariance
Embedded interop types
Async features
Caller information
Expression Bodied Methods
Auto-property initializer
nameof Expression
Primary constructor
Await in catch block
Exception Filter
String Interpolation
out variables
Tuples
Discards
Pattern Matching
Local functions
Generalized async return types
Readonly members
Default interface methods
Using declarations
Static local functions
Disposable ref structs
Nullable reference types
Records
Init-only properties
Top-level statements
Init accessors and readonly fields
With-expressions
Value-based equality
Record structs
Global using directives
File-scoped namespace declaration
Extended Proptery Patterns
Null Parameter Checking
C source file
Type here to search
ThinkVision

Object and collection initializers
Auto-Implemented properties
Anonymous types
Extension methods
Query expressions
Lambda expressions
Expression trees
Partial Methods
Dynamic binding (late binding)
Named and optional arguments
Generic co and contravariance
Embedded interop types
Async features
Caller information
Expression Bodied Methods
Auto-property initializer
nameof Expression
Primary constructor
Await in catch block
Exception Filter
String Interpolation
out variables
Tuples
Discards
Pattern Matching
Local functions
Generalized async return types
Readonly members
Default interface methods
Using declarations
Static local functions
Disposable ref structs
Nullable reference types
Records
Init-only properties
Top-level statements
Init accessors and readonly fields
With-expressions
Value-based equality
Record structs
Global using directives
File-scoped namespace declaration
Extended Property Patterns
Null Parameter Checking
Constant interpolated strings

//calling exe from code and passing arguments
ProcessStartInfo startInfo = new ProcessStartInfo();
startInfo.FileName = "MyApp.exe";
startInfo.Arguments = "param1 param2";
Process.Start(startInfo);

- A namespace in C# is a way to organize and group related classes, interfaces, structs, enums, and delegates.
- Fields: Simple, direct access, no encapsulation.
- Properties: Encapsulated, indirect access, can include logic for validation and other processing.
---

*This comprehensive study guide covers all major C\# and .NET concepts from basic fundamentals to advanced topics like microservices, caching strategies, and modern development practices. Use this as a reference for interview preparation, project development, or learning new concepts.*  




