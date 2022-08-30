---
type: post
title: "A simple application of refactorization"
description: "You should always refactor your code, even if it's not part of a large or complex project!"
date: 2022-08-30
tags: ["math", "algorithms", "programming", "code readability", "competitive programming", "refactorization"]
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
This concept is a straight analogy of financial debt: it refers to the *cost* of choosing the easiest option for an immediate problem, instead of taking the time to develop a better solution.
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
> The first line of the input contains one integer $t$ ($1 \le t \le 10^4$) — the number of test cases.
> 
> The next $t$ lines describe test cases. Each test case is given on a new line and consists of four space-separated integers $a$, $b$, $c$ and $n$ ($1 \le a, b, c, n \le 10^8$) — the number of coins Alice has, the number of coins Barbara has, the number of coins Cerene has and the number of coins Polycarp has.
> 
> **Output**
> 
> For each test case, print `"YES"` if Polycarp can distribute all $n$ coins between his sisters and `"NO"` otherwise.

As mentioned earlier, the first and most important step is to write code that can just solve this problem.

We know that each sister can initially have a different amount of coins, and that Polycarp can only *give* coins to them, not take from them.
So, to eliminate the gap between $a$, $b$ and $c$, we should use the maximum of them as a value to match, giving coins to the others in such a way that the 3 quantities are equal.
Even if $a = b = c$, this would work because to match the "maximum", we would just "give" 0 coins to the rest.

Let's define $r$ as the remaining coins after matching the amounts, where $r \le n$.
Polycarp must get rid of all the coins, so if $r > 0$, it must to be divisible by 3 in order to be a possible distribution.
If $r \mod 3 \ne 0$, then there is no way that the sisters can have the same amount of coins.

Both the time and space complexities of this solution is constant.
Now, this type of problems usually comes around during contests where the goal is to solve them ASAP, so a lot of submissions would look like a quick script:

```py
t = int(input())

while t > 0:
    a, b, c, n = map(int, input().split())

    m = max(a, b, c)
    x = m - a
    y = m - b
    z = m - c

    r = n - x - y - z

    if r >= 0 and r % 3 == 0:
        print("YES")
    else:
        print("NO")

    t -= 1
```

This code is accepted by Codeforces, it's the optimal solution.

If you were participating in a contest, you would immediately go to the next problem, of course.
What I suggest here is, once the contest is over, take these pieces of code (your code) and see how you can make it prettier, more readable.
I believe this is a good practice because it trains us as developers to always try to improve our code, to make sure it is maintainable and scalable, even when we already know that it works correctly.

Code can be refactored in many ways.
Following with the example, a good first thing to do is adding comments that explain something that the code itself don't.
The names of variables, functions, and objects in general, should be descriptive enough to minimize comments:

```py
cases = int(input())

while cases > 0:
    a, b, c, n = map(int, input().split())

    max_coins = max(a, b, c)
    # differences to match amounts to max amount
    a_diff = max_coins - a
    b_diff = max_coins - b
    c_diff = max_coins - c

    remainder = n - a_diff - b_diff - c_diff

    if remainder >= 0 and remainder % 3 == 0:
        print("YES")
    else:
        print("NO")

    cases -= 1
```

Another common type of refactorization is **modularizing** your code.
Modularization refers to separating the functionality of a program into independent and reusable modules that can then execute individual and specific tasks.
If you take a look at the [solutions to the problem](https://codeforces.com/contest/1294/status), you may find something like the following:

```py
def solve():
    a, b, c, n = map(int, input().split())

    max_coins = max(a, b, c)
    # differences to match amounts to max amount
    a_diff = max_coins - a
    b_diff = max_coins - b
    c_diff = max_coins - c

    remainder = n - a_diff - b_diff - c_diff

    if remainder >= 0 and remainder % 3 == 0:
        print("YES")
    else:
        print("NO")

cases = int(input())
while cases > 0:
    solve()
    cases -= 1
```

Yes, the code is now organized in a function that can be called multiple times, but that function doesn't have any advantages as to how it was before.
Besides, different aspects of the program functionality are not being separated, which makes the code harder to follow and maintain.
Just wrapping a bunch of lines into a function is not always enough to apply a good modularization.

Let's think about the steps of our solution.
We read things from the input, we do some calculations, and then we print a result.
A good modular code will do these 3 things separately:

```py
def can_distribute(a: int, b: int, c: int, n: int) -> bool:
    max_coins = max(a, b, c)

    # differences to match amounts to max amount
    a_diff = max_coins - a
    b_diff = max_coins - b
    c_diff = max_coins - c

    remainder = n - a_diff - b_diff - c_diff
    return remainder >= 0 and remainder % 3 == 0

# read input
cases = int(input())

while cases > 0:
    # read input again
    a, b, c, n = map(int, input().split())

    # do calculations and save result
    answer = "YES" if can_distribute(a, b, c, n) else "NO"
    # print result
    print(answer)

    cases -= 1
```

We have isolated the core part of our code, the one that actually solves the problem, which is the `can_distribute` function.
This function has been abstracted to concern about a single task: calculating whether an equal distribution for the 3 sisters is possible or not, by returning a boolean value.
And that's it.

Now it doesn't matter if we get our input through the terminal or an API or by reading a file, because the function can do its job with just the parameters.
We can say the same about the output, we can either print the solution to the console or use it to construct the reply of a bot, without ever modifying the function.
And, especially, the code looks much cleaner.

This is personally useful because I like testing my approaches with Pytest, but if I submit a file that has a `import pytest` in it, Codeforces throws a runtime error.
To fix this, I created another file in which I import both the Pytest library and [the function](https://github.com/netotz/codecamp/blob/master/codeforces/p1294A_coins.py) that actually solves the problem, and then write the tests [in there](https://github.com/netotz/codecamp/blob/master/codeforces/tests/1294A.py).
