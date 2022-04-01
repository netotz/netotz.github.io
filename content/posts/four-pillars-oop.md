---
type: post
title: "The Four Pillars of Object Oriented Programming"
description: "Notes on the so called four pillars of Object Oriented Programming."
date: 2022-03-07
lastmod: 2022-03-31
tags: ["programming", "object oriented programming", "software engineering", "notes"]
---

The four pillars of OOP are:

- Abstraction
- Encapsulation
- Inheritance
- Polymorphism

But before describing each, basic concepts of OOP are needed to understand.

# Basic OOP

Object Oriented Programming (OOP) is a programming paradigm that structures a software program into reusable pieces of code blueprints, which are called **classes**.

## Class

A class is a code template or blueprint that describes what an **object** is and what it does, by defining **attributes** to store data and implementing **methods** or behaviors to use that data.

### Attributes

The attributes of a class represent its characteristics and features.
They are also called **properties** or **fields**.

### Methods

The methods of a class define its behavior, most commonly to interact with the attributes.
Methods are really functions but scoped to the class, therefore having access to its data or fields.

## Object

An object is an **instance** of a class.
It's the product of storing particular values in the attributes of that class and being able to act on them according to its behaviors.

# The Four Pillars of OOP

## Abstraction

It allows complex actions to be simplified to simple verbs. It also means that it separates general qualities from specific ones.

> I don’t need to know how a computer works if I can use it with a simple button.
> 
> There’s no animal called “the mammal” because a mammal is an abstraction of several animals that share some qualities.

## Encapsulation

It keeps related logic and data together, separates responsibilities, and decides what to show to external objects. Some data is kept private so only the containing class can access it.

Not grouping like things together makes it hard to abstract them.

> A car doesn’t walk, and a person doesn’t have a motor. This person can’t see the gasoline tank and then measure the volume to know how much is left (the gasoline tank is private), but she can ask the car by just seeing a level in the screen (the level is public).

## Inheritance

The ability of a class (child or subclass) to acquire all the non-private behavior and attributes of another class (parent or superclass). The subclass can then be extended with additional attributes from those of its parent, and even redefine them to suit the specific requirements of the subclass, if needed.

Inheritance represents an “is” relationship: the subclass is the superclass.

Inheritance favors **reusability**.

> A dog inherits all the behavior and attributes of a mammal, because a dog *is* a mammal.

## Polymorphism

From Greek *poly* “many” and *morphos* “forms”, “**of many forms**”. A class has many forms if it has many children, but they are used the same as their parent, although each child can define its own functionality.

> All mammals make a sound, so dogs make a sound, cats make a sound too. Specifically, dogs bark and cats meow, defining their own sound. But they still make a sound.
