---
type: post
title: "The SOLID Principles"
description: "Notes and definitions about the SOLID principles."
date: 2022-04-25
tags: ["programming", "software engineering", "solid", "notes"]
image: "/images/unclebob.jfif"
showTableOfContents: true
---

![](/images/unclebob.jfif)

Awesome articles about the topic:

- [SOLID Principles Around You](https://trekhleb.medium.com/solid-principles-around-you-6db2f0e12139)

---

> In software engineering, **SOLID** is a mnemonic acronym for five design principles intended to make software designs more understandable, flexible, and maintainable.

But when to apply SOLID? [Ardalis](https://twitter.com/ardalis) recommends following the *Pain Driven Development* or PDD:
1. Write code using the simplest techniques you know to get the problem solved. Trying to always apply all SOLID principles would result in a premature optimization of the application's design.
2. As the application grows, look for places where it is *painful* to work with. See if this pain could be alleviated by applying one (or more) of the SOLID principles.

The five SOLID principles are:

1. Single Responsibility (SRP).
2. Open-Closed (OCP).
3. Liskov Substitution (LSP).
4. Interface Segregation (ISP).
5. Dependency Inversion (DIP).

# Single Responsibility Principle (SRP)

> Each module should have one and only one reason to change.

In the context of software design, it's important to separate the *what* from the *how* through **delegation** and **encapsulation**.

Classes (or modules) should encapsulate doing a particular task in a particular way. 
When they're single-purpose, they're usually perfectly suited to this purpose and easy to use.
Other classes can delegate the specific task to an instance of a class that encapsulates performing that task.

## Responsibilities

A **responsibility** answers the question of how something is done.
It's a decision that the code makes about the specific implementation details of some part of what the application does.

Responsibilities in the code represent things that may change at different times and for different reasons.
Each responsibility can be thought as a different axis of change.

Logging is an example of a responsibility.
Let's say that at first we log through the console output, as usual.
But later, in production, we want to log to text files.
If we hard coded lines like `Console.WriteLine` whenever we needed to log something, it's going to be a pain having to rewrite every line for something like `File.WriteLine`.
And then the client requires logging in the database too, so waste time going back and modify all of the logging lines again.

So, a class that implements how the logging is done would allow us to just change that implementation, and nothing more, no changes in classes that have no relation with logging.
This would take care of the logging responsibility.
We can even add multiple implementations to that class and use them as we need in the rest of the code.

We need to output information through the lifetime of the application.
What is this? Logging.
The output destination may change.
The logging framework or library may change.
How to implement logging then? With this question we have identified a responsibility, meaning that the solution would be to implement a class that handles only this single responsibility of logging.

## Modularization

Modular programming emphasizes separating the functionality of a program into independent, interchangeable modules, such that each one contains everything necessary to execute only one aspect of the desired functionality.

**Cohesion** refers to the degree to which elements contained in the same module are related between them.
It measures the strength of the relationships between these elements.

**Coupling** refers to the degree of interdependence between modules.
It measures how closely connected distinct modules are, and the strength of the relationships between them.

The SRP aims for high cohesion and loose coupling.

![](/images/modularization.png)

We have classes `A`, `B`, and `C`.
Each class has its own elements or members labeled with numbers.
Imagine having to modify any member in the bad modularization example.
Those are the moments when you ask yourself why you wanted to be a developer.

# Open-Closed Principle (OCP)

> Software entities should be open for extension, but closed for modification.

In this context, a software entity can be a class, a method, a module, a function, etc.

Or as [Uncle Bob](https://en.wikipedia.org/wiki/Robert_C._Martin) said,

> You should be able to extend the behavior of a system without having to modify that system.

This principle is important because the less the source code is edited, the less likely it is that new bugs are introduced and that dependencies are broken.

One area in which the Open-Closed Principle is especially important is in libraries and packages.
Consumers of a package cannot modify its source code directly, and the authors shouldn't either because it could break past versions.
But consumers should be able to extend the contents of the package for their own needs.

## Abstraction vs Concreteness

Code that does one and only one very specific thing, is extremely concrete:

```cs
/// A class that only prints "Concrete!" in console.
public class DoesOneThing {
    public void Execute() {
        Console.WriteLine("Concrete!");
    }
}
```

On the other hand, code than can be extended in every possible direction, capable to do literally anything, is infinitely abstract:

```cs
/// A class that can do anything.
public class DoesAnything<TArg, TReturn> {
    public Func<TArg, TReturn> Function { get; set; }
}
```

```cs
var cosineCalculator = new DoesAnything<double, int> {
    Function = Math.Cos
};
var cos10 = cosineCalculator.Function(10);

var firstCharGetter = new DoesAnything<string, char> {
    Function = word => word[0]
};
// ...
```

This class can do anything because it can't do anything by itself. 100% of the functionality is passed to it.

Code should be balanced between abstraction and concreteness, keeping in mind that abstraction adds complexity.
In order to have a good balance, we'd have to try to predict where variation is going to be needed as the application evolves, and apply enough abstraction there.

A simple and good approach is to start being concrete in everything.
Then, if we notice some modifications in some parts of the code, we should start implementing just enough abstraction to those parts to make them extensible, like adding parameters, designing inheritance, etc.

In legacy projects, use new classes for new features, to avoid breaking existing dependencies and implementations.

# Liskov Substitution Principle (LSP)

[Barbara Liskov](https://en.wikipedia.org/wiki/Barbara_Liskov) and [Jeannette Wing](https://en.wikipedia.org/wiki/Jeannette_Wing) described this principle in 1994 as follows:

> Let $\phi(x)$ be a property provable about objects $x$ of type $T$.
> Then $\phi(y)$ should be true for objects $y$ of type $S$ where $S$ is a subtype of $T$.

Huh? What they meant is:

> Classes and subclasses should be interchangeable without breaking the system.

Inheritance in OOP is often defined as an "is" relationship: a dog **is** a mammal, and a mammal **is** an animal.

```cs
public abstract class Animal {
    // ...
}

public abstract class Mammal : Animal {
    // ...
}

public class Dog : Mammal {
    // ...
}
```

The Liskov Substitution Principle states that this "is" relationship is insufficient, so it should be replaced by "is substitutable for": a dog **is substitutable for** a mammal... isn't it?
That depends on the logic of your application.
Sometimes, types have invariants that break the supposed "is" relationship with another type.
If a `Dog` object is not substitutable for a `Mammal` object and vice versa in the code because there's some invariant about `Dog` that must be kept, then consider making `Dog` a class and not a subclass of `Mammal`.

## Detecting LSP violations

If some code is supposed to be polymorphic, then it should work with a type or any subtype of it.
Therefore, there shouldn't be type checking conditions or castings, i.e. using keywords like `is` and `as`.
These conditions break LSP because if you must change the code according to the subtypes, then they are not substitutable for their base type.
One way to solve this is to override specific behavior in the subclasses themselves, instead of externally checking their subtypes.
This is known as *Tell, Don't Ask*.

Another violation is the use of `NotImplementedException` because then the calls to a subtype methods wouldn't be safe.
This is also a violation of the next principle, ISP.

# Interface Segregation Principle (ISP)

> No code should be forced to depend on methods it doesn't use.

Many small, cohesive, and client-specific interfaces are preferred over a one large and general purpose interface.
This is very similar to what the Single Responsibility Principle states.

An interface in OOP is a contract between classes that standardize behavior.
In the ISP context, an interface refers to anything that can be accessed by client code, such as public methods and properties.
The client code is the calling code, so the OOP definition holds because a subtype is the client code of an interface, but it's not limited to that.

[This article](https://medium.com/@jim_ej/the-interface-segregation-principle-its-confused-aa856de97d36) explains ISP with examples.

# Dependency Inversion Principle (DIP)

> High-level modules should not depend on low-level modules. Both should depend on abstractions.
> 
> Details (concrete implementations) should depend on abstractions.

The high-level code is more abstract and is process-oriented, whereas low-level code is detailed-oriented and interacts with specific external systems.

Abstraction describes the **what** and details describe specify the **how**.

![](/images/dependencies.png)

## Dependency Injection

The dependency injection is a programming technique that avoids creating owned dependencies.
Instead, client code "injects" the required dependencies.
In this way the client code knows the specific dependencies that a class needs.
