---
title: "A holistic look at how different product frameworks combine to empower product strategy"
date: "2021-10-13"
categories:
  - "product-development"
tags:
  - "jtbd"
  - "product-management"
layout: post
subclass: post
navigation: "true"
cover: "assets/images_1/playground-4.png"
---

I have written about [product strategy](https://dafuqis-that.com/2021/06/06/my-take-on-product-strategy/) as well as product development frameworks that are the bread and butter of my job as a product manager: JTBD, Opportunity-Solution Tree, The North Star Framework, Four Level of Strategy Deployment, Continuous Discovery, so on and so forth.

They said that the road to expert product development is paved with novices trying to apply frameworks. Well, I mean actually no one said that but me, but you get the point. I know I'm no expert, but I think there's nothing wrong with learning and applying what other people tried.

Anyway, the other day I was reflecting about what's been going with me recently in work and life. As part of that reflection, I thought that I gathered many mental models in my repertoire, but all frameworks are flawed, so perhaps there's some sort of relationships between the frameworks that can provide insights on how they should complement one another. After a few hours of flexing out my thinking, I finally arrived at something that kinda makes sense. So that's what I want to share in this post.

But first a disclaimer, product management is about handling the mess, so there's no "right" way to look at things. There are so many product frameworks out there, so this investigation only concerns the ones that I'm actually familiar with.

### A somewhat holistic attempt to view product development frameworks

The following is a simplified diagram of how I think about the relationships between certain product frameworks (open it in another top to view it more clearly). The rest of this blog post attempts to explain and expand on this diagram. There are plenty of good resources on the details of each of these frameworks, so I will primarily focus on the relationships instead of how to apply the frameworks specifically.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-7.png?w=1024)

### The inputs and outputs of product strategy

Stephen Bungay defines strategy in his book _The art of action_ as:

> Strategy is a deployable **decision-making framework**, enabling action to achieve **_desired outcomes_**, constrained by **_current capabilities_**, coherently aligned to the existing **_context_**.
> 
> Stephen Bungay - _The art of action_

It would be easier if we visualize it.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground.png?w=1024)

Some of these terms are somewhat unfamiliar. But we can translate them to more familiar terms as follows:

- **Desired outcomes are business outcomes**. Products are vehicles that enable value exchange between customers and businesses. We can't define what customers value, but we have the luxury to determine what our business values.
- **Capabilities are product changes that we can make**, this is the solution space constrained by the team's resource and competency.
- **Context is the activities that people do**. In [Christopher Alexander: A Primer](https://vimeo.com/491222729). Ryan Singer mentioned the idea of Context-Form Fit being the essential thing in design. Forms are the parts of the world that we have decided to create or change. The context is the activities that people are trying to do, the things going on. We can go along with or against the grain of this context. The fitness between context and form judges the success of a design. _Note:_ Even though this idea surfaces in buildings' design and architecture, I find it helpful in both software and product development.

After translating then into familiar terms, we get something like this:

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-1.png?w=1024)

A product strategy can be seen as a function that takes in business outcomes, possible product changes, activities people do and produce effective actions.

  
This serves as a framing that seems useful. The product frameworks I know either help identify the critical inputs to this definition of product strategy, and/or combine the inputs together to enable actions of some kinds.

### Company Vision helps identify business outcomes

First, we need to derive business outcomes from the company vision. A company vision explains where the company is going to be. Which route a company chooses depends on its maturity, development stage, and a host of other factors.

A vision is too long to be acted upon. That's where business outcomes come in. They communicate the company's current focus that helps realize the vision, which usually takes years.

Business outcomes may also be called strategic intents. They may focus on increase revenue, protect revenue, reduce costs and avoid costs.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/pasted-image-20210715234036.png?w=1024)

From Escaping the build trap - Melissa Perri

For example, Netflix had a clear strategic intent: "Lead the streaming market." All of its decisions - enabling internet-connected devices to focus on creating more content- helped achieve this goal. It pushed them in the right direction. Once Netflix realized the strategic intent, it changed its course to maintain its position by creating original content.

  
So the first framework I want to mention is the four levels of strategic deployment framework (in _Escaping The Build Trap)_ which involves the company vision and strategic intents as the starting point for doing strategy work. There are two other factors that will be explored in the later section.

![](https://dafuqisthatblog.files.wordpress.com/2022/06/pasted-image-20210715233458.png?w=1024)

So we have a starting point: from the 4-levels of strategy deployment, we know that we need to define the company vision and business outcomes to get started.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-3.png?w=1024)

### JTBD helps discover activities people do that need improvement

Okay, so onto the second component, which is the context in which our customers operate, or the activities that customers do. Here's the visualization of product strategy again as a reminder of what we're talking about.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-1.png?w=1024)

[Ryan Singer talked about the tendency to focus on designing forms](https://edovanroyen.com/notes-of-ryan-singers-introduction-to-christopher-alexander/). Most design requirements are expressed in terms of form. The form is what we want to create or change: like adding a button to the UI to change a setting. But a button is meaningful insofar as it enables people to do certain activities better. In other words, what essential is the Context-Form Fit.

The same tendency can also be seen in product development. Sometimes we build and deliver features without adequately thinking about their context. The Job-to-be-done framework counters this tendency by shifting the focus of product development to center around people's activities, or the jobs they're trying to get done.

When [applying JTBD, we produce an artifact called Job Map](https://scribe.rip/wrkshp/jobs-to-be-done-and-the-job-map-f1fc370fd42e). It describes all the jobs people do, and the underserved needs that make them difficult to perform. This is an output that empowers our strategy.

I'll keep them separate to highlight the input to product strategy, but in principle can combine them together and say that the output of the JTBD is the activities people do that need improvement.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-2.png?w=1024)

But the point of applying the JTBD framework is not so much about outputting the Job Map and the Underserved Needs, it is about helping you and your team gain empathy from your user problems. I have never walked away from a user interview where I did not learn anything new about the ways that my product is used.

So the activities that customers do and the business outcomes are defined. Next we have to connect them together.

### The North Star Framework helps produce metrics that align with business outcomes and activities customers do that need improvement

It is vital to have a metric because it enables optimization, but it is even more critical that the metric reflects both business and customer values. Otherwise, as Ryan Singer said, we risk falling into the trap of focusing on building forms (solutions) that fail to achieve Form-Context Fit. If it's not meaningful to the customers, then no value exchange is possible, which also renders the product - a vehicle for value exchange, meaningless.

But there are many user problems, and not all of them would maximize value for the business. There's a delicate balance that we have to maintain. This tends to be counterintuitive for those who have a high empathy, it is equally important that our business survives, strives and grows to continue to deliver values to customers.

The point of defining The North Star Metric is then not so much about defining the metrics. The point is to get you and your team to think about business outcomes and customer outcomes with equal consideration.

> [The North Star metric](https://amplitude.com/north-star) is a leading indicator that defines the relationship between _customer problems_ being solved and sustainable, long-term _business results_.

The inputs of the North Star Framework are customer problems and business outcomes. The outputs of the North Star Framework are North Star Metric itself and a set of [Input Metrics that influence the North Star Metric](https://amplitude.com/north-star/amplitudes-north-star-metric-and-inputs) (the NSM are usually lagging while the inputs are usually leading metrics).

For the sake of repeating myself, activities which people need to get done and which they want to get done better are customer problems. By solving customer problems, we create value for them. In exchange, customers pay us with their time, attention, and money, which help our business to grow.

> The heart of the North Star Framework is the North Star Metric, a single critical rate, count, or ratio that represents your product strategy.
> 
> Amplitude - [North Star Playbook](https://amplitude.com/north-star)

The North Star Metric framework connects JTBD and 4-level of strategy deployment, which gives us a picture that is now closer to being holistic.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-8.png?w=1024)

### Continuous Discovery helps discover opportunities and solutions

We've established that the outputs of the North Star Framework are the North Star Metric itself and a set of [Input Metrics that influence the North Star Metric](https://amplitude.com/north-star/amplitudes-north-star-metric-and-inputs). Next, to move these metrics, we must identify opportunities and craft solutions.

JTBD can be applied again in this phase if we only focus on identifying the big jobs and big underserved needs in the previous stage. But in this phase, usually we can dive into a particular job and further identify the specific underserved needs.

I want to mention Continuous Discovery process as the framework that can help us do this. Continuous Discovery is about talking users on a regular basis to identify valuable opportunities.

The point of Continuous Discovery on its own is about staying in touch with your users. But applying Continuous Discovery in isolation isn't enough, we must do that while keeping an eye on the metrics that maximize both business and customer values. That is where the North Star Framework, or the set of principles behind it, comes in.

Applying the Continuous Discovery process, we identify the opportunities as high-level bets (3-6 months) broken down into low-level bets (1-3 months). These opportunities are also called Product Initiatives in Four Level of Strategy Deployment.

The frameworks are beginning to blend in together. The "product initiative" and "option" components in the framework are enabled through practicing Continuous Discovery, which are connected with NSM so that it ensures that business values and customer values are maximized at the same time.

### Opportunity-Solution tree helps make product changes that align with opportunities which best solve company and customer problems simultaneously

Once we have opportunities aligned with business outcomes and customer problems that were enabled by Continuous Discovery, generating and mapping solutions is another step we have to make.

In [Continuous Discovery Habits](https://www.amazon.com/Continuous-Discovery-Habits-Discover-Products/dp/1736633309), Teresa Torres talked about Opportunity-Solution Tree, which does precisely this. An Opportunity-Solution tree assumes that at the root there's a north star metric which reflects the desired business outcome and customer values. The north star metric is broken down to inputs. Now we have to search for opportunities that move these inputs. These opportunities are discovered by practicing Continuous Discovery.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/pasted-image-20211002204200.png?w=1024)

Sizing the Opportunities based on their impact on customers, improving our positions on the market, and aligning with our company values. We can pick an opportunity and then generate solutions to address it.

  
Opportunity sizing is usually based on intuition (or product sense), but the Opportunity-Solution tree helps by giving us some visual indications. The depth and breadth of the opportunity space reflect the team's current understanding of their target customer. If our opportunity space is too shallow, it can guide us to do more customer interviews. An opportunity space that's too wide reminds us to narrow our focus. If we're not considering enough solutions for our target opportunity, we can hold an ideation session. If we don't have enough assumption tests in flight, we can ramp up our testing.

  
There are a lot of actions we can take from looking at the Opportunity-Solution Tree. So OST goes hand in hand with Continuous Discovery.

Now we have the complete picture of how the frameworks fit together to empower our strategy.

![](https://dafuqisthatblog.files.wordpress.com/2021/10/playground-7.png?w=1024)

### Product development frameworks should come in pack

Echoing the importance of Form-Context Fit yet again, product development frameworks need to fit into the context of what we're doing. There's no single right way of doing it. It's always about keeping an eye on different aspects of the current situation and then combining complementary frameworks so that we can create effective actions.

You shouldn't run Continuous Discovery without knowing what metrics you should move.

You can't define North Star Metrics without knowing the activities that your customers really engage in and what your business is about.

You can't map out an Opportunity-Solution tree without knowing the opportunities that align with business value and customer value.

Applying the JTBD framework alone risks solving customer problems without also solving business problems. So even though customers are happy, in the long run you wouldn't be able to hit your desired revenue target.

### The end

What do you think? Feel free to leave your thoughts and comments. I'd love to hear your opinions on this blog post or any of the ideas mentioned here.
