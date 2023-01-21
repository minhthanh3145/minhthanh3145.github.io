---
title: "Dining Philosophers: An intuitive interpretation of the hygiene solution"
date: "2020-04-10"
tags:
  - "conflict-resolution"
  - "dining-philosophers"
  - "hygiene-solution"
layout: post
subclass: post
navigation: "true"
cover: "assets/images_1/untitled.png"
---

In this blog post, I will introduce the dining philosophers problems, and discuss [Chandy and Misra's solution](https://www.cs.utexas.edu/users/misra/scannedPdf.dir/DrinkingPhil.pdf) (which is also referred as the hygiene solution).

It's not a formal discussion, but rather an intuitive interpretation, therefore I will omit details that I don't think are conducive to my interpretation, and expand on details that I think need elaboration. I highly recommend you read the original paper to obtain the algorithm which can be used for implementation.

## **introduction**

The drinking philosopher problem provides a framework to think about conflict resolution between multiple processes. The problem contains a number of processes represented as philosophers, and a number of resources represented as bottles. A philosopher may be thinking, thirsty or drinking. Two philosophers come into conflicts with each other when they become thirsty simultaneously.

When conflicts arise, it is only resolved when a process obtains exclusive access to a number of shared resources and uses them to transition away from the conflicting state. In other words, when a philosopher is thirsty, he (or she, or whichever pronoun you identify with) must drink from the bottles and become a thinking philosopher.

The dining philosopher is a particular variation of the drinking philosopher problem, where a philosopher requires all resources shared by its neighbors to complete its drinking session. A consequence of this constraint is that conflicting philosophers cannot drink/eat simultaneously. In the dining philosophers problem, resources are called forks, and the state in which the resources are actually accessed is called eating. In this post, we are concerned with the dining philosopher problem.

## **Problem** **representation**

Conflicts in a distributed system can be represented as an undirected graph G where an edge between two nodes represents a possibility that two nodes may be conflicted.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/export.png?w=479)

A, B and C may be in conflicts

## **Solution** **formulation**

In order to resolve conflicts, one process must yield shared resources to the other process in finite time. The process which yields naturally takes on a lower precedence, and its counterpart a higher precedence. Conflict resolution thus amounts to a mechanism that assigns such a precedence ordering for any pair of conflicting processes.

### **Abstract Requirements**

There are the probabilistic and the deterministic approach. We are concerned with a deterministic solution in this post only. These desirabilities lead to the following requirements

1. **Distinguishability** A deterministic solution, regardless of how it is implemented, must select a process among conflicting processes to be the winner, which means conflicting processes must be distinguishable.
2. **Fairness** For each process, if conflicts arise, the deterministic solution must allow for a resolution in favor of that process in bounded time. In other words, no philosophers suffer from terminal dehydration.

### **The Hygiene Solution**

A solution can be conceptually represented as a directed graph that imposes upon the undirected graph of the problem. The direction of the edge indicates precedence: The arrow is directed away from the process with higher precedence, and points to the process with lower precedence. The process with lower precedence must yield to the process with higher precedence in finite time.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/export-1.png?w=359)

A has higher precedence than B and C

#### **Use Acyclicity and Depth of a Node to ensure Distinguishability**

If the solution graph is acyclic, then depth of a node is a distinguishing property. Depth of a node is the maximum number of edges to it from any node without predecessors. To see this is true, we employ proof by contradiction.

Assuming two neighbors have the same depth, **d(A) = d(B)**. Since A and B are neighbors they must be connected. If there's an edge directed from A to B, then the depth of A can be recursively defined as the depth of B plus one, i.e **d(A) = d(B) + 1**, which leads to a contradiction. The opposite also leads to a contradiction. Thus, by proof of contradiction, two neighbors cannot have the same depth, therefore depth a distinguishing property.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/export-1-1.png?w=359)

A, B and C are in a conflict but A is the winner

It is important to note that the graph must be acyclic. In a cyclic graph, the concept of depth of a node does not make sense. Therefore, our solution which uses depth of a node as the distinguishing property must initialize the processes so that the graph is acyclic, and maintain acyclicity throughout its lifetime.

To guarantee acyclicity, it is enough to guarantee that there is a node that does not have any outgoing edge. If the graph is acyclic to begin with, there must already be a node without any outgoing edge. Therefore our solution guarantees acyclicity if, in a single atomic action, it simultaneously turns the precedences of all edges incident on a process. Let's return to this requirement later.

#### **Use Auxiliary Information to ensure Fairness**

**Why must we use auxiliary information ?**

In order to ensure fairness, a process cannot hold on to their resources forever, and must give up when requested when it is no longer using them. Phrased in the language of the problem, a philosopher must give up his forks at some point in time. The ever important question is when ?

An eating philosopher needs all forks to eat so he cannot give up any fork while eating. But sooner or later, he will become a non-eating philosopher and no longer uses the resources. However, without any additional rule, hungry neighbor philosophers may keep demanding and giving up forks to and from one another. A consequence is that they end up never having enough forks to start eating, which leads to starvation for all processes involved. Therefore we must introduce auxiliary information to determine _when does a non-eating philosopher gives up a fork upon request_s.

We call these information auxiliary because they do not belong to the problem or solution's abstract conceptualization. They are concrete rules and information that we introduce in order to realize a particular solution.

**How do auxiliary information help** ?

Since we are focusing on the dining philosophers problem, resources will be interchangeably referred as forks. The first piece of auxiliary information is the state of the forks. A fork is either _dirty_ or _clean._ Using this state information, the following rules are introduced one by one.

A philosopher in the dining philosopher problem needs all forks from his neighbors to start eating. A failure to obtain even one fork may lead to starvation, so we need a rule to ensure that all forks will eventually be gathered. Therefore, **(1) a non-eating philosopher, gives up the shared dirty fork, but not the shared clean fork to its neighbor when requested.**

This rule plays an important part in guaranteeing that any process can gather enough forks to eat by never giving up obtained clean forks and keep requesting dirty forks from its neighbors. It constitutes the main body of the answer to the central question: _When does a non-eating philosopher give up a fork upon request_s. Other rules in this section elaborate on this rule.

We still need to define _when_ a fork is dirty. Since a non-eating philosopher gives up dirty forks when requested, a dirty fork represents a resource that is available for request. Therefore, we associate it with the states of the philosopher as followed: **(2)** **a fork is dirty when it is being used by an eating philosopher, and remains dirty until it is cleaned**.

Technically, by the above line of reasoning, it would be more natural to mark a fork dirty only after the philosopher is done eating, because the resources are not technically available when the philosopher is eating. But since an eating philosopher does not give up any type of forks even when requested, marking it dirty when it is being used will not make a difference. Therefore the choice depends on wether or not you feel more natural to associate dirty forks with eating or post-eating state.

Next, we need to define _when_ a fork is clean. Since there are two states of the fork, the question can be rephrased as _when_ the transition from dirty to clean occurs. This transition must happen at some point in time. However, this point isn't arbitrary because there's a situation which depends on the state of the fork that, if allowed to happen, would prevent fairness property from being achieved. We must choose a point in time that prevents such a situation from happening.

Such a situation happens when, upon arrival at the process which requested, it is still dirty. If the process which sent it then becomes hungry and demands the forks back immediately, the process which requested has to give up the resource it just obtained (per rule **(1)**), which leads to its starvation when the situation repeats. Therefore, a fork must be clean before it is sent, or equivalently, **(3) a philosopher must clean the fork before sending to its neighbor**.

To restate the point of this section, the auxiliary information above are introduced to ensure that the fairness property is achieved.

#### **precedence assignment mechanism**

It is worth mentioning a point made in **Solution Formation** section, which is that a conflict resolution amounts to a mechanism that assigns a precedence ordering for any pair of conflicting processes. In this section, we provide that mechanism. Looking at the state of a fork (_dirty_ or _clean_). and its location between two processes, we can assign precedence ordering to any pair of conflicting processes, using the following rules:

Since a non-eating philosopher gives up the shared dirty fork when requested (per rule **(1)**), any potential conflict will be resolved against him and in favor of his neighbor. Therefore **the process with the shared dirty fork has lower precedence**.

If the philosopher in possession of a clean fork and his neighbor becomes hungry simultaneously, he wins by default because he already has the resource and will not yield it (per rule **(1)**) . In other words, any potential conflict will be resolved in his favor and against his neighbor. Therefore **the process with the shared clean fork has higher precedence.**

**The process receiving a fork in transit has higher precedence** When a fork is in transit, we can infer that the philosopher giving it up has the lower precedence, and the philosopher receiving it has the higher precedence.

Here's a little image that summarizes the rules:

![](https://dafuqisthatblog.files.wordpress.com/2020/04/export-2.png?w=746)

Precedence scheme based on location and state of the shared fork

#### **A distributed implementation**

The final task is to implement the above mechanism. As noted, the solution must initialize the solution graph to be acyclic. If the initial state of forks are clean, and certain clean forks are possessed by a philosopher who reach transcendence and never becomes hungry (i.e a process that never uses its assigned resources), the resources will never be released, therefore the initial state of all forks must be dirty. That way, a transcendent philosophers can give up the dirty forks and allow his neighbors to eat.

The dirty forks are placed so that the precedence graph is acyclic, according to the precedence assignment mechanism. For the philosopher in the pair which is not initialized to have the shared dirty fork, he holds a shared request token for the fork. The position of the shared request token and the shared fork is initialized exclusive, meaning a philosopher initially cannot have both the shared request token and the shared fork.

The request token enables a distributed implementation of the hygiene solution. A distributed implementation means that decisions are made locally - by individual processes. If you look at the precedence assignment mechanism, a process can only determines its own precedence over a neighbor if it has the shared fork. If the fork is dirty, it has lower precedence, otherwise it has higher precedence.

But if it doesn't have the fork, then the mechanism provides no way for a process to know its precedence. In that case, a philosopher without the shared fork will have to consult a global watchtower to know its precedence with respect to a neighbor.

Because the initial configuration dictates that positions of dirty forks and positions of request tokens are exclusive. It means that philosophers with the request token are automatically aware that they have higher precedence.

The below images are taken directly from the original paper. Since the point of this post is not to formalize, but rather intuit, I will not discuss the formal algorithm.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/screen-shot-2020-04-10-at-11.58.24-pm.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/04/screen-shot-2020-04-10-at-11.58.32-pm.png?w=1024)

  

![](https://dafuqisthatblog.files.wordpress.com/2020/04/screen-shot-2020-04-10-at-11.58.41-pm.png?w=1024)

It is left as an exercise to the readers (LOL) to read the original paper.

## **Conclusion**

In this post, I (hopefully) provide an intuitive interpretation the dining philosophers problem, along with the hygiene solution. The next step is to implement this solution, I will update into this post when that step is accomplished.
