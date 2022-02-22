---
type: post
title: "Decomposing an objective function"
description: "How the objective function of an optimization problem can be evaluated step by step."
date: 2022-02-22
tags: ["operations research", "optimization", "math", "computer science"]
mathjax: true
---

As the final project of the bachelor, I'm researching heuristic algorithms in the Operations Research field.
Specifically, [my mentor](http://yalma.fime.uanl.mx/~roger/work/index.html) and I are implementing heuristics for the $\alpha$-neighor $p$-center problem (ANPCP for short).
Because the literature about it is scarce, we've been reading papers that implement algorithms for similar problems, and trying to adapt them to the ANPCP.
In this process, I found it interesting how some authors decompose the objective function of a problem and evaluate it step by step, at the same when some heuristic is being calculated.

I was taking a look at the fast swap-based local search procedure[^1], which is an acceleration of the fast algorithm for greedy interchange[^2], in which the goal is to find as fast as possible a facility to remove from the solution, given a candidate facility to add to it, that would best improve the objective function value.

Let me give you the context. The previous two referenced papers are applied to the $p$-median problem (PMP), whose objective function is defined as:

$$\textrm{minimize} \sum_{i \in U}{\min_{j \in S}{d_{ij}}}$$

In which $U$ is the set of users or customers, $S$ the solution set of open facilities, and $d_{ij}$ the distance between nodes $i$ and $j$.

When I first learned about local search algorithms, I thought that the objective function had to be evaluated every time a move was made and compared.
For example, if the move is an interchange or a swap between a node inside the solution and another outside of it, after applying it the solution would be a different (obviously), so because the objective function depends on the solution, it would change too (obviously), meaning that, in order to determine if that move yields a better solution, the objective function needs to be evaluated (obviously... or not?).

You can see that the time complexity of the equation above is $O(pn)$, where $p$ is the size of $S$, and $n$ the size of $U$.
A straight implementation of a local search with the interchange move would have to check every possible pair of nodes inside and outside of the solution, resulting in a total of $p(m - p)$ pairs, $m$ being the size of $F$, the set of candidate facilities ($S \subset F$, $p < m$).
The time complexity of just looping through these pairs is $O(pm)$.
And because the objective function has to be evaluated for every pair, the complexity increases to $O(p^2mn)$.

I naively ran this implementation the first time but for the ANPCP and, believe me, **it is slow**.

A better and faster approach is to only calculate the difference in the objective function value after applying every move, because reevaluating it over and over is an expensive operation.

In the case of the PMP the objection function is a summation, so the difference between its values is easy to calculate as they are just subtractions of distances.
This is what Whitaker did in [^2] and was later improved by Resende & Werneck in [^1].
They build a local search procedure with $O(mn)$ operations.

So, continuing with the story, 

# The $\alpha$-neighbor $p$-center problem

## Motivation

This problem arises in the modeling of the placement of emergency facilities, such as fire stations or hospitals, where the aim is to have a minimum guaranteed response time between a customer and its $\alpha$-th closest facility by considering a notion of fault tolerance, i.e., providing backup centers in case one of them fails to respond to an emergency situation.
This ensures that even if up to $\alpha - 1$ facilities fail, every customer has a functioning facility close to it.

## Definition

The ANPCP is a generalization of the $p$-center problem (PCP), in which the goal is to select $p$ centers or facilities such that the maximum distance of a demand point or customer to its $\alpha$-th closest center is minimized.
Setting $\alpha = 1$ corresponds to the classical PCP, because it would be *first* closest facility.
But if, for example, $\alpha = 2$, a customer would be assigned to its *second* closest facility.

[^1]: Resende, M. G., & Werneck, R. F. (2007). A fast swap-based local search procedure for location problems. *Annals of Operations Research*, 150(1), 205-230.
[^2]: Whitaker, R. A. (1983). A fast algorithm for the greedy interchange for large-scale clustering and median location problems. *INFOR: Information Systems and Operational Research*, 21(2), 95-108.
