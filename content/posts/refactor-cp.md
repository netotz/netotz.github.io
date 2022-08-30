---
type: post
title: "A simple application of refactorization"
description: "You should always refactor your code, even if it's not part of a large or complex project!"
date: 2022-08-29
tags: ["math", "algorithms", "programming", "code readability", "competitive programming", "refactor"]
mathjax: true
showTableOfContents: true
---

Valuable code is such because **it works**.
It truly doesn't matter how such code looks or under what standards or rules it was written.
If it doesn't work, it's trash.
If it works, it adds value.

This is something that almost all programmers know.
We should get this taught early in our journeys because we ought to solve problems by writing code.
The satisfaction that a developer gets when she sees her program working as expected and how it actually solves a real problem is unexplainable ~~(she can finally go to sleep)~~.
It's not a secret that a programmer is capable of writing the most ugliest, anomalous, repulsive piece of code just for the sake of making it work.
I wish I was kidding, but [here are the proofs](https://www.reddit.com/r/programminghorror/).
There's also a substantial amount of evidence in my own projects.

Now, the successful results of a program that works often distracts us from the actual code that we just wrote.
When we are desperate enough for good results that we must resort to bad code, we stake *technical debt*.
This concept is a straight analogy of financial debt: it refers to the **cost** of choosing the easiest option for an immediate problem, instead of taking the time to develop a better solution.
Of course, this cost is no good and you never want to chose an expensive solution (expensive in the long run).
However, in some situations we have no option but going for it, and that's okay.
It actually happens more often than not!
The remedy for technical debt is **refactorization**, which means modifying the code in such a way that its behavior remains unchanged.
The purpose of refactoring code is making it pretty, or handsome, or both, while still getting the desired results.

All of this makes obvious sense when developing large scale and enterprise projects because the code in there should be maintainable, scalable, and readable.
This is actually the goal of the [SOLID principles](../solid).
Yet we tend to ignore this basic programming guideline when we write small pieces of code, like miscellaneous scripts, or when solving problems from competitive programming, where time is key; and these cases are indeed the best examples to practice refactoring code!

For example, let's take a look at a problem from Codeforces, [Collecting Coins (1294A)](https://codeforces.com/problemset/problem/1294/A):

> Polycarp has three sisters: Alice, Barbara, and Cerene. They're collecting coins. Currently, Alice has $a$ coins, Barbara has $b$ coins and Cerene has $c$ coins. Recently Polycarp has returned from the trip around the world and brought $n$ coins.
>
> He wants to distribute all these $n$ coins between his sisters in such a way that the number of coins Alice has is equal to the number of coins Barbara has and is equal to the number of coins Cerene has. In other words, if Polycarp gives $A$ coins to Alice, $B$ coins to Barbara and $C$ coins to Cerene ($A + B + C = n$), then $a + A = b + B = c + C$.
> 
> Note that $A$, $B$ or $C$ (the number of coins Polycarp gives to Alice, Barbara and Cerene correspondingly) can be 0.
> 
> Your task is to find out if it is possible to distribute all $n$ coins between sisters in a way described above.
> 
> You have to answer $t$ independent test cases.
> 
> **Input**
> 
> The first line of the input contains one integer $t$ ($1 \le t \le 104$) — the number of test cases.
> 
> The next $t$ lines describe test cases. Each test case is given on a new line and consists of four space-separated integers $a$, $b$, $c$ and $n$ ($1 \le a, b, c, n \le 108$) — the number of coins Alice has, the number of coins Barbara has, the number of coins Cerene has and the number of coins Polycarp has.
> 
> **Output**
> 
> For each test case, print `"YES"` if Polycarp can distribute all $n$ coins between his sisters and `"NO"` otherwise.

As mentioned earlier, the first and most important step is to write code that can just solve this problem.

We know that each sister can initially have a different amount of coins, and that Polycarp can only **give** coins to them, not take from them.
So, to eliminate the gap between $a$, $b$ and $c$, we should use the maximum of them as a value to match, giving coins to the others in such a way that the 3 quantities are equal.
Even if $a = b = c$, this would work because to match the "maximum", we would just "give" 0 coins to the rest.

Let's define $r$ as the remaining coins after matching the amounts, where $r \le n$.
Polycarp must get rid of all the coins, so if $r > 0$, it must to be divisible by 3 in order to be a possible distribution.
If $r \mod 3 \ne 0$, then there is no way that the sisters can have the same amount of coins.

Both the time and space complexities of this solution is $O(1)$.

