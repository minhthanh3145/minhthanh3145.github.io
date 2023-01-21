---
title: "[Code Simplicity] Equation of Software Design"
date: "2019-10-29"
categories:
  - "book-notes"
layout: post
subclass: post
navigation: "true"
cover: "assets/images_1/export-5.png"
---

**Disclaimer:** This is my attempt to read the book [Code Simplicity](http://shop.oreilly.com/product/0636920022251.do), I am not the author of these ideas, only an interpreter

The fundamental problem in software design is the question:

### **How do I make decisions about my software ?**

More specifically, faced with multiple possibilities, we must decide on a course of action. That is obvious enough, otherwise we won't get to anywhere. Making a choice is the only way to move from where we currently are. But the question of how to make such a decision is an elusive one.

When we speak of decisions about the software, what we really speak about is the decision to change. Implementing a new feature in a software changes the software, refactoring code changes the software. Every meaningful action in software development can be characterized as an attempt to change the software.

### **Equation of Software Design**

With that in mind, a decision about software can be thought of as:

**D = V/E**

Where:

- **D** stands for Desirability of a change. How much do we want to make this particular change to the software.
- **V** stands for Value of a change. As defined in the [previous post,](https://dafuqisthatblog.wordpress.com/2019/10/22/code-simplicity-software-design/) the goal of software designs is to help people, so Value will be measured in terms of how much it helps people.
- **E** stands for Effort necessary to make this change. This usually can be estimated in terms of days or weeks.

### **Value**

With values, we can think about value in terms of probability or potential.

For example, if your software does not allow connection through proxy, then a small number of enterprise users would not be able to use this, as the security requirement of enterprises is usually strict. Therefore the feature which allows your users to connect to the software using proxy is valuable because it has high potential value, despite affecting a small group of user.

Another example would be a feature to add a handy tool item on the UI for an action that is very commonly performed by your users. This feature is valuable because it affects a lot of users, despite the fact that they can all probably do without.

On the other hand, if you add a lot of items on the UI for actions that only get used once in a while, it really doesn't help much, and nobody uses it. That's not a valuable feature both in term of probability and potential.

### **Effort**

Efforts are tricky, because we've been told that there are ways to estimate the efforts in terms of man-power, or man-day, man-month, man-year. In reality, putting even an approximately close number of estimation is difficult enough.

Such a task is often left to more senior developers, who have experienced through inaccurate estimations and made adjustments to their internal models.

It helps to take into accounts factors not directly related to software development, such as the efforts to communicate, write documentation, testing activities, coordinations with sales/marketing teams.

> **In short, every single piece of time connected with a change is part of the effort cost**

### **Introducing time to the equation**

The equation, in its current form, has not yet taken into account the time dimension of software. Values and efforts are not only relevant in the presence, but also in the long run.

Values can actually increase overtime, as a single feature you rolled out becomes a unique point of attraction that set you apart from other tools. When your software gets mature enough, this distinction then becomes a valuable selling point, which is more valuable than it was rolled out.

On the other hand, values can also decrease overtime. If you decide to integrate your software with a third-party library, then there's an inherit risk that the third-party can become buggy which ripples through your software. There's nothing you can do about it because it's a third-party, users don't care if if it's a third-party because it's a feature of your software, they can be demotivated to proceed with the software. It can be the case that such integration was decided to put into production to ride a trend, and that would be the case where values decreased overtime.

Likewise, efforts involved in software development in the feature most often appear as the maintenance efforts. A feature that affects multiple parts of your code base is harder to maintain in the future by ourself, and by other developers.

Put it together, more realistically, our equation should look like:

**D = (Vp + Vf) / (Ei + Em)**

Where:

- Vp is the value of the feature at the present
- Vf is the value of the feature in the future
- Ei is the implementation effort of this feature
- Em is the maintenance effort of this feature

> **The desirability of a change is directly proportional to the value now plus the future value, and inversely proportional to the effort of implementation plus the effort of maintenance.**

### **Reducing the equation**

If you're mathematically inclined, then perhaps it would occur intuitively to you that, because we introduced a time dimension into the equation, we will reduce the equation by simply observing over a long period of time.

Because the present value and efforts of implementation for a feature is always constant, and that the future value and maintenance efforts change overtime, when we take the limit of the equation with time variable approaching infinity, the constants will be faded out. Now we have:

**D = Vf / Em**

> **In general, software systems evolve in a way such that the present value and effort of implementation almost always becomes insignificant in comparison with the future value and effort of maintenance over a long period of time.**

Features that are more valuable in the future are more desirable, and features that require less maintenance efforts in the future are also more desirable. However, if we are forced to choose, then we should always go with one that results in less maintenance efforts.

The rationale is simple, if a change gets easier and easier to maintain, then its desirability actually increases overtime, regardless of how much future value it's gonna bring about (as long as it's not negative). If instead, we choose the feature which has larger future values, then if the maintenance efforts exceed that values then the desirability decreases.

It's a more complex problem, which we sometimes have to solve. Another instinct is to reduce the implementation efforts of a feature. To say it bluntly, is to implement the feature as quickly as possible using all means necessary. As we can see from the equation, the implementation efforts dissipate over the long haul. That is why, one of the most important things to take from this equation of software design:

> **It is more important to reduce the effort of maintenance than it is to reduce the effort of implementation.**
