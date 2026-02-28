## C# Language Features by Version

### C# 2.0 (2005)
- Generics
- Partial types
- Anonymous methods
- Iterators
- Nullable types
- Private setters (properties)
- Method group conversions (delegates)
- Covariance and Contra-variance
- Static classes

### C# 3.0 (2007)
- Implicitly typed local variables
- Object and collection initializers
- Auto-Implemented properties
- Anonymous types
- Extension methods
- Query expressions
- Lambda expressions
- Expression trees
- Partial Methods

### C# 4.0 (2010)
- Dynamic binding (late binding)
- Named and optional arguments
- Generic co- and contravariance
- Embedded interop types

### C# 5.0 (2012)
- Async features
- Caller information

### C# 6.0 (2015)
- Expression Bodied Methods
- Auto-property initializer
- nameof Expression
- Primary constructor
- Await in catch block
- Exception Filter
- String Interpolation

### C# 7.0 (2017)
- out variables
- Tuples
- Discards
- Pattern Matching
- Local functions
- Generalized async return types
- Readonly members
- Default interface methods
- Using declarations
- Static local functions
- Disposable ref structs

### C# 8.0 (2019)
- Nullable reference types
- Records
- Init-only properties
- Top-level statements
- Init accessors and readonly fields
- With-expressions
- Value-based equality
- Record structs
- Global using directives
- File-scoped namespace declaration
- Extended Property Patterns
- Null Parameter Checking
- Constant interpolated strings

### C# 9.0 (2022)
- **Records**: Enhanced records with positional parameters
- **Init-only setters**: Properties that can only be set during initialization
- **Top-level statements**: Simplified program entry point
- **Pattern matching enhancements**: Relational patterns, logical patterns
- **Native-sized integers**: `nint` and `nuint` types
- **Function pointers**: `delegate*<void>` syntax
- **Suppress locals**: `[SkipLocalsInit]` attribute
- **Target-typed `new` expressions**: `new()` without type name
- **Covariant returns**: Covariant return types for overridden methods

### C# 10.0 (2021)
- **Global using directives**: `global using System;`
- **File-scoped namespaces**: `namespace MyNamespace;`
- **Record structs**: Value type records
- **Extended property patterns**: Enhanced pattern matching
- **Lambda improvements**: Attributes on lambda expressions
- **Constant interpolated strings**: `const string s = $"Hello {name}";`
- **Record types**: `record Person(string Name, int Age);`
- **Improved definite assignment**: Better flow analysis
- **AsyncMethodBuilder attribute**: Custom async method builders

### C# 11.0 (2022)
- **Raw string literals**: `"""raw text"""` with multiple lines
- **Generic math support**: `IMath<T>` interface
- **List patterns**: Pattern matching on lists
- **Newlines in string interpolations**: Multi-line interpolated strings
- **Auto-default structs**: Struct fields auto-initialized
- **Required members**: `required string Name;`
- **Span patterns**: Pattern matching on spans
- **File local types**: Types scoped to a file
- **UTF-8 string literals**: `u8"Hello World"`

### C# 12.0 (2023)
- **Primary constructors**: `class Person(string name) { }`
- **Collection expressions**: `[1, 2, 3, 4, 5]` syntax
- **Inline arrays**: `int[] arr = [1, 2, 3];`
- **Optional parameters in lambda expressions**: `(int x = 0) => x * 2`
- **Ref readonly parameters**: `ref readonly` modifier
- **Alias any type**: `using MyInt = System.Int32;`
- **Experimental attributes**: `[Experimental]` feature flagging
- **Interceptors**: Method interception capabilities
- **`params` spans**: `params ReadOnlySpan<T>` support

### C# 13.0 (2024)
- **Extension types**: Adding members to existing types
- **Partial properties**: Properties split across files
- **Overload resolution priority**: Explicit overload priority
- **Params collections**: `params IEnumerable<T>` support
- **Lock objects**: `System.Threading.Lock` type
- **Field keyword in lambdas**: Access to fields in lambda expressions
- **Method group natural types**: Better type inference
- **`ref struct` improvements**: Enhanced ref struct usage
- **Async method builder improvements**: Better async patterns
- **Performance improvements**: JIT and runtime optimizations

### C# 14.0 