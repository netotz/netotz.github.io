---
type: post
title: "The Alpha Fast Vertex Substitution algorithm"
description: "A new fast local search heuristic for a location problem."
date: 2022-07-19
tags: ["operations research", "optimization", "math", "computer science", "algorithms", "data structures", "thesis"]
mathjax: true
showTableOfContents: true
---

TL;DR: 

---

# Introduction

Earlier in the year I wrote about [how the objective function of an optimization problem is decomposed](../decomposing-of) and partially evaluated so an algorithm that uses that function runs faster.
I found this technique so interesting that I tried to explain it in that post.
But the goal wasn't to just understand the decomposing of an objective function.
I needed to design a fast algorithm to solve the $\alpha$-neighbor $p$-center problem (ANPCP), as I mentioned in the other post, and while researching to do so, I came across the concept of the *fast interchange* or *fast swap* and how it was applied to other location problems (not the ANPCP).
I also wrote about it in that post, however, at that moment I didn't have a working algorithm for the problem, and I just noticed that I didn't include some definitions there like that of *heuristic*, so:

> A heuristic is any approach to problem solving that employs a practical method that is not guaranteed to be optimal, perfect, or rational, but is nevertheless sufficient for reaching an immediate, short-term goal or approximation.

In that post, I concluded that I would have to do "manual" tests to see where exactly the adapted algorithm was failing... *ok boomer*.
Although careful experiments were needed to detect the errors, I thought that recurring to pen and paper was too much.
I ended up preferring Gen-Z cutting-edge futuristic technologies instead and tested the algorithm in Jupyter Notebooks using small instances of the ANPCP.
After four notebooks of testing and modifying the algorithm, it could finally solve the problem for any $\alpha < p$.
You can check them out [here](https://github.com/netotz/alpha-neighbor-p-center-problem/tree/main/anpcp), they are the ones whose filenames start with `ieval_`, but only the introduction and conclusion explain something, I didn't document much the whole evaluation process.
The final version of the algorithm was baptized as the *Alpha Fast Vertex Substitution* (A-FVS) local search heuristic, because it considers the $\alpha$th closest center, it is fast, and it substitutes one vertex for another.

Since then, I have been able to write most of the thesis (**big shout-out to [my mentor](https://yalma.fime.uanl.mx/~roger/work/index.html) and my revisors!**), which I think is more understandable than the previous post, so I'm going to put here the chapter that reviews the background of the fast interchange concept and explains the observations that led us to the birth of the A-FVS.
To the best of our knowledge at the time writing this post, implementing a fast interchange local search heuristic to solve the ANPCP hasn't been considered before. 

# The local search heuristic

A solution to the ANPCP can be improved by applying a local search procedure, i.e., modifying one or more components of the solution based on some rule, looking for a better objective function value among the space of candidate solutions or neighborhoods, until a local optimum is reached.
A well-known local search procedure is the *vertex substitution method*, also known as the *greedy interchange* or *swap*.
This method consists of replacing one of the facilities in the solution, denoted as $f_r$, for another one outside the solution, denoted as $f_i$.
The procedure is repeated until no more improvements are found (local optimum reached).

A straightforward local search algorithm was first implemented for the ANPCP, identified as Naive Interchange (NI).
This algorithm simply iterates over the set of open facilities and, for each of them, iterates the set of closed facilities.
At each iteration, it temporarily removes the current open facility, adds the closed facility, and recalculates the objective function value.
This procedure does $O(p^2mn)$ operations, resulting in inefficient performance, based on preliminary experiments.
For this reason, we propose a faster vertex substitution algorithm to obtain solutions faster.

## Fast Algorithm for the Greedy Interchange (FAGI)

The **fast algorithm for the greedy interchange** (FAGI) was first proposed for the $p$-median problem (PMP) by Whitaker (1983).
The key aspect of this implementation is its ability to find the most profitable candidate facility for removal ($f_r$), given a certain candidate facility for insertion ($f_i$), while partially evaluating the objective function.
What makes this procedure fast is the observation that the profit that would result from applying a swap can be decomposed into two components.
The first one, called *gain*, identifies the users who would benefit from the insertion of $f_i$ into the solution because each user is closer to it than its current closest facility.
The second component, *netloss*, accounts for all other users that would not benefit from the insertion of $f_i$.
If $f_r$ is the current closest facility of a user, then it would have to be reassigned either to its second closest one or to $f_i$, whichever is the closest.
In both cases, the cost of serving that user will either increase or remain constant.
These two components are useful as they work as auxiliary data structures that make the algorithm fast, which has a worst-case time complexity of $O(mn)$.

The FAGI was implemented and tested for the ANPCP.
Even though this algorithm uses the objective function of the PMP, no solutions were obtained successfully (the algorithm was getting stuck when $\alpha \ge 2$, even with small instances).
After analyzing how the algorithm was taking decisions and updating the auxiliary data structures, we observed some key differences between the PMP and the ANPCP which were later crucial to propose an improved adaptation that could obtain solutions for any value of $\alpha$ (where $\alpha < p$).

In the PMP, as well as in the PCP, users are assigned to their closest open facility.
Although their objective functions are evaluated differently, in both problems the centers have the same definition.
Nonetheless, the definition of a center changes for the ANPCP, because users are assigned not to their closest open facility but their \alphath closest open facility, meaning that there are $\alpha - 1$ open facilities that are closer to any user $u$ than its center.

More formally, let $\phi_k(u)$ be the \kth closest open facility of any user $u$,

$$
\begin{equation}
    \phi_\alpha(u) \text{ is determined by all } \{ \phi_k(u) \mid 1 \le k < \alpha \}
\end{equation}
$$

If for $u$, one of the open facilities closer than its current center is considered to be $f_r$, then this center would no longer be the \alphath closest open facility.
Hence, the new center of $u$ denoted as $\phi_\alpha'(u)$ would, by definition, be moved to the next farther open facility, which would be the current $\phi_{\alpha + 1}(u)$.

A visual representation is shown below, where $f_r$ is the gray node, the black straight line is the assignment of $u$ to its center, and the red line and outline indicate which node will be the new center of $u$ after removing $f_r$.

![](/images/not_attracted.png)
> Resulting new center of $u$, $\phi_\alpha'(u)$, after removing its current center $\phi_\alpha(u)$.

Similarly, the fact that $u$ is attracted to $f_i$ does not necessarily mean that $f_i$ is going to be its new center: it depends on how close $f_i$ is from $u$.
This is represented visually in \autoref{fig:attracted}. The light red nodes are open facilities, and the dotted lines represent the distances from $u$ to the open facilities that are closer than its center.

Mathematically, let $d_k(u) = d(u, \phi_k(u))$ and $d_i(u) = d(u, f_i)$, then

$$
\begin{equation}
    \phi_\alpha'(u) \gets
        \begin{cases}
            f_i,
                & \text{if } d_{\alpha - 1}(u) < d_i(u) < d_\alpha(u) \\\\
            \phi_{\alpha - 1}(u),
                & \text{if } d_i(u) < d_{\alpha - 1}(u)
        \end{cases}
\end{equation}
$$

# References

[^1]: R. A. Whitaker.
A fast algorithm for the greedy interchange for large-scale clustering and median location problems.
*INFOR*, 21(2):95–108, 1983.

[^2]: J. Sánchez-Oro, A. D. López-Sánchez, A. G. Hernández-Díaz, and A. Duarte.
GRASP with strategic oscillation for the $\alpha$-neighbor $p$-center problem.
*European Journal of Operational Research*, 2022.


