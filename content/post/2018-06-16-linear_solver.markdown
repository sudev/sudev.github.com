---
categories:
- notes
comments: true
date: 2018-06-16T00:00:00Z
tags:
- Optimization
- Simplex
- Scala
- Apache Commons
title: Linear Solvers
draft: true
---

### Problem

You are given a table with available trucks.

* Truck volume is the maximum volume of goods that the truck can carry.
* Truck Cost is the cost of hiring a truck(say $100).

Assume that total capacity that we need to serve is 1400, find out the number of truck that we should hire in each type such that the total cost is *minimum*.

Total cost of operation can be expressed as the following formula.

$$  \sum N_iC_i $$

The volume constrain will be of the following form. 

$$  \sum N_iV_i > 1400 $$



Sample dataset.

| Truck Type | Truck Volume $$ V_i $$ | Truck Cost $$ C_i $$ | Required number of trucks $$ N_i $$ |
| ---------- | ------------------ | ---------------- | ----------------------------------- |
| A          | 10                 | 80               | ??                             |
| B          | 12                 | 100              | ??                             |
| C          | 15                 | 180              | ??                             |
| D          | 25                 | 350              | ??                            |

## Solution

The problem belongs to the category of [constrained optimization][co], we can use a *linear programming constrain solver* here which can minimise the `total cost` with the `constrain` as given above. There are many types of constrain solvers; here we have both the constrain and minimization function is linear we can use linear constrain solvers. 

I had done linear programming during my engineering but I had brush up lot of those while working on this problem here is a nice leacture from [MIT open courseware.][mlp]

Linear constrain problems are solvable in a polynomial time(average case simplex is polynomial even though the worst case is exponential), but if have constrains where the solution needs to be integers then the complexity becomes exponential.

#### Linear Programming Standard form

*Minimise or maximise linear onjective functions subject to linear inequalities or equations.* 

*Varibales*
$$
\space \vec{x}
$$
*Objective function*
$$
\vec{c}.\vec{x}
$$
*Inequalities*
$$
A\vec{x} \leq \vec{b}
$$

##### Certificate of optimality

Is there a ceritificate that shows LP solution is optimum?

##### LP Duality





[co]: https://en.wikipedia.org/wiki/Constrained_optimization	"Constrained Optimizations"

[mlp]: https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-046j-design-and-analysis-of-algorithms-spring-2015/lecture-videos/lecture-15-linear-programming-lp-reductions-simplex/	"MIT Linear programming lecture"

