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

