### Delegates and Events

**Delegates**: A delegate is a type that safely encapsulates a method, acting as an object that knows how to call it. It defines a method's signature (return type and parameters). You can create an instance of a delegate and assign a compatible method to it. Invoking the delegate instance then calls the assigned method. This decouples the caller from the target method, enabling plug-in architectures and higher-order functions.

**Multicast Delegates**: A single delegate instance can reference multiple target methods. Using the `+` and `+=` operators, you can add methods to a delegate's invocation list. When invoked, all methods are called in the order they were added.

**Generic Delegates**: Delegate types can use generic type parameters, allowing for more flexible and reusable code. The built-in `Func` and `Action` delegates are general-purpose generic delegates that cover most common signatures. `Func` delegates return a value, while `Action` delegates have a `void` return type.

 **Events**: Events formalize the publisher-subscriber pattern using delegates. An event is a member of a class (the publisher) that other classes (subscribers) can register methods with. The publisher "fires" the event to notify all subscribers. Events restrict external access, only allowing subscribers to add (`+=`) or remove (`-=`) their event handlers, preventing them from clearing other subscribers' handlers or firing the event directly. The standard event pattern in .NET uses `System.EventArgs` and the `EventHandler<TEventArgs>` delegate.

### Lambda Expressions and Anonymous Methods

- **Lambda Expressions**: A lambda expression is a concise, unnamed method written in place of a delegate instance. The compiler converts it into a delegate or an expression tree. The syntax is `(parameters) => expression-or-statement-block`. For example, `x => x * x` is a lambda that squares its input. Lambdas can _capture_ outer variables from their enclosing scope, creating a closure.
- **Anonymous Methods**: An older C# 2.0 feature, largely superseded by lambda expressions. They use the `delegate` keyword to define an inline method.

### Exception Handling

- A `try` statement encloses code that might throw an exception. It is followed by one or more `catch` blocks to handle specific exceptions and/or a `finally` block for cleanup code.
- A `catch` block executes if an exception of a matching type is thrown within the `try` block.
- A `finally` block always executes after the `try` (and any `catch`) block, regardless of whether an exception occurred. It ensures that critical cleanup code, like closing file handles, is always run.
- The `using` statement provides a convenient syntax for ensuring that `IDisposable` objects are correctly disposed of, effectively wrapping the code in a `try`/`finally` block.

### Enumeration and Iterators

- **Enumeration**: The `foreach` loop works on _enumerable_ objects, which are types that provide an _enumerator_. An enumerator is a read-only, forward-only cursor over a sequence.
- **Iterators**: An iterator is a method, property, or indexer that uses `yield return` statements to produce a sequence of values lazily. Each `yield return` provides the next element to the consumer. Control returns to the iterator where it left off when the next element is requested. This is highly efficient for creating and composing sequences, as elements are generated on demand.

### Nullability

- **Nullable Value Types**: Value types (like `int`, `double`) cannot normally be `null`. By adding a `?` suffix (e.g., `int?`), you create a nullable value type, which can hold either a value or `null`. This is implemented using the `System.Nullable<T>` struct.
- **Nullable Reference Types**: A C# 8+ feature that, when enabled, helps prevent `NullReferenceException`s. It makes the compiler treat all reference types as non-nullable by default. To allow `null`, you must explicitly mark the type with a `?` (e.g., `string?`). The compiler then issues warnings if you try to assign `null` to a non-nullable type or dereference a nullable type without a null check.

### Other Key Features

- **Extension Methods**: Allow you to add new methods to existing types without modifying them. An extension method is a static method in a static class, with the `this` modifier on its first parameter, which specifies the type it extends.
- **Anonymous Types**: Simple, compiler-generated classes for storing a set of read-only properties. They are created with `new { Name = "Bob", Age = 23 }`.
- **Tuples**: Provide a simple syntax for grouping multiple data elements, commonly used for returning multiple values from a method. Elements can be named, e.g., `(string name, int age)`.
- **Records**: A special kind of class or struct designed for immutable data. They provide compiler-synthesized methods for value-based equality, a concise `ToString()` override, and support for _nondestructive mutation_ via the `with` keyword (`var p2 = p1 with { Y = 4 };`).
- **Pattern Matching**: An enhanced `is` operator and `switch` statements/expressions allow for sophisticated matching based on type, properties, and values. Patterns include type, property, relational (`> 10`), and list (`[1, .., 10]`) patterns.
- **Dynamic Binding**: Using the `dynamic` keyword bypasses compile-time type checking and defers type resolution to runtime. This is useful for interoperability with dynamic languages.
- **Operator Overloading**: Allows custom types to define behavior for operators like `+`, `-`, `==`, and `!=`.
- **Unsafe Code**: Within an `unsafe` block, you can use pointers (`*`), the address-of operator (`&`), and perform direct memory manipulation. This is mainly for performance-critical code or interop with native libraries.