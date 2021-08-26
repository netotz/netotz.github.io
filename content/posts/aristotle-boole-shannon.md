---
type: post
title: Aristotle, Boole and Shannon
description: "How Aristotle's works on logic, through the extensions by George Boole, helped Claude Shannon to create the basis of digital computers."
date: 2021-08-26
tags: [learning, logic, "computer science", history, boole, aristotle, shannon]
mathjax: true
---

The other day I started to watch [Computer Science Crash Course on YouTube](https://www.youtube.com/playlist?list=PL8dPuuaLjXtNlUrzyH5r6jN9ulIgZBpdo).
The first two videos explain the early History of computers, starting from the [abacus](https://en.wikipedia.org/wiki/Abacus): humans needed to **store** quantities;
up to the 1950s.
They explain how technology stopped being mechanical thanks to relays and how it was improved with vaccum tubes and then with transistors.
Each one of these innovations allowed for faster propagation of electrical signals.

The [third video](https://www.youtube.com/watch?v=gI-qXk7XojA) is about Boolean logic, which is one of the bases of programming and one of the first topics we learn in related majors.
In university, we are taught that somebody named George Boole invented [Boolean algebra](https://en.wikipedia.org/wiki/Boolean_algebra), and we learn the tables of truth of the logic operators so we can use logic gates.
And that was it, at least in my classes.
But this video gave me an interesting context that my teachers didn't.

[George Boole](https://en.wikipedia.org/wiki/George_Boole), besides being a self-taugh mathematician, was a philosopher, and he created Boolean algebra because he wanted to provide a mathematical representation of the logical statements made by [Aristotle](https://en.wikipedia.org/wiki/Aristotle) 2000 years before him.
Boole was interested in systematizing Aristotle's approach to logic, in order to provide it with a mathematical foundation through equations so it can be formally proven.
This is well summarized in Boole's first book title: *The Mathematical Analysis of Logic*, published in 1847.

Aristotle, being a [polymath](https://en.wikipedia.org/wiki/Polymath), wrote about logic besides all of the other topics he's know for.
He has even been called *the father of logic*.
His works on logic, often referred to as [**term logic**](https://en.wikipedia.org/wiki/Term_logic), were compiled by the [peripatetics](https://en.wikipedia.org/wiki/Peripatetic_school), his followers, into a set of six books called the [*Organon*](https://en.wikipedia.org/wiki/Organon).
He introduced the concept of [*syllogism*](https://en.wikipedia.org/wiki/Syllogism), which is a logical argument that applies deductive reasoning to arrive at a conclusion based on two propositions that are assumed to be true.
Here's an example of a syllogism:

> All humans need to sleep.
>
> All programmers are humans.
>
> Therefore, all programmers need to sleep.

Sounds logical right? Well, with the *Organon*, Aristotle gave birth to a whole new field of study.

Approximately 2000 years later, George Boole introduced Boolean algebra.
In normal algebra, the variables can be any number, but in Boolean algebra, their values can only be `true` or `false`, and instead of adding or multiplying them, they work with different [operations](https://en.wikipedia.org/wiki/Boolean_algebra#Operations): [`AND`](https://en.wikipedia.org/wiki/Logical_conjunction), [`OR`](https://en.wikipedia.org/wiki/Logical_disjunction), [`NOT`](https://en.wikipedia.org/wiki/Negation).
These operators work very similar to what they mean in language.
For example, the following statement is `false`:

> I'm writing and I don't have more important things to do.

because although I am writing, I *do* have more important things to do.
Both of the previous sentences are `true`, but in the example, the second sentence is being negated with *don't*.
What's the opposite of `true`? `false`.
So the second sentence ends up being `false`.
In language, when we say *and* we are trying to say that every condition has to be fulfilled or, in other words, `true`.
Because the first sentence is `true` but the second one isn't, the whole sentence results in being `false`.

Almost 100 years later, [Claude Shannon](https://en.wikipedia.org/wiki/Claude_Shannon), the father of [information theory](https://en.wikipedia.org/wiki/Information_theory), attended a philosophy class that introduced him to Boole's work.
He realized that the rules of Boolean algebra could be applied to optimize the systems of relays, that in that time were used in telephone routing switches.
In 1937, Shannon wrote his master's degree thesis, *A Symbolic Analysis of Relay and Switching Circuits*, at the MIT, in which he proved that Boolean algebra could construct any logical numerical relationship and that circuits with relays could solve any Boolean problem, thus founding the basis of **digital circuits**.
This concept of electrical switches implementing logic is the fundamental property of all computers, including modern laptops and smartphones.
He was 21 years old at that time.

I will continue the Crash Course series, as I have learned a lot and I have 37 videos yet to watch.
I found it interesting how Aristotle can also be called in some way *the father of computers*, in addition to all of the other *father of* titles he already has, and how antique studies on logic ended up contributing to the creation of computation.
There's a lot to read too: the works mentioned here seem definitely worth to read.
