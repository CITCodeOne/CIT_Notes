## Ternary Operations 
Ternary operators execute one expression if the condition is `true` and the second expression otherwise, similar to an `if-else` statement.

e.g.
```
string color = "green";
string movement = (color == "green") ? "go" : "stop";
Console.WriteLine(movement);
```

In this example `movement` will be set to the result of the ternary expression. The Boolean expression `(color == "green")`, followed by a question mark `?`, determines the outcome. 

1. If the condition evaluates to `true`the ternary statement will evaluate to "go"
2. Otherwise, the statement will evaluate to "stop".

We separate the two possible expressions, "go" and "stop", with a colon :.

This e.g. is similar to the Ternary operator above:
```
string color = "green";
string movement;
if (color == "green") {
  movement = "go";
} else {
  movement = "stop";
}
```

Important to remember, that ternary statements can be difficult to read, so it is bet to use them with simple expressions and conditions.

