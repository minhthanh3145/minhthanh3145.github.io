---
title: "Continuous Integration, Deployment and Testing - what's the point?"
date: "2020-09-23"
categories:
  - "software-development"
tags:
  - "business-risks"
  - "continuous-delivery"
  - "continuous-integration"
  - "continuous-testing"
  - "economics-of-small-batch"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

We often hear the term CI/CD getting tossed around in conversations, particularly in the software world. It has become such a buzzword, like AI and Machine Learning, that our mind becomes saturated. As a result, we unconsciously (or consciously) don't even bother elaborating the term.

This article assumes that the readers are aware of the CI/CD concept and have a vague idea of how it works.

## Definitions

> In [software engineering](https://en.wikipedia.org/wiki/Software_engineering), **continuous integration** (**CI**) is the practice of merging all developers' working copies to a shared [mainline](https://en.wikipedia.org/wiki/Trunk_(software)) several times a day
> 
> https://en.wikipedia.org/wiki/Continuous\_integration

> **Continuous delivery** (**CD**) is a [software engineering](https://en.wikipedia.org/wiki/Software_engineering) approach in which teams produce software in short cycles, ensuring that the software can be reliably released at any time and, when releasing the software, without doing so manually
> 
> https://en.wikipedia.org/wiki/Continuous\_delivery

> **Continuous testing** is the process of executing [automated tests](https://en.wikipedia.org/wiki/Test_automation) as part of the software delivery pipeline to obtain immediate feedback on the business risks associated with a software release candidate
> 
> https://en.wikipedia.org/wiki/Continuous\_testing

## Continuous Integration

### Continuous Integration helps reduce risks

![](https://dafuqisthatblog.files.wordpress.com/2020/09/mermaid-diagram-20200923123513.png?w=615)

#### By reducing the size of merge conflicts

The job of a software developer, at a very literal level that doesn't account for all of its sophistication, is to modify the source code of an application so that it produces new behaviors. The source code is a dozen of text files written in one or more programming languages. The source code goes through a magical process called building that eventually turns the text files into an application.

An application has many features. Usually, a developer is responsible for one feature at a time. To implement the new feature, the developer modifies the source code on his local machine. However, a team usually has more than one developer, so the natural question is how would the features developed locally get incorporated together ?

Like most things nowadays, the answer is on the cloud. There are many cloud solutions that enable collaboration between developers by providing a central place to store the source code. An example is Git/GitHub. When a developer is finished with a feature, he or she pushes the source code into GitHub. The feature then become immediately available to other developers who need to use that feature to develop their own features.

However, there is a problem. If the source code is just a bunch of text files, and developing a feature amounts to modifying the source code, then it leads to the possibility that two developers working on two separate features may end up modifying the same line of the same text file. This is called a **merge-conflict**, and it happens a lot. Merge-conflicts are a natural problem arising from collaborations between developers.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image.png?w=1024)

When a merge-conflict happens, a developer has to manually resolve the conflict by making a decision: to keep the changes from both features, to discard one or the other, or to discard both. Either way, this has direct impact on the application, because the source code on the GitHub will be used to build the application. A wrong decision when resolving merge-conflicts may result in defects. Therefore, this merge-conflict thing is risky.

Herein the first benefit of continuous integration: Continuous Integration helps reduce risks by reducing the size of merge conflicts. Not doing continuous integration means the team integrates the code into a shared repository infrequently. Often times, this means pushing the code for a feature _only after the feature has been done_. This leads to big merge conflicts, because there are more code and therefore more places where conflicts could occur. Big merge conflicts are risker than small merge conflicts.

#### By preventing programming errors from going downstream

The other important role of continuous integration is verifying the source code automatically.

After reducing the size of merge-conflicts to reduce risks, we are left with literally everything else that could go wrong with software. There are many types of bugs that may happen, with different degrees of difficulty to discover. Correspondingly, there are many types of tests with different associated costs, such as unit tests, integration tests, end-to-end test (end-to-end tests often take the longest to run).

Therefore the problem is really a Sequencing Problem: what is the optimal sequence of tests to run to discover bugs. **Continuous Integration solves this by sequencing first that which adds value most cheaply**. Unit tests are the cheapest types of tests that add the most value: an application cannot function properly if its individual programs (or units) do not function properly.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-1.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-2.png?w=1024)

In continuous integration, after the developers merge the code into a shared repository, unit tests are executed in order to verify if the programs are working as expected. Let's say you want to build a stabbing robot because you've just seen the movie John Wick. The robot has a stabbing application installed which controls the hand movement.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-9.png?w=1024)

I mentioned that unit tests are used to verify programs. Here's where the difference between programs and applications is relevant. A program is a set of instruction that can be executed on a computer, while an application is a set of useful programs that help people perform functions, tasks or activities. In a more simple language, a program is a piece of code that does very small and atomic things, and a software is a collection of pieces of code that altogether produce more complex behaviors.

To build a stabbing application, a developer will develop a set of programs that are orchestrated in a way that produces a stabbing behavior (which is a more complex behavior). For example, there may be a program that picks up a knife, the program that lifts the right arm, the program that lowers the right arm. They altogether constitute a stabbing behavior.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/mermaid-diagram-20200923123532.png?w=744)

In order for the stabbing application to work as expected, the individual programs must work as expected. Testing individual programs is much much faster than testing the application.

Just imagine an automatic production line where the first phase verifies if the knife is taken by the robot, the second phase verifies if the robot's right arm is at a certain height, and the third phase verifies if the robot's right arm is at a lower height.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-7.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/09/mermaid-diagram-20200923123634.png?w=945)

This analogy of an automatic production line emphasizes the fact that testing individual programs can be done automatically and fast. In the software world, that part of the production line is a set of unit tests.

When a developer pushes the code of a set of programs (or features) into a shared repository, a set of unit tests will be executed. These unit tests not only test the newly written programs, but all the programs that have been developed previously, to make sure that the new programs don't invalidate the old programs

Go back to our robot, imagine that the robot has another drinking application installed that keeps it functional by drinking from the bottle of oil on the left hand, much like how people must drink water to survive. If during the development of the stabbing application, the right arm grabs the knife but the developer wrote the code to make the left arm let go of the bottle of oil. In this case, the stabbing application is invalidating the drinking applicatio.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/mermaid-diagram-20200923123643.png?w=685)

With unit tests, we want to make sure that all the smaller programs that make up our application don't invalidate each other's behaviors.

Unit tests are fast to run, but passed unit tests do not equate working software.

![](https://dafuqisthatblog.files.wordpress.com/2022/10/image.png?w=500)

https://natooktesting.wordpress.com/2017/08/24/x-unit-tests-0-integration-tests/

This is why people include not only unit tests but other kinds of tests as well into Continuous Integration pipeline. But what is faster to run gets prioritized. By continuously integrating code into a shared repository and automatically running tests against the repo, we reduce the risk of defects by detecting early and preventing programming errors from going downstream.

## Continuous Testing

Traditionally, software is developed to enable business process: inventory software enables better inventory process, Powerpoint enables better presentation.

However, businesses have come to find softwares to be the primary differentiator.

> Another example is mobile check deposit applications. In 2011, top banks were racing to provide this must-have feature. By 2012, mobile check deposit became the leading driver for bank selection (Zhen, 2012). Getting a secure, reliable application to market was suddenly business critical. With low switching costs associated with online banking, financial institutions unable to innovate were threatened with customer defection.  
> [Source](http://uploads.pnsqc.org/2015/papers/t-007_Ariola_paper.pdf)

Achieving a differentiable competitive advantage by being first to market with innovative software drives shareholder value. Therefore, business wants to get software to the market faster and faster.

However, software become complex very quickly. Risks of failures associated with software increase through every release. Without understanding of the risks, decisions may be made that cause a loss in shareholder values for business:

> Parasoft analyzed the most notable software failures in 2012 and 2013; each incident initiated an average -3.35% decline in stock price, which equates to an average of negative $ 2.15 billion loss of market capitalization. This is a tremendous loss of shareholder value.  
> [DevOps: Are you pushing bugs to your clients faster ?](http://uploads.pnsqc.org/2015/papers/t-007_Ariola_paper.pdf)

To avoid the loss of Shareholder Value, business needs to understand the risk associated with the software _at any given point in time_ so that trade-offs can be taken into account when making decisions. But it takes a lot of experience and practice to understand the development of software and to appreciate the complexity in doing so. This makes it hard to communicate technical decisions such as to refactor code because it it protects developers against invisible obstacles.

Therefore we need to bridge the gap between what the business expects from the software versus what developers produce. This is what drives continuous testing - the need for business to understand the risk associated with software at any given point in time.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-3.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-4.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-5.png?w=1024)

![](https://dafuqisthatblog.files.wordpress.com/2020/09/image-6.png?w=1024)

## Continuous Delivery

Continuous Delivery is the ability to get changes into the hands of users in a safe, fast and sustainable manner.

### Small batch size is desirable

**It speeds up learning**. Smaller batches mean faster feedback, which means faster learning. A developer receives feedback about the quality of his work faster if his work is small and can be tested quickly.

**It improves productivity**. When a developer is working on a task, and is interrupted by an important bug from a task that he finished sometimes ago but just now tested, he has to switch context which incurs attention residue.

**It makes it easier to fix a problem**. Fewer features in a release means that when a faulty behavior occurs we can more quickly and easily identify which feature it originates from. For a release with dozens of features, it is more difficult to track down the cause of a particular behavior.

**It allows us to drop features and avoid sunk cost fallacy**, which is when individuals continue a behavior because of previously invested resource (time, money, efforts).

### The optimal batch size is where the aggregation of transaction cost and holding cost is minimal.

![](https://dafuqisthatblog.files.wordpress.com/2020/09/pasted-image-2.png?w=300)

For example, you go to a store to buy woods for the winter. You have two choices:

- To go to the store once and buy a lot of woods. The transaction cost is low (one-time fuel money), big size batch and high holding cost (since you'll need a place to store the wood, and preserve it so that it doesn't become unusable).

- To go to the store multiple times and buy a little of wood each time. The transaction cost is high (more money more fuel), small size batch and low holding cost.

The optimal batch size is where the aggregation of transaction cost and holding cost is minimal. U-curve has a flat bottom, therefore missing the exact optimum costs very little. This insensitivity is practically important because it's hard to have accurate information.

In software development, transaction cost can be reduced by using automated testing, reducing transaction cost shifts the optimal batch size to the left, which means working with smaller batch size is economically justified.

Finally, holding costs usually increase faster than linear rate in product development. For example, it's expontentially harder to locate the root cause of a bug when a particular build contains more features. (smaller changes mean less debug complexity).Market is very unpredictable, and delaying a particular feature may lose us competitive advantages.

### Transaction costs in Testing phase

Running a test cycle incurs fixed transaction costs such as:

- Building features into testable package.

- Initialize and configure test environments.

- Populating test data.

- Running regression tests.

### Continuous Delivery is about making small batches economically viable

Because small batch size is desirable, but there are always transaction costs in testing phase which makes small batches economically unattractive, a key goal of continuous delivery is to change the economics of the software delivery process to make it economically viable to work in small batches so we can obtain the many benefits of this approach.
