---
type: post
title: "C# Wizardry - Understanding events"
description: "Personal notes on what events are in C#."
date: 2022-03-04
tags: ["programming", "csharp", "dotnet", "notes"]
mathjax: true
---

The content below is retrieved from Jupyter notebooks that you can find [in this repository](https://github.com/netotz/learning-dotnet/blob/main/csharp/events/).

Source: [Microsoft documentation](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/).

---

Events enable a class or object (the publisher) to notify other classes or objects (the subscribers) when something occurs.

- The publisher determines when to raise an event. It can have multiple subscribers.
- The subscriber determines how to handle an event when it's raised. It can be subscribed to multiple events.

`System.EventHandler` is a delegate to handle an event that has no event data. Its signature is:

```cs
public delegate void EventHandler(object? sender, EventArgs e);
```

It's equivalent to:


```C#
public Action<object?, EventArgs> ActionEventHandler;
```

`System.EventArgs` provides a value to use for events with no data, but it also represents the base class for events with data, e.g.


```C#
public class MyEventArgs : EventArgs {
    public string Message { get; }
    public DateTime DateTime { get; }

    public MyEventArgs(string message) {
        Message = message;
        DateTime = DateTime.Now;
    }
}
```

It's possible to define a custom event handler if we have custom event data, like the class defined above:


```C#
public delegate void DefinedEventHandler(object sender, MyEventArgs args);
```

which can also be defined using generics with `System.Action` class.

Events are declared with the `event` keyword in the publisher class:


```C#
public event DefinedEventHandler DefinedEvent;
```

There's an option to use generic types for custom event data so it's not necessary to define a new delegate, as shown below:


```C#
public class Publisher {
    public event EventHandler<MyEventArgs> MyEvent;

    public void SendMessage(string message) {
        var eventArgs = new MyEventArgs(message);
        OnRaiseMyEvent(eventArgs);
    }

    public void OnRaiseMyEvent(MyEventArgs args) {
        // use a copy of the event to avoid a race condition
        var raiseEvent = MyEvent;

        if (raiseEvent is null) {
            // event is null if it doesn't have subscribers
            return;
        }

        // raise the event by invoking it as calling a method
        raiseEvent(this, args);
    }
}
```


```C#
public class Subscriber {
    public int Id { get; }

    public Subscriber(int id, Publisher publisher) {
        Id = id;
        // the event gets a new subscriber, which is a method to handle it
        publisher.MyEvent += HandleMyEvent;
    }

    /// The parameters of this method match the signature of Publisher.MyEvent
    public void HandleMyEvent(object sender, MyEventArgs args) {
        Console.WriteLine($"Subscriber {Id} received the following message:\n{args.Message}\nAt {args.DateTime}");
    }
}
```


```C#
var publisher = new Publisher();
var subscribers = Enumerable.Range(1, 3)
    .Select(i => new Subscriber(i, publisher))
    .ToList();
```


```C#
publisher.SendMessage("Good afternoon to all!");
```

    Subscriber 1 received the following message:
    Good afternoon to all!
    At 1/21/2022 6:47:40 PM

    Subscriber 2 received the following message:
    Good afternoon to all!
    At 1/21/2022 6:47:40 PM

    Subscriber 3 received the following message:
    Good afternoon to all!
    At 1/21/2022 6:47:40 PM



Events are a special type of multicast delegates that can only be invoked from within the class that declares them. In other words, only the publishers can raise their own events.
