---
title: "[Code Simplicity] Software Design"
date: "2019-10-22"
categories:
  - "book-notes"
layout: post
subclass: post
navigation: "true"
cover: "assets/images_1/export-3.png"
---

**[Code Simplicity - The Fundamentals of Software](http://shop.oreilly.com/product/0636920022251.do)**

What is a computer program ? A computer program can be viewed as:

- A set of instructions given by the programmer to the computer.
- A set of actions observed by the users that the computer produces as the result of the given instructions

Both definitions are correct and are constrainsted by your role with respect to the computer program.

It is the second definition that is of interests. If the computer program constitutes a set of actions, then for what purpose does that set of actions serve ?

The answer is, to help the users do something with much less efforts than the alternatives. Here lies the first description of what software designs do (or should do):

#### To allow us to write computer programs that help people

All software designs must necessarily attempt to achieve this purpose. A software doesn't help an inanimate object, or even another software, but its ultimate goal is to always help people to do their business. People don't want to deal with things that are not directly related to their business, there's a 'busy' in 'business'

Thus, the ability to design a good program corresponds to the ability to help people. To help people is to identify and extract the essentials that people need, and to describe these essentials in terms of the language of programming. It's often easy to find people who know programming, but it's not so easy to find people who know how to use programming to help people.

In order to be truly helpful, the program must not self-destruct (or break down) after a one-time usage, but must continue to help the end users tomorrow. Thus comes the second objective of software designs:

#### To allow our computer programs to continue being helpful to people

All software designs must possess the quality of enabling changes. This is because in order to continue being of help to people, a computer program must undergo modifications. These modifications can either be a reflection of modifications in existing elements within the process that people use, or a result of new elements being introduced to this process. Either way, changes are inevitable, and software designs must accommodate this inevitability constantly.

This means that a good software designer, aside from the ability to help people, must be able to think about helping people in the long term. Besides being able to extract what people essentially need, one must be able to describe such essentials in a way that make it easy for others to describe other essentials.

That means a software designer is more than a programmer. A programmer is someone who knows how to program, while a software designer (developer) is someone who creates a software design:

#### To allow the programmers to create and design computer programs that help - and continue to help - people

A software design, then, is a hierarchy of values where the guiding values/principles is ease of creation and maintenance, all of which to be helpful.
