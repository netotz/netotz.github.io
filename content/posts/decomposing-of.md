---
type: post
title: "Decomposing an objective function"
description: "How the objective function of an optimization problem can be evaluated step by step."
date: 2022-02-22
lastmod: 2022-07-19
tags: ["operations research", "optimization", "math", "computer science"]
mathjax: true
---

As the thesis for the BSc Software Engineering, I'm researching heuristic algorithms in the Operations Research field.
Specifically, [my mentor](http://yalma.fime.uanl.mx/~roger/work/index.html) and I are implementing heuristics for the $\alpha$-neighor $p$-center problem (ANPCP for short).
Because the literature about it is scarce, we've been reading papers that use heuristic algorithms for similar problems, and trying to adapt them to the ANPCP.
In this process, I found it interesting how some authors decompose the objective function of a problem and evaluate it step by step while, at the same time, applying a heuristic to the current solution.

In this post, you're going to learn about the field of operations research, how some optimization problems are evaluated, and a concept that allows a type of algorithms to be blazing fast.

I was taking a look at the fast swap-based local search procedure[^1], which is an acceleration of the fast algorithm for greedy interchange[^2], in which the goal is to find as fast as possible a facility to remove from the solution, given a candidate facility to add to it, that would best improve the objective function value.

Before going on, let me give you some context.
The two heuristics mentioned above are applied to the $p$-median problem (PMP), which is a location problem.

> In Operations Research, location problems are discrete optimization problems that consist of determining the best location for one or several centers or facilities in order to serve and supply a set of demand points, often referred as customers or users.
> 
> The solution of a location problem is the set of those centers.
> 
> A location problem is characterized by a specific objective function, which defines how a solution is *better* than another.

The objective function of the PMP is formulated as:

$$
\textrm{minimize}
\sum_{u \in U}{
    \min_{s \in S}{
        d(u, s)
    }
}
$$

> Minimize the sum of distances between each user and its center (its closest open facility). In this case, the distance is the cost of supplying a user.

In which $U$ is the set of users, $S$ the solution set of open facilities, and $d(u, s)$ a function that returns the distance between a user $u$ and a facility $s$.

When I first learned about local search heuristics, I thought that the objective function had to be evaluated every time a move was made and compared.

> Local search heuristics try to improve an existing solution by iteratively modifying it. A modification applied to the solution is know as a *move*.

For example, if the move is an interchange or a swap between a facility inside the solution (a center) and another outside of it, then the solution would change after applying it (obviously), so because the objective function depends on the solution, the function would change as well (obviously), meaning that, in order to determine if the swap yields a better solution, the objective function needs to be evaluated again (obviously... or not?).

You can see that the time complexity of the equation above is $O(pn)$, where $p$ is the size of $S$, and $n$ the size of $U$.
A straight implementation of a local search with the interchange move would have to check every possible pair between the centers (facilities in the solution) and closed facilities, resulting in a total of $p(m - p)$ pairs, $m$ being the size of $F$, the set of candidate facilities ($S \subset F$, $p < m$).
The time complexity of just looping through these pairs is $O(pm)$.
And because the objective function supposedly has to be evaluated for every pair, the complexity increases to $O(p^2mn)$.

I naively ran this implementation the first time (for the ANPCP) and, believe me, it is slow.

A smarter and faster approach would be to **just calculate the difference in the objective function value while, at the same time, a swap is being applied**, since reevaluating the objective function over and over is computationally expensive (very).
In the case of the PMP the objective function is a summation, so the differences are easy to calculate as they are just subtractions of distances.
This is what Whitaker did in [^2] and was later improved by Resende & Werneck in [^1] (using "partial" moves) by **reasoning about each user contributing independently to the objective function value**.
They built a local search procedure that does $O(mn)$ operations.

I read those papers to try to adapt the fast swap procedure for the ANPCP... but what the heck is the ANPCP?

> The $\alpha$-neighbor $p$-center problem (ANPCP) is a generalization of the $p$-center problem (PCP), in which the goal is to select $p$ centers such that the maximum distance of a user to its $\alpha$th closest center is minimized.
>
> Setting $\alpha = 1$ corresponds to the classical PCP: it represents the *first* closest center. So $\alpha = 2$ represents the *second* closest center, and so on.

The objective function of the ANPCP is formulated as:

$$
\textrm{minimize}
\max_{u \in U}{
    \min_{s \in S}^{\alpha}{
        d(u, s)
    }
}
$$

> Minimize the largest distance between each user and its $\alpha$th closest center.

Perhaps you have already noticed my mistake: the fast swap procedure calculates additions and subtractions because that's how the objective function of the PMP is evaluated, and this is totally different from the ANPCP objective function.

But it was kind of late. The heuristic was already implemented and I was running some experiments with it.
It was getting stuck when $\alpha > 2$, but for the PCP ($\alpha = 1$) it was actually improving solutions, probably due to the similarities that both problems share:
while the PCP doesn't add anything, each user is assigned to its closest facility, which is also true for the PMP... but not for the ANPCP.

Consequently, I decided to keep trying the same algorithm with additions and subtractions, but this time adapting it to calculate differences from the $\alpha$th closest facilities, as if it were the "$\alpha$-neighbor $p$-median problem", which I don't know if it even exists, but it was using the PMP objective function.

The most relevant change in the algorithm is the fact that in the ANPCP, for any user $u$, when the facility that is being added to the solution, $f_i$, is closer than its current $\alpha$th closest, $f_\alpha$, two things can happen:

- $\textrm{If } d_{\alpha - 1} < d_i < d_\alpha \textrm{, then } f_\alpha \leftarrow f_i$
- $\textrm{Else if } d_i < d_{\alpha - 1} < d_\alpha \textrm{, then } f_\alpha \leftarrow f_{\alpha - 1}$

This is because $f_\alpha$ **is determined by all the "previous" closer open facilities** $f_1, f_2, \dots, f_{\alpha - 1}$.

But this change got no results either. I then reasoned that the issue must be the objective function itself, which is obvious, but I wanted to keep experimenting with the objective function of the PMP.
For this, I'm trying to adapt the fast vertex substitution local search used to solve the PCP [^3], which is based on Whitaker's algorithm, with the distinction that it decomposes the PCP objective function by **updating the maximum distance at each iteration of the local search**, instead of calculating differences.
They also use some data structures for accelerations purposes, but different from those by [^1].

So far, I haven't gotten good results using this adaptation.
The main loop of the local search runs forever, except for when $\alpha = 1$ (PCP).
I'm currently stuck in a debugging hell, where I've been running again and again the same procedure without detecting what's wrong.
This has led me to do a manual experiment on paper and see if in that way I can find the bug.

Despite this, I'm having the chance to read good papers about combinatorial optimization and to learn techniques from recognized authors, which for me it is what matters, because as programmers we will always struggle with code.
I've read from senior developers that they still enter debugging hells quite often.
But the difference is that they have much more knowledge and experience, because that's what remains after solving so many problems.

There's a [GitHub repository](https://github.com/netotz/alpha-neighbor-p-center-problem) about this research and the experiments of the algorithms, in case you want to take a look at the code.
It's written in Python, and it will probably be migrated to C++ for performance.
It's organized in branches so the latest updates may not be in `main` one yet.

[^1]: Resende, M. G., & Werneck, R. F. (2007). A fast swap-based local search procedure for location problems. *Annals of Operations Research*, 150(1), 205-230.

[^2]: Whitaker, R. A. (1983). A fast algorithm for the greedy interchange for large-scale clustering and median location problems. *INFOR*, 21(2), 95-108.

[^3]: Mladenović, N., Labbé, M., & Hansen, P. (2003). Solving the $p$‐center problem with tabu search and variable neighborhood search. *Networks*, 42(1), 48-64.
