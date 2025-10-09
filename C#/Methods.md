## Quick summary

1. Method is used to define named blocks of custom code that can easily be reused.
2. Methods can be called by adding parentheses to the end of the method name.
3. Every time an application is started, the Main() method is called.
4. Methods can return values as output. Method outputs can be stored in variables for later use.
5. A basic method can be defined using following syntax `static void BasicMethod()`
6. *Arguments* are values passed to method call, between the parentheses.
7. *Parameters* in a method signature define variables that will serve as placeholders for arguments passed when the method is called.
8. Method parameters can only be used within the method body.
9. In *method overloading* multiple methods can have the same name, as long as they have different method signatures.
10. Method parameters can be optional given a default using equals syntax.
11. When calling a method arguments can be passed by position by name. If using names, use the colon syntax.
12. Methods return values with the `return` keyword.
13. We can define a type and value for a method to return.
14. Every method has a return type designated in its method signature. That type must match the type of the value actually returned.
15. If a method returns no type, its return type is `void`
16. `out` parameters can be used to return multiple values from a method.

## Introduction
A method is a reusable set of instructions that performs a specific task. It is used to keep code organized, maintainable, and repeatable. Sometimes methods is also refered to functions. 

Methods can take inputs and produce outputs.

1. Inputs can be required or optional have an affect on how the method performs its task.
2. Outputs may be information about the task being performed or the result of the task. 

Each method has a different behavior, one of the most common one is `Console.WriteLine()` that prints something to the console. 
We activate a method's behavior by calling it. In C# we do this by adding parentheses to the end of a method name.

There are also built-in methods for each built-in data type in C#. Every `string` has access to methods like IndexOf() and Substring(). The Substring() method returns a shorter section of a string based on 1 or 2 integer arguments.

e.g. 
```C#
string name = "Beatrice";
name.Substring(0, 3); // returns "Bea"
```

### Calling Methods small example

How to use index to call different a different placement in the string.

```C#
namespace CallingMethods
{
  class Program
  {
    static void Main(string[] args)
    {
      string designer = "Anders Hejlsberg";
      Console.WriteLine(designer);
      int indexOfSpace = designer.IndexOf(" ");
      int indexOfSecondName = indexOfSpace + 1;
      string secondName = designer.Substring(indexOfSecondName);
      Console.WriteLine(secondName);
    }
  }
} 
```

Here we use the method `indexOf()` and `Substring()` to call only on the last name. **Remember** we use +1 in `indexOfSecondName`, because " " doesn't count as a character.

## Defining Methods
In C# it's a convention to use PascalCase to name our methods. A name starts with and uppercase letter and each word following begins with an uppercase as well. 

The code in the body is executed whenever the method is called. Just like any other method, we call it with parentheses.

### Method Parameters
We can define methods with arguments. While defining our method we do not know the argument values that will be used when calling the method, but we do define the data type and how it will be used. 

We do this by using our information to define a parameter which acts as an input variable within a method. 

e.g.
Try to imagine it as a placeholder for the actual argument value. 
```C#
static void OurMethodName(string identity)
{
  Console.WriteLine(identity);
}
```
The OurMethodName() method has a parameter named `identity` of type `string`. 

We can define multiple parameters within a single method definition, separated by commas.
```C#
static void OurMethodName(string identity, int age)
{
  Console.WriteLine($"{identity} is {age} years old.");
}
```

When you call your method, the values passed in that correspond to each parameter are called *arguments*. 

**Note**: Parameters can only be used inside the method that defines them.

*When talking to other developers about this type of issue, you might hear the term scope. Here it applies as: a parameter's scope is within its method, which means that the name is only valid within its method. If parameter name is used outside the method it has no meaning, and throws an error.*

**Small exercise**
```C#
using System;

namespace DefineParameters
{
  class Program
  {
    static void Main(string[] args)
    {
      VisitPlanets(3);
      VisitPlanets(4);
      VisitPlanets(5);
    }
    
    static void VisitPlanets(int numberOfPlanets)
    {
      Console.WriteLine($"You visited {numberOfPlanets} new planets...");
    }
  }
}
```

## Return Statement 
We can have a method return a value by providing a **return** type in its signature and using the `return` keyword in the method body. 

The `return` keyword tells the computer to exit the method and return a value to wherever the method was called. 

e.g.
```C#
static int Triple(int num) 
{
  return num * 3;
}

public static void Main()
{
  int output = Triple(4);
  Console.WriteLine(output); // 12
}
```

1. `Triple()` is called
2. In `Triple()`, the value is of `num` multiplied by `3` is calculated and returned.
3. Back in `Main()`, that returned.
4. Back in `Main()`, that returned value is stored in the variable `output` and then printed to the console. 

When a method is declared, it must announce the type of value it will return. 
When we use the return type of `void` it indicates that the method does not `return` a value.

**Small exercise**
```C#
using System;

namespace Return
{
  class Program
  {
    static void Main(string[] args)
    {
      Console.WriteLine(DecoratePlanet("Jupiter"));
    }
    
    static string DecoratePlanet(string planet)
    {
      return $"*.*.* Welcome to {planet} *.*.*";
    }
	}
}
```

Same outcome as the former exercise, but this time using the return statement.

## Method Overloading
[Microsoft documentation for Math.Round()](https://learn.microsoft.com/en-us/dotnet/api/system.math.round?view=net-8.0#overloads) 
`Math.Round()` has at least 8 different versions, but they all have the same name, `Math.Round()`, but they all have an unique set of parameters.

This is using method overloading, and each "version" is called an overload. 
So even if they have the same name, overloads have either:

1. Different parameter types
2. Different number of parameters

Overloading is useful when we want the same method to have different behavior based on its inputs.

This can be used to other methods as well, as long as it has different sets of parameters.

## Out Parameters
Sometimes we need to output multiple pieces of information. Calling a method that uses an `out` parameter is one way to return multiple values. 

e.g. `Int32.TryParse()` method tries to parse its input as an int. If it can properly parse the input, the method returns `true` and sets its `out` variable to the new value. If it can't, the method returns `false`and sets the out variable to `0`. 

e.g.
```C#
public static bool TryParse (string s, out int result);
```

The method returns a bool and accepts a `string` and a variable that has been declared of type `int` as input.

We can also use the `out` parameters in our own methods. Any `out` parameter must be assigned a value within the method body. The last value assigned in the method will be available to the methods caller. 

In this e.g. we define `area` and `perimeter` `out` parameters and use their values in `Main()`: 
```C#
static void CalculateRectangle(int length, int width, out int area, out int perimeter)
{
    area = length * width;
    perimeter = 2 * (length + width);
}

static void Main()
{
    int length = 5;
    int width = 3; 
    CalculateRectangle(length, width, out int area, out int perimeter);
    Console.WriteLine($"Area: {area}"); // Prints "Area: 15"
    Console.WriteLine($"Perimeter: {perimeter}"); // Prints "Perimeter: 16"
}
```

## Optional Parameters
To make functions even more flexible we can make certain parameters *optional*. If someone calls our method without all the parameters, the method will assign a default value to those missing parameters.

We use the equals sign `(=)` when defining an optional method parameter. In this example `punctuation` is optional, and its default value is `"."` 

e.g.
```C#
static void OurMethodName(string message, string punctuation = ".")
{
  Console.WriteLine(message + punctuation);
}

static void Main(string[] args)
{
  OurMethodName("I'm hungry", "!"); // prints "I'm hungry!"
  OurMethodName("I'm hungry");  // prints "I'm hungry."
}
```

Reusing the code with planets, we can see how we added a default and made a new parameter in `VisitPlanets` that equals to `0`.

```C#
using System;

namespace OptionalParameters
{
  class Program
  {
    static void Main(string[] args)
    {
      VisitPlanets(3);
      VisitPlanets(4);
      VisitPlanets(5);
      VisitPlanets();
    }
    
    static void VisitPlanets(int numberOfPlanets = 0)
    {
      Console.WriteLine($"You visited {numberOfPlanets} new planets...");
    }
  }
}
```

## Named Arguments 
Say our method has lots of optional parameters, but we only want to specify one when we call it. 

In this e.g., our method has five optional parameters:
```C#
static void OurMethodName(int a = 0, int b = 0, int c = 0, int d = 0, int e = 0) 
{
  Console.WriteLine($"{a}{b}{c}{d}{e}");
}
```

We call this method, but we only want to specify `d`. By default, calling the method with only one argument would set `a` to `4`, not `d` since `a` is the first parameter defined by the method definition. This is called **positional arguments**, as they correspond to the order that the parameters are defined in:

```C#
OurMethodName(4);
// Prints "40000"
```

**Named arguments** allow us to specify which parameter a given argument should be assigned to.  We can refer to the parameter by its name using the following syntax:

```C#
OurMethodName(d: 4);
// Prints "00040"
```

Named arguments can also be listed in any order

```C#
OurMethodName(d: 4, b: 1, a: 2);
// Prints "21040"
```

Or mix it around, **but positional arguments must come before named arguments**:
```C#
OurMethodName(5, 3, d: 2) 
// a is 5, b is 3, d is 2; Prints "53020"

OurMethodName(d: 4, 2, 1) // Error!
```

This is useful when

1. A method has many optional parameters
2. We want to differentiate between similar arguments

A code e.g. using the former planet code
```C#
using System;

namespace NamedArguments
{
  class Program
  {
    static void Main(string[] args)
    {
      VisitPlanets();
      VisitPlanets(numberOfPlanets: 2);
      VisitPlanets(numberOfPlanets: 2, name: "Elisabeth");
    }
    
    static void VisitPlanets(
      string adjective = "brave",
      string name = "Cosmonaut", 
      int numberOfPlanets = 0,
      double gForce = 4.2)
    {
      Console.WriteLine($"Welcome back, {adjective} {name}.");
      Console.WriteLine($"You visited {numberOfPlanets} new planets...");
      Console.WriteLine($"...while experiencing a g-force of {gForce} g!");
    }
  }
}
```
