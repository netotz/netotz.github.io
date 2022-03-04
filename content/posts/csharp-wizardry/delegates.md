---
type: post
title: "C# Wizardry - Understanding delegates with custom LINQ methods"
description: "Personal notes on what delegates are in C#."
date: 2022-03-04
tags: ["programming", "csharp", "dotnet", "notes"]
mathjax: true
---

The content below is retrieved from Jupyter notebooks that you can find [in this repository](https://github.com/netotz/learning-dotnet/blob/main/csharp/delegates/).

Source: [Microsoft documentation](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/).

---

A delegate encapsulates a method.

## Actions

`System.Action` is the standard library type for void delegates, methods that don't return a value. By default it's parameterless, but it can have parameters with generics.


```C#
Action action = () => Console.WriteLine("Action done.");
// invoked as method call
action()
```

    Action done.




```C#
// user-declared delegate, no parameters
delegate void MyAction();
MyAction myAction = () => Console.WriteLine("My action done.");
myAction()
```

    My action done.



I'm using lambdas here, which are anonymous functions that are defined on the fly, but delegates can accept already defined methods as well:


```C#
/// Method that writes "Done." to console output.
public void WriteDone() {
    Console.WriteLine("Done.");
}

Action actionDone = WriteDone;
actionDone()
```

    Done.




```C#
Action actionWithParam = (int i) => Console.WriteLine(i);
```


    (1,26): error CS1593: Delegate 'Action' does not take 1 arguments


Declaring an `Action` with generic types to invoke it with parameters:


```C#
// type of lambda parameter `i` is determined from declaration of delegate object
Action<int> actionOnInteger1 = i => Console.WriteLine(i);
// same as
// type of delegate object is determined from definition of lambda
var actionOnInteger2 = (int i) => Console.WriteLine(i);
actionOnInteger2(0)
```

    0




```C#
actionOnInteger2.GetType()
```


<span><a href="https://docs.microsoft.com/dotnet/api/system.action-1?view=net-5.0">System.Action&lt;System.Int32&gt;</a></span>



```C#
// user-declared delegate, one parameter
delegate void MyActionOnInteger(int i);
MyActionOnInteger myActionOnInteger = i => Console.WriteLine(i);
myActionOnInteger(0)
```

    0




```C#
Action<int> actionThatReturns = i => { return i * 2; };
```


    (1,40): error CS8030: Anonymous function converted to a void returning delegate cannot return a value


Remember, `Action` is declared as a void delegate, it can't return any value.

## Funcs

`System.Func` is the standard library type for delegates that encapsulate methods that return a value. It can have parameters with generics too.


```C#
// the last generic type is the return type. Parameters are optional
Func<string> func = () => "Function returned.";
var returned = func();
returned
```


    Function returned.



```C#
// user-declared delegate that returns a string, same as previous Func
delegate string MyFunc();
MyFunc myFunc = () => "My function returned.";
returned = myFunc();
returned
```


    My function returned.



```C#
Func<int, int, int> funcMultiply = (x, y) => x * y;
funcMultiply(2, 3)
```


<div class="dni-plaintext">6</div>



```C#
delegate int MyFuncMultiply(int x, int y);
MyFuncMultiply myFuncMultiply = (x, y) => x * y;
myFuncMultiply(2, 3)
```


<div class="dni-plaintext">6</div>


## Multicasting

A delegate can call multiple methods when invoked:


```C#
Action actions = () => Console.WriteLine("first method");
actions += action;
actions += actionDone;
actions += () => Console.WriteLine("last method");
actions()
```

    first method

    Action done.

    Done.

    last method




```C#
actions.GetInvocationList().Count()
```


<div class="dni-plaintext">4</div>


## Use cases

- There's no need to declare new custom delegates, use `Action` and `Func` types.
- You can just use `var` and declare the types in the lambda definition. So why being aware of delegate types?:

### Delegates as abstractions

Code calling a delegate used as an abstraction doesn't need to know (and doesn't care) about how that delegate is implemented.


```C#
public void WriteAsUpper(string message) {
    Console.WriteLine(message.ToUpper());
}

public void WriteLength(string message) {
    Console.WriteLine(message.Length);
}

public string CreateMessage(string title, string text, DateTime date, Action<string> writeCallback) {
    var message = $"{title}\n{text}\n{date.ToLongDateString()}";

    // I already know the delegate type: it receives a string and doesn't return anything. Ok more than enough info
    // But what does it do? I COULD CARE LESS, just call it :)
    writeCallback(message);
    
    return message;
}
```


```C#
var redMessage = CreateMessage("Red", "Roses are red", DateTime.Now, WriteLength);
```

    44




```C#
redMessage
```


    Red
    Roses are red
    Saturday, January 15, 2022



```C#
var blueMessage = CreateMessage("Blue", "Blue roses are fake", DateTime.Now, WriteAsUpper);
```

    BLUE
    BLUE ROSES ARE FAKE
    SATURDAY, JANUARY 15, 2022



In the above example, `CreateMessage` was defined to create a message and return it. It wasn't defined thinking about the console. If this method could speak it would tell you:
> If you want to show the message in console, create a delegate to do it and pass it to me as the `callback` parameter. Or whatever you want to do with it just create a delegate for that and pass it. I don't care, that's the callback's job.

# Implementing custom LINQ operations

If you have used LINQ you know that it uses functions to process collections of data. The purpose of this exercise is to apply delegates in a 
more practical way than just dummy examples.

**Note:** I'm not using tags inside docstrings because Visual Studio Code doesn't autocomplete them in notebooks, but they must be included in real code.


```C#
/// Inheriting from List to skip specific implementations from interfaces, irrelevant for our topic,
/// so we can use foreach statements directly and focus on our custom methods and delegates.
class IntList : List<int> {
    /// Custom delegate equivalent to Func<int, bool>
    public delegate bool ConditionCallback(int integer);

    /// Equivalent for Where
    public IEnumerable<int> Filter(ConditionCallback condition) {
        foreach (var integer in this) {
            if (condition(integer)) {
                yield return integer;
            }
        }
    }

    /// Equivalent for Select<T>
    public IEnumerable<TReturn> Map<TReturn>(Func<int, TReturn> mapper) {
        foreach (var integer in this) {
            yield return mapper(integer);
        }
    }

    /// Equivalent for All
    public bool Every(ConditionCallback condition) {
        foreach (var integer in this) {
            if (!condition(integer)) {
                return false;
            }
        }

        return true;
    }

    /// Equivalent for Any
    public bool Some(ConditionCallback condition) {
        foreach (var integer in this) {
            if (condition(integer)) {
                return true;
            }
        }

        return false;
    }
}
```

In the above code, each method invokes its delegate parameter, thus calling the function it encapsulates.


```C#
var ints = new IntList { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
```

Using lambdas for throwaway functions:


```C#
// get the fifth part of each integer as double
ints.Map(i => i / 5.0)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><div class="dni-plaintext">0.2</div></td></tr><tr><td>1</td><td><div class="dni-plaintext">0.4</div></td></tr><tr><td>2</td><td><div class="dni-plaintext">0.6</div></td></tr><tr><td>3</td><td><div class="dni-plaintext">0.8</div></td></tr><tr><td>4</td><td><div class="dni-plaintext">1</div></td></tr><tr><td>5</td><td><div class="dni-plaintext">1.2</div></td></tr><tr><td>6</td><td><div class="dni-plaintext">1.4</div></td></tr><tr><td>7</td><td><div class="dni-plaintext">1.6</div></td></tr><tr><td>8</td><td><div class="dni-plaintext">1.8</div></td></tr><tr><td>9</td><td><div class="dni-plaintext">2</div></td></tr></tbody></table>



```C#
// get integers as string percentages
ints.Map(i => $"{i * 10}%")
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td>10%</td></tr><tr><td>1</td><td>20%</td></tr><tr><td>2</td><td>30%</td></tr><tr><td>3</td><td>40%</td></tr><tr><td>4</td><td>50%</td></tr><tr><td>5</td><td>60%</td></tr><tr><td>6</td><td>70%</td></tr><tr><td>7</td><td>80%</td></tr><tr><td>8</td><td>90%</td></tr><tr><td>9</td><td>100%</td></tr></tbody></table>


Implementing methods to reuse as callbacks:


```C#
bool IsEven(int integer) {
    return integer % 2 == 0;
}

bool IsOdd(int integer) {
    // reuse code!
    return !IsEven(integer);
}
```


```C#
ints.Filter(IsEven)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><div class="dni-plaintext">2</div></td></tr><tr><td>1</td><td><div class="dni-plaintext">4</div></td></tr><tr><td>2</td><td><div class="dni-plaintext">6</div></td></tr><tr><td>3</td><td><div class="dni-plaintext">8</div></td></tr><tr><td>4</td><td><div class="dni-plaintext">10</div></td></tr></tbody></table>



```C#
ints.Filter(IsOdd)
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td><div class="dni-plaintext">1</div></td></tr><tr><td>1</td><td><div class="dni-plaintext">3</div></td></tr><tr><td>2</td><td><div class="dni-plaintext">5</div></td></tr><tr><td>3</td><td><div class="dni-plaintext">7</div></td></tr><tr><td>4</td><td><div class="dni-plaintext">9</div></td></tr></tbody></table>



```C#
// are there some even integers?
ints.Some(IsEven)
```


<div class="dni-plaintext">True</div>



```C#
var odds = new IntList { 3, 5, 7, 11, 13, 17 };
// is every integer odd?
odds.Every(IsOdd)
```


<div class="dni-plaintext">True</div>

