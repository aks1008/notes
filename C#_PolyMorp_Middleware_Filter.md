# C# Polymorphism, Middleware, and Filters

## Table of Contents

1. [Polymorphism](#polymorphism)
   - [Definition and Concepts](#definition-and-concepts)
   - [Compile-time Polymorphism (Static)](#compile-time-polymorphism-static)
   - [Run-time Polymorphism (Dynamic)](#run-time-polymorphism-dynamic)
   - [Method Hiding](#method-hiding)
2. [Four Pillars of OOP](#four-pillars-of-oop)
   - [Encapsulation](#encapsulation)
   - [Abstraction](#abstraction)
   - [Inheritance](#inheritance)
   - [Polymorphism](#polymorphism-1)
3. [Different Types of Classes](#different-types-of-classes)
   - [Concrete Class](#concrete-class)
   - [Abstract Class](#abstract-class)
   - [Sealed Class](#sealed-class)
   - [Static Class](#static-class)
   - [Partial Class](#partial-class)
   - [Generic Class](#generic-class)
4. [Lambda Expressions](#lambda-expressions)
5. [ASP.NET Core Middleware](#aspnet-core-middleware)
   - [Middleware Overview](#middleware-overview)
   - [Custom Middleware](#custom-middleware)
   - [Middleware Examples](#middleware-examples)
6. [ASP.NET Core Filters](#aspnet-core-filters)
   - [Filters Overview](#filters-overview)
   - [Filter Types](#filter-types)
   - [Real-time Filter Examples](#real-time-filter-examples)
   - [Custom Filters](#custom-filters)
7. [Extension Methods](#extension-methods)
8. [Code Examples](#code-examples)

## Polymorphism

### Definition and Concepts

Polymorphism in C# is an object-oriented programming concept that allows objects of different types to be treated as objects of a common base type.

Polymorphism in programming means one function or method can work in different ways depending on the object.

It enables a single interface to represent different underlying forms (data types).

Enables code reusability and flexibility.

Supports the open/closed principle (open for extension, closed for modification).

Allows you to write code that works with objects of different types in a uniform way.

### Compile-time Polymorphism (Static)

Achieved through method overloading and operator overloading.

The method to be called is determined at compile time.

#### Example:

```csharp
public class MathUtil
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
}
```

### Run-time Polymorphism (Dynamic)

Achieved through method overriding using `virtual`, `override`, and `abstract` keywords.

The method to be called is determined at runtime based on the actual object type.

#### Example:

```csharp
public class Animal
{
    public virtual void Speak() => Console.WriteLine("Animal speaks");
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine("Dog barks");
}

// Usage:
Animal animal = new Dog();
animal.Speak(); // Output: Dog barks
```

### Method Hiding

Occurs when a derived class defines a method with the same name as a method in its base class, but does not use the `override` keyword. Instead, it uses the `new` keyword to explicitly hide the base class method.

#### Example:

```csharp
public class BaseClass
{
    public void Show()
    {
        Console.WriteLine("BaseClass Show");
    }
}

public class DerivedClass : BaseClass
{
    public new void Show()
    {
        Console.WriteLine("DerivedClass Show");
    }
}

// Usage:
BaseClass b = new DerivedClass();
b.Show(); // Output: BaseClass Show

DerivedClass d = new DerivedClass();
d.Show(); // Output: DerivedClass Show
```

## Four Pillars of OOP

### 1. Encapsulation

#### Definition:
Bundling data (fields) and methods (functions) that operate on the data into a single unit (class), and restricting access.

#### Purpose:
Protects the internal state of an object and only exposes what is necessary.

#### How to do it:
Using access modifiers (private, public, etc.) and properties.

#### Example:

```csharp
public class Person
{
    private string _Name; // Encapsulated field
    public string Name // Public property
    {
        get { return _Name; }
        set { _Name = value; }
    }
}
```

### 2. Abstraction

#### Definition:
Hiding complex implementation details and showing only the essential features of an object.

#### Purpose:
Simplifies code and reduces complexity by exposing only what is necessary.

#### How to do it:
Using abstract classes and interfaces.

#### Example:

```csharp
public interface IShape
{
    double GetArea();
}
```

### 3. Inheritance

#### Definition:
The ability of a class to inherit members (fields, methods, properties) from another class.

#### Purpose:
Promotes code reuse and establishes a relationship between base and derived classes.

#### How in C#:
Using the `:` symbol.

#### Example:

```csharp
public class Animal
{
    public void Eat() { }
}

public class Dog : Animal
{
    public void Bark() { }
}
```

#### Inheritance in detail:

The derived class gets all accessible members of the base class (except constructors and private members).

The derived class can add new members or override virtual/abstract members from the base class.

The `base` keyword is used in a derived class to access members (methods, properties, constructors) of its base (parent) class.

It allows you to:
- Call a base class method that is overridden in the derived class.
- Call a base class constructor from a derived class constructor.
- Access base class properties or fields.

#### Example with base keyword:

```csharp
public class Animal(string name)
{
    Console.WriteLine($"Animal: {name}");
}

public class Dog : Animal
{
    public Dog(string name) : base(name)
    {
        Console.WriteLine("Dog created!");
    }
}
```

### 4. Polymorphism

(See detailed explanation above in the Polymorphism section)

## Different Types of Classes

### Concrete Class

**Definition:** A regular class that can be instantiated.

**Usage:** Used to create objects and implement functionality.

**Example:**
```csharp
public class Car
{
    public void Drive() { }
}
```

### Abstract Class

**Definition:** Cannot be instantiated directly. May contain abstract methods (without implementation) and concrete methods.

**Usage:** Used as a base class to provide a common definition for derived classes.

**Example:**
```csharp
public abstract class Animal
{
    public abstract void Speak();
    public void Eat() { }
}
```

### Sealed Class

**Definition:** Cannot be inherited.

**Usage:** Used to prevent other classes from deriving from it.

**Example:**
```csharp
public sealed class Logger
{
    public void Log(string message) { }
}
```

### Static Class

**Definition:** Cannot be instantiated or inherited. All members must be static.

**Usage:** Used for utility or helper methods.

**Example:**
```csharp
public static class MathHelper
{
    public static int Add(int a, int b) => a + b;
}
```

### Partial Class

**Definition:** Class definition can be split across multiple files.

**Usage:** Useful for large classes or code generation scenarios.

**Example:**
```csharp
// File1.cs
public partial class Person
{
    public string Name { get; set; }
}

// File2.cs
public partial class Person
{
    public int Age { get; set; }
}
```

### Generic Class

**Definition:** Defined with type parameters to work with any data type.

**Usage:** Used for reusable data structures and algorithms.

**Example:**
```csharp
public class Box<T>
{
    public T Value { get; set; }
}
```

## Lambda Expressions

Lambda expressions make code more readable, concise.

#### Without lambda:

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

// Using a named method
bool IsEven(int n) => n % 2 == 0;
```

## ASP.NET Core Middleware

### Middleware Overview

**Middlewares:**
- Middlewares in ASP.NET Core are components that process HTTP requests and responses as they travel through the application pipeline.
- Each middleware can:
  - Inspect, modify, or short-circuit the request/response.
  - Pass control to the next middleware in the pipeline.

### How Do Middlewares Work in the Pipeline?

- The pipeline is a sequence of middleware components.
- Each middleware receives an HttpContext and a reference to the next middleware.
- The order in which you add middlewares in Program.cs (using app.Use...) determines the order of execution.

### A middleware can:
- Do something before passing the request to the next middleware.
- Do something after the next middleware has processed the request.
- Stop the pipeline (e.g., return a response early).

#### Pipeline Flow Example:

```csharp
app.UseMiddleware<MiddlewareA>();
app.UseMiddleware<MiddlewareB>();
app.UseMiddleware<MiddlewareC>();
// Request flows: A -> B -> C -> Controller/Endpoint
```

### Custom Middleware

#### Create custom middleware class:

```csharp
public class CustomLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public CustomLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Code before next middleware
        Console.WriteLine($"Request: {context.Request.Path}");

        await _next(context); // Call the next middleware

        // Code after next middleware
        Console.WriteLine($"Response: {context.Response.StatusCode}");
    }
}
```

#### Register service

**Option 1 - Direct registration in the pipeline:**
```csharp
app.UseMiddleware<CustomLoggingMiddleware>();
```

**Option 2 - Using extension method:**

```csharp
public static class CustomLoggingMiddlewareExtensions
{
    public static IApplicationBuilder UseCustomLogging(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<CustomLoggingMiddleware>();
    }
}

// Then in Program.cs:
app.UseCustomLogging();
```

### Middleware Examples

#### app.Use >>>
- Used to add middleware to pipeline
- Can process request, call next middleware using next(), or do not call next middleware by not calling next()
- Multiple app.Use for chaining middleware

```csharp
app.Use(async (context, next) =>
{
    // Code before next middleware
    await next(); // Call the next middleware
    // Code after next middleware
});
```

#### app.Run >>>
- Adds a terminal middleware component to the pipeline.
- Handles the request and does not call the next middleware. This ends the pipeline.
- Typically used for simple, final request handling or for short-circuiting the pipeline.

## ASP.NET Core Filters

### Filters Overview

Filters in ASP.NET Core provide a way to run code before or after specific stages in the request processing pipeline. They allow you to cross-cut concerns such as authorization, error handling, caching, logging, and more.

### Filter Types

ASP.NET Core provides several types of filters that run at different stages of the request pipeline:

1. **Authorization Filters** - Run first to determine if a user is authorized for the request
2. **Resource Filters** - Run after authorization and can handle requests before the rest of the pipeline
3. **Action Filters** - Run before and after an action method executes
4. **Exception Filters** - Run when an unhandled exception occurs
5. **Result Filters** - Run before and after an action result executes

### Real-time Filter Examples

#### Authorization Filter Examples
- Validate user authentication
- Check user roles and permissions
- Implement custom authorization logic

#### Action Filter Examples
- Log action method execution
- Validate model state before action execution
- Modify action parameters or results
- Implement caching for action results

#### Exception Filter Examples
- Log unhandled exceptions
- Return custom error responses
- Implement global error handling
- Send notifications for critical errors

#### Result Filter Examples
- Modify response headers
- Transform response data
- Implement response caching
- Log response information

#### Resource Filter Examples
- Request/response caching
- Request validation
- Response compression
- Custom request preprocessing

### Custom Filters

You can create custom filters by implementing specific filter interfaces:

#### Synchronous Filter Interfaces:
- `IActionFilter` - For action filters
- `IResultFilter` - For result filters  
- `IExceptionFilter` - For exception filters
- `IResourceFilter` - For resource filters
- `IAuthorizationFilter` - For authorization filters

#### Asynchronous Filter Interfaces:
- `IAsyncActionFilter` - For async action filters
- `IAsyncResultFilter` - For async result filters
- `IAsyncExceptionFilter` - For async exception filters
- `IAsyncResourceFilter` - For async resource filters
- `IAsyncAuthorizationFilter` - For async authorization filters

#### Custom Filter Implementation Example:

```csharp
public class CustomActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Code before action executes
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Code after action executes
    }
}

public class CustomAsyncActionFilter : IAsyncActionFilter
{
    public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        // Code before action executes
        var resultContext = await next();
        // Code after action executes
    }
}
```

### Filter Scope and Order

Filters can be applied at different levels:
- **Global** - Applied to all controllers and actions
- **Controller** - Applied to all actions in a controller
- **Action** - Applied to a specific action method

The order of execution follows this pattern:
1. Global filters
2. Controller filters  
3. Action filters

Within each scope, the order is: Authorization → Resource → Action → Exception → Result

#### Custom Filter Implementation Details

**Example: Output caching for expensive operations.**

**Custom Filters:**

**1. Choose interface**

- `IActionFilter` / `IAsyncActionFilter` - for logic before/after action methods.
- `IResultFilter` / `IAsyncResultFilter` - for logic before/after action results.
- `IExceptionFilter` / `IAsyncExceptionFilter` - for exception handling.
- `IAuthorizationFilter` / `IAsyncAuthorizationFilter` - for authorization logic.
- `IResourceFilter` / `IAsyncResourceFilter` - for logic before/after the rest of the pipeline

**2. Create custom filter**

```csharp
using Microsoft.AspNetCore.Mvc.Filters;
using System.Diagnostics;

public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        Debug.WriteLine($"Action {context.ActionDescriptor.DisplayName} starting.");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        Debug.WriteLine($"Action {context.ActionDescriptor.DisplayName} finished.");
    }
}
```

**3. Register (for applying at global level, means it will be applied to all actions)**

```csharp
builder.Services.AddControllers(options =>
{
    options.Filters.Add<LogActionFilter>();
});
```

Or you can define on action method like below for specific action:
```csharp
[LogActionFilter]
public static void testAction() { }
```

**4. Use**

```csharp
[ServiceFilter(typeof(LogActionFilter))]
public class MyController : ControllerBase
{
    [ServiceFilter(typeof(LogActionFilter))]
    public IActionResult MyAction() { ... }
}
```

**5. Register if required through DI**

```csharp
builder.Services.AddScoped<LogActionFilter>();
```

## Extension Methods

### Extension Method Definition and Rules

**Extension method:**
- Extension methods must be in a static class.
- The method itself must be static.
- The first parameter defines the type being extended, using this.
- They appear as if they are instance methods on the extended type.
- Commonly used in LINQ and utility libraries.

#### Example:

```csharp
public static class StringExtensions
{
    public static bool IsNullOrEmpty(this string str)
    {
        return string.IsNullOrEmpty(str);
    }
}

// Usage:
string name = null;
bool result = name.IsNullOrEmpty(); // true
```

### Lambda Expressions with Extension Methods

```csharp
// Using extension method with lambda
var evenNumbers = numbers.Where(IsEven).ToList();

// Using lambda directly
var evenNumbers = numbers.Where(n => n % 2 == 0).ToList();
```

## Code Examples

### Complete Example with All Concepts

```csharp
using System;
using System.Collections.Generic;

// Compile-time polymorphism
public class MathUtil
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
}

// Run-time polymorphism
public class Animal
{
    public virtual void Speak() => Console.WriteLine("Animal speaks");
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine("Dog barks");
}

// Method hiding
public class BaseClass
{
    public void Show()
    {
        Console.WriteLine("BaseClass Show");
    }
}

public class DerivedClass : BaseClass
{
    public new void Show()
    {
        Console.WriteLine("DerivedClass Show");
    }
}

// Encapsulation
public class Person
{
    private string _Name;
    public string Name
    {
        get { return _Name; }
        set { _Name = value; }
    }
}

// Abstraction
public interface IShape
{
    double GetArea();
}

// Inheritance
public class AnimalBase
{
    public void Eat() { }
}

public class DogInherited : AnimalBase
{
    public void Bark() { }
}

// Constructor inheritance
public class AnimalWithConstructor
{
    public AnimalWithConstructor(string name)
    {
        Console.WriteLine($"Animal: {name}");
    }
}

public class DogWithConstructor : AnimalWithConstructor
{
    public DogWithConstructor(string name) : base(name)
    {
        Console.WriteLine("Dog created!");
    }
}

// Lambda expressions
class Program
{
    static void Main()
    {
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
        
        // Using lambda
        var evenNumbers = numbers.Where(n => n % 2 == 0).ToList();
        
        // Polymorphism example
        Animal animal = new Dog();
        animal.Speak(); // Output: Dog barks
        
        // Method hiding example
        BaseClass b = new DerivedClass();
        b.Show(); // Output: BaseClass Show
        
        DerivedClass d = new DerivedClass();
        d.Show(); // Output: DerivedClass Show
    }
}
```