---
title: "Clean Architecture"
date: "2019-10-16"
categories:
  - "architecture"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

Lately I have been involved with a new project whose output is a cross-platform mobile application. I choose Flutter as the framework, and consequently Dart as the front-end language.

However, around this time I saw this awesome \[[series](https://www.youtube.com/watch?v=Ulk9qUErIa4&t=186s)\] about Flutter TDD Clean Architecture. It is a a really well-constructed course in my opinion. I have definitely drawn inspiration from this course in my own implementation of the Clean Architecture.

Because I don't have a habit of following anything too religiously, I have tried to adapt the materials of this course, along with various reading sources on Clean Architecture, in my application. This is my understanding, expressed in very few words compared to the number of existing interpretations of this architecture.

## Business Entities

The first motivation of The Clean Architecture, in my opinion, is that you want to delay design decisions that are not business-critical, and in order to do this your architecture must be organized around business objects, rather than things like wether Spring Boot is appropriate or not. Thus, the core of the Clean Architecture consists of Business Entities.

## Application-specific Rules

The next layer should be the application-specific rules, which are often referred to as Use Cases. A use case is how a user uses your application to achieve a specific business purpose. If your business objects are Teachers and Students, then a use case, called **Teacher assigns homework to Student** - which is also the business purpose of this use case, can take in a Teacher and a Student object and figure out if the Student is in one of the classes taught by the Teacher. Finally, the Teacher can assign the Student a homework after successful validation. This use case may feel similar to procedural programming where you list out the steps necessary to achieve a purpose.

Note that this observation may mean that if you adhere closely to Object-Oriented Design then the use case may look troublesome. I don't have any opinion on this, as I think it's fine to use multiple paradigms as long as we strive to be aware of possible conflicts and how to avoid them.

![](https://dafuqisthatblog.files.wordpress.com/2019/10/cleanarchitecture.jpg?w=772)

[https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

## Interface Adapters

The next layer is called Interface Adapters. In this layer, the most notable component is the Repository. A Repository, from the use case's perspective is merely an interface. A repository, from the perspective of any persistence framework, is the implementation which it provides. Because your use cases orchestrates the flow of data from and between business entities, which implies that your use cases most likely will require data, the Repository component serves as a stable interface so that your use case does not depend on the unstable specifics.

This layer will also contain the converter whose responsibility is to convert data from the most convenient form for the outer layers (frameworks) to the form most convenient for the use case and business entities. The reverse is also true. This layer is aware of the specificities of the outer layers because such awareness is a prerequisite for the purpose.

Viewing as a hole, the discussed three layers altogether adhere to the Dependency Inversion Principle because Use Case **depends** on the Repository Interface, and the Repository implementation **depends** on the Repository Interface, while if we draw out the flow of execution then the flow of control from the Use Case will inevitably go to the Repository Implementation.

## Frameworks, Drivers, UI

The outermost layer is UI, database and persistence frameworks. You can call this the layer of specificities where things may change unpredictably. The Clean Architecture dictates that changes on this level are expected to be at a frequent degree. This may or may not be true, depends on other aspects of your company which run a business whose meaning is broader than the "business" expressed in the innermost layer expresses.

## Clean Architecture and TDD

The benefit of this architecture is that it plays along with Test-Driven Development pretty nicely. Because in between the boundaries across layers we use interfaces (or abstract classes), mocking these interfaces are very straight-forward.

Moreover, the act of writing tests serve as the documentation for your thinking. When you look at a test, you can clearly see the intention of that test, and correspondingly the intention of the code being tested. It decreases cognitive loads so that you can focus on more high-level design thinking process and, if necessary, re-organization of code.

## Closing Thoughts

Clean Architecture promises you that you don't have to worry about dependencies like frameworks, which are design decisions, to a later point in time. However, I already chose Flutter framework and then saw the Flutter Architecture series, therefore my implementation of Clean Architecture seemingly resides within the Flutter application. Does it mean that the promise has been diminished ?

This made me question, because client-server architecture dictates that the business logic should belong to the server, not the client. The Flutter application is obviously the client, right ? So am I doing anything wrong ?

In retrospect, I did not see any need to use Flutter-specific functionalities, therefore my implementation so far is actually decoupled from any framework. This brings me to an important distinction, and a reminder, that Flutter is **only a UI framework to build widgets**. The code for business entities and use cases is written in Dart. This means, the code is not within a client, as the previous error reasoning dictate, so no, I am not doing anything wrong.

However, the code also is not within a server, because there is no server yet at the time I write this post. However, I have been able to write and test my business entities and use cases successfully, without having aware of the imposition of client-server architecture on my design.

Thus, as a closing thought, you can see that the Clean Architecture can actually help you defer even architectural decisions, in this case the decision to adhere to the client-server architecture or not.
