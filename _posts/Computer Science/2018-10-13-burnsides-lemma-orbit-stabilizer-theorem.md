---
title: "Burnside's Lemma: Orbit-Stabilizer Theorem"
date: "2018-10-13"
categories:
  - "mathematics"
tags:
  - "burnsides-lemma"
  - "group-theory"
  - "orbit-stabilizer-theorem"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

**Problem:** Given a 3 by 3 grid, with 5 colors. How many different ways to color the grid, given that two configurations are considered the same if they can be reached through rotations ( 0, 90, 180, 270 degrees )?

* * *

This problem was given in my Number Theory course. Somehow when I searched for the solution, I stumbled upon Group Theory concepts instead, so I guess there's a connection somewhere somehow. Anyway, the solution to this problem is to use something called the Burnside's Lemma, which states the following:

![1](images/1.png)

For now, it is literally just something multiplied with something equals something. But after this series, you will understand what this Lemma says and why it is true. You will also see the application of Burnside's Lemma in solving the above grid coloring problem.

I am going to assume a very passing familiarity with group theory. This is because I have a very passing familiarity with group theory ( even now ). This series is _heavily_ inspired by [this series](https://youtu.be/wKsqP3zL5bw), I highly recommend anyone with an aptitude for math and a lack of genius to watch it. Shout out to Ben1994 for explaining complex concepts in a manner that is ruthlessly clear. This first part is almost literally a transcript from his Orbit-Stabilizer Theorem videos.

In order to understand this Lemma, we first have to prove something called Orbit-Stabilizer Theorem first. The reason will become clear in the next post, but for now let's take the leap of faith that it's necessary for us to prove the following result:

![2.png](images/2.png)

Which states that: **The number of elements in the orbit of _a_, where _a_ is an element of A, is equal to the number of left cosets of stabilizer of _a_ that G partitions into.** 

Again, it is for now merely a collection of symbols. The presence of the terms _orbit_ and _stabilizer_ give rise to the name of the theorem obviously . In this post, we will set out to prove this theorem. First, let's set down some basics.

# Group action

A picture is worth a thousand words, so:

![3.png](images/3.png)

**G** is a set of actions and a composition table describes the composition of each action with every other action. **G** must contain an identity action, denote **e,** which when composed with any other action does nothing and returns the argument.

An intuitive way to think about a group action is a composition table between a set of actions and a set of elements. In other words, it is a composition table where each entry represents an element that results from applying a particular action to a particular element. It also needs to satisfy two properties, namely that the order of actions doesn't matter, and that the identity action does nothing to an element.

# Orbit

Pick any element from **A**, then we define the orbit of **a** to be the set of all elements that all actions in **G** can send **a** to. Once again, the result of an action on an element is another element.

![4.png](images/4.png)

Orbit of **a** is a subset of A, because every element in the orbit is an element of A.

# Stabilizer 

A stabilizer of **a** in **G** is the set of all actions that send **a** to itself.

![5.png](images/5.png)

A stabilizer of **a** is a subset of **G,** because every action in the stabilizer belongs to **G**. But more interestingly, a stabilizer is more than a subset, it is a subgroup of **G.** To prove this, we must prove that a stabilizer is a group itself, which means that it has to satisfy all properties of a group as defined by axioms in group theory.

There are four properties that need to be satisfied, however we don't need to prove the second one which is _associativity_.  This is because a stabilizer is a subset of **G**, which essentially means we simply get some actions from **G** and put into the stabilizer. This means that nothing hinders _associativity_ from being carried over too.

But we still need to check other three properties.

![6.png](images/6.png)

The first property:

![7.png](images/7.png)

The third property is too obvious, even to me. Since the identity action sends an element to itself, as defined in a group action, the identity action is in the stabilizer of **a.**

The fourth property.

![8.png](images/81.png)

We have proved that the stabilizer of **a** satisfies all properties of a group, it is indeed a subgroup in itself.

# Coset

![9.png](images/9.png)

A left coset can be intuitively thought of as a row of the composition table of **G.** Similarly, the right coset is a column of the composition table.

Since a subgroup is a group, we also have the left cosets of a subgroup.

![11.png](images/11.png)

The proof above shows that each left coset is disjoint from one another. We say that the left cosets of a subgroup in a group partitions the group. Intuitively, this means the table of composition is partitioned into rows which are left cosets. As a consequence, since the stabilizer of **a** is a subgroup, its left cosets also partition **G.** This result will be used in the following section.

#  The Orbit-Stabilizer Theorem

Okay, so let's get back to the Theorem which states something like this:

![2.png](images/21.png)

The left-hand side is already obvious, e.g, the number of elements in the orbit of **a**. However, the right-hand side seems more mysterious. We shall use the proven result above that the stabilizer of **a** is a subgroup, to instead understand the notion of index of a subgroup in a group.

![12.png](images/12.png)

Note that we only shows two left cosets, but there are more. To find a new left coset, we simply get an action **g** that is not in any of the old left cosets and compose it with the subgroup **H**. It is worth noting that the number of left cosets is not necessarily equal to the number of action in **G**. It is easy to be fooled by notations if you're not careful.

Back to the notion of index, it is merely a fancy way to count the number of left cosets of a subgroup in a group that the group partitions into. Once again, the stabilizer of **a** is a subgroup, so we have the exact same image, just replaced **H** with **Ga.** 

![13.png](images/13.png)

So the Orbit-Stabilizer Theorem really means that:

![14.png](images/14.png)

Where **G/Ga** is the set of left cosets of **Ga** in **G**.

If you think about it, then the number of elements in the orbit of **a** is equal to the number of left cosets of the stabilizer of **a** if and only if there exists a bijection between them. A bijection is another fancy term to say that each left coset of the stabilizer associates with one and only one element in the orbit.

The number of elements in a set is formally defined as the cardinality of the set. Two sets are defined to have the same cardinality if and only if there exists a bijection between them. Since a group is just a set of elements together with some operations, the definition that links cardinality and bijection till applies.

Now, having established all of that, then understanding the Orbit-Stabilizer Theorem is just equivalent to proving the bijection.

Wait, a left coset is basically a set of actions, right? But the **Orbit-Stabilizer Theorem speaks about the relationship between _set of corsets_ and an _orbit_**. We must prove that for a left corset, it acts on **a** and send it to one and only one element in the orbit of **a.** This, in turn, must necessarily imply that all actions in a left coset act on **a** and send it to the same element in the orbit of **a.** 

![15.png](images/15.png)

So now we understand that within a left coset all actions behave exactly the same way on **a.** The goal is to find a bijection between a left coset and an element in the orbit right ? This must mean that if we choose two representative actions from two different left cosets, then it must not act and send **a** to the same element in the orbit of **a.** 

![16.png](images/16.png)

Recall one thing in the Cosets section eariler, I proved that any two cosets are disjoint from one another. In other words, actions from one coset cannot be present in another coset. The proof above assumes that there exist two actions from two different cosets that will act on **a** the same way, sending to one element in the orbit of **a.** The chain of reasoning ultimately leads to the conclusion that such two actions must belong to the same coset, which contradicts the assumption. What we just proved is that there exists an injection between the set of left corsets and the orbit. That is, if two corsets act on **a** the same way, then they are the same corset.

**A bijection is defined as an injection plus a surjection**. Now we need to prove the existence of a surjection. This means that for any element in the orbit, there must exist some left corset that acts on **a** and send **a** to this element. We shall prove by contradiction:

![17.png](images/17.png)

Therefore, the bijection between a set of left corsets of the stabilizer and the orbit must exist. Consequently, the cardinality of these two sets must be equal, which is what are stated by the Orbit-Stabilizer Theorem.

In the next post, we will discover how using this Theorem we can prove Burnside's Lemma, and how to apply Burnside's Lemma to the grid coloring problem.

Stay tune !
