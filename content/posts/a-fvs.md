---
type: post
title: "The Alpha Fast Vertex Substitution algorithm"
description: "A new fast local search heuristic for a location problem."
date: 2022-07-19
tags: ["operations research", "optimization", "math", "computer science", "algorithms", "data structures", "thesis"]
mathjax: true
---

TL;DR: 

---

Earlier in the year I wrote about [how the objective function of an optimization problem is decomposed](../decomposing-of) and partially evaluated so an algorithm that uses that function runs faster.
I found this technique so interesting that I tried to explain it in that post.
But the goal wasn't to just understand the decomposing of an objective function.
I needed to design a fast algorithm to solve the $\alpha$-neighbor $p$-center problem (ANPCP), as I mentioned in the other post, and while researching to do so, I came across the concept of the *fast interchange* or *fast swap* and how it was applied to other location problems (not the ANPCP).
I also wrote about it in that post, however, at that moment I didn't have a working algorithm for the problem, and I just noticed that I didn't include some definitions there like the meaning of *heuristic*.

I concluded that post saying that I would have to do "manual" tests to see where exactly the adapted algorithm was failing... *ok boomer*.
Although careful experiments were needed to detect the errors, recurring to pen and paper was my inner boomer manifesting.
I ended up preferring Gen-Z cutting-edge futuristic technologies instead and tested the algorithm in Jupyter Notebooks using small instances of the ANPCP.
After four notebooks of testing and modifying the algorithm, it could finally solve the problem for any $\alpha < p$.
You can check them out [here](https://github.com/netotz/alpha-neighbor-p-center-problem/tree/main/anpcp), they are the ones whose filenames start with `ieval_`, but only the introduction and conclusion explain something, I didn't document much the whole evaluation process.
The final version of the algorithm was baptized as the *Alpha Fast Vertex Substitution* (A-FVS) local search heuristic, because it considers the $\alpha$th closest center, it is fast, and it substitutes one vertex for another.

Since then, I have been able to write most of the thesis (**big shout-out to [my mentor](https://yalma.fime.uanl.mx/~roger/work/index.html) and my revisors!**), which I think is more understandable than the previous post, so I'm going to put here the chapter that reviews the background of the fast interchange concept and explains the observations that led to the birth of the A-FVS.
