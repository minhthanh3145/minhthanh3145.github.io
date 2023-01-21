---
title: "Bottom-up parsers"
date: "2017-10-12"
categories:
  - "parser"
tags:
  - "bottom-up-parser"
  - "lalr1"
  - "lr0"
  - "lr1"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

# **Bottom-up parsing**

Bottom-up parser constructs the parse tree from the leaf towards the root. Why would we want to attempt to build this way ? Top-down parser uses one lookahead to choose immediately the right production to expand the parse tree. This seems efficient, and it is. But there the class of language that **LL(1)** parser can parse sucessfully is small. Usually we cannot use one lookahead to determine the production to use immediately. One would propose using two lookaheads, but that assumes that all productions are unique given the two lookaheads, that still leaves out all other languages whose productions have the same prefix up until arbitrary number of symbols. That's why we have bottom-up parsers, the class of language covered by this approach is much more large. In fact, most programming languages can be parsed using bottom-up parsers.

# SLR parser

This section discusses **SLR** parser. **SLR** stands for Simple **L**eft-to-right scan that trace **R**ightmost derivation in reverse. **SLR** parsers function by maintaining  a stack and using the top element to try to reduce some right-hand sides to a nonterminal before feeding characters on the input.

First let's talk about the choice of data structure: 

# **Why stack ?**

There are two ways to build a parse tree: rightmost derivation where we constantly expand the rightmost nonterminals and the leftmost derivation where we do the opposite. 

Due to the nature of rightmost derivation, the string being derived always has a prefix that contains solely of terminals. This prefix exists because the right-hand side of the start symbol can always splitted into two parts: One part before the first nonterminal  and the part contains the first nonterminal and the rest. Rightmost derivation always explores nonterminals inside the second part and never touches the first part. The parse tree always starts from the production of the start symbol, therefore at any point going downward from the root, the prefix that is the first part of the start symbol production's right-hand side always exists. This prefix can be empty if the start symbol production produces a nonterminal as the first symbol.

Leftmost derivation, by the same logic, derives a string that always has a suffix that contains solely of terminal.

Because we are building the parse tree towards the root, we are essentially doing the opposite of what a production does. Grammar productions take us from the root  nonterminal symbol to the fringe terminal symbols. Bottom-up parsers try to build from the fringe terminal symbols towards the root nonterminal symbol. An inverse of a production is called a reduction. Similarly, a reduction can occur in two ways: either traceback a leftmost derivation or traceback a rightmost derivation.

![sc_67.png](images/sc_671.png)

Let's focus on the parsing that traces back leftmost derivation. Parsing like this, we delay processing the rightmost terminal as much as possible and try to reduce the leftmost part first at any stage during the parse. Imagine the start symbol production                                **S -> AabcdBfghik** and production **A->z, B->e** where **A, B** is a nonterminal and the rest are terminals. This grammar accepts **zabcdefghik**. Using leftmost derivation, we have to consume z, a, b, c, d, e, f, g, h, i, k before we can reduce z into A and Aabcdefghik to S. Why ? Because the rule in tracing back leftmost derivation states that the rightmost string of terminals must be constant during any phase of the parse. But because the parser swallows tokens from left-to-right manner, the rightmost string of terminals **never** **is constant**. Therefore it will have to swallow tokens until no tokens are appended into the right side, only then it will perform any reduction.

Now imagine **S -> zabAeBgC**, **A->cd**, **B->f, C->hik.** This grammar also accepts **zabcdefghik**. Tracing back the rightmost derivation, we swallow z, a, b, c, d and then reduce cd to A. We continue swallowing e, f and reduce f to B, then we swallow h, i, k and reduce hik to C, finally we reduce abAeBgC to S. Why ? Because the rule in tracing back the rightmost derivation states that the leftmost string of terminals must be constant during any stage of the parse. And indeed, because the parser swallows tokens in left-to-right manner, changes only occur to the rightmost and the leftmost remains constant.

The parser calls the scanner for more tokens every time it doesnt have enough information to reduce. In the case of leftmost derivation tracing, the parser calls the scanner 11 times before performing the first reduction. In the case of rightmost derivation tracing, the parser calls the scanner 5 times before the first reduction.

Each reduction constructs a partially complete parse tree, therefore the longer the time between each redution is, the longer it takes for  a parse tree to be built. For this reason, **SLR** parsers parse from **l**eft-to-right, tracing **r**ightmost derivation in reverse. Given a set of grammar productions, we can always choose to implement a series of productions using rightmost derivation or leftmost derivation.The choice of derivation doesn't matter because parse tree only retains information about the rules applied but not the order in which they are applied. In real life, however, there is a difference between these two derivations depending on the context. Because the stream of tokens is produced from **left**\-to-**right**, which means changes in information occur to the **right** of the string being parsed while the **left** of the string remains constant. Rightmost derivation tracing parsers delay processing the left part and prioritize processing the **right** part of the string being parsed. This is effective because newly acquired information are immediately processed and combined with the constant part to perform reductions effectively.

A substring that is reducible to a nonterminal is called a **handle**. Back to the main question: **why stack ?** From the image above, we observe that at any time during the parse, a reducible part appears only on the right-end of the string being parsed. This means that a handle will always appear on the right-end and never on the left-end. This justifies the use of the stack because the stack is designed specifically for accessing and manipulating elements on one end efficiently.

# **What is on the stack ?**

We know why **SLR** parsers use the stack, now on to how the stack is used. Due to the nature of the stack, viabble actions are push and pop. Because we have established a fact that a handle can only appear on top of the stack, naturally a pop would mean reducing a handle. Pushing elements on the stack may have two meanings. Popping elements off forces us to push a nonterminal on because that's how reductions work. The other case where we push an element on the stack is when the current stack content doesn't have a handle and we have to consume more information to reveal a handle.

We know that a handle, if any, must appear on top of the stack. But due to nature of the stack, the only element accessible is the top element. Suppose during the parse at some points, we observe a symbol on top of the stack, how do we know that this marks a handle or a handle hasn't appeared yet. Rephrase the problem: **Having observed just the top element, how do we decide wether we have a handle and thus reduce it or we don't have a handle and keep on consuming information ?** 

This problem calls for serious thoughts on the type of elements inside the stack. Intuitively, we should only push terminals and nonterminals on the stack to correctly represent the string being parsed. We know the parse is successful when the start symbol is on the stack. Intuitively, this should be enough. But there is no basis upon which we can recognize a handle given the last terminal or nonterminal of that handle. This condition is even more stricter than that of LL(1) parsers and thus cannot be imposed on because that would make SLR parsers worthless.

We must need a data type that would represent a recognized handle. Only with the existence of this kind of data can we hope to use the stack to parse a sentence. It is necessary to carefully revise the concept of handle since it is the item of interest. Take for example the handle: _A_→_β__U_.  If we have _β__U_ on the stack, the handle is recognized. So we mark the position of the top stack by a dot:  _A_→_β__U •._ This combination of a production and a dot tells us about a **possible** situation where we want to find a handle for nonterminal A **and** have __β__U__ on the stack **and** the next step to recognize the handle is to reduce __β__U to A__ . "**Possible**" is the operative word here, it means that this is only a possible configuration of the parsing, we don't actually read any the stack while parsing. This possibility is constructed beforehand. The other two possible configurations for that handle is _A_→_•_ _β__U_ and _A_→ _β •_ _U_. Imagine if we were to represent each possible configuration by a NFA state and connections between them are represented by transitions on a terminal or nonterminal.

![sc_68.png](images/sc_68.png)

How does a NFA state can be appropriate for the data type we need ? Well we need a data type that encodes the information of the entire stack content so that by reading it we can choose an appropriate action. 

State **n0** says that want to find a handle for nonterminal A **and** the content of the stack does not have _β__U_ **and** the next step is to discover a _β_ . 

State **n1** says that we want to find a handle for nonterminal A **and** the content of the stack contains a _β_ **and** the next step is to discover an _U__._

State **n2** says that we want to find a handle for nonterminal A **and** the content of the stack contains βU **and** the next step is to reduce the handle. 

We should be reminded that each state encodes three information: The nonterminal whose handle we want to recognize, the stack content available to recognize the handle and the next step to take in order to recognize the handle. The first two are obvious, the last one is due to the fact that knowledge about all production rules are made available before parsing. Thus each state encodes the exact kind of information necessary to recognize any handle. An **NFA** state that encodes these information is called an **SLR** **item**, however we ought to call them NFA state to stick to what we already know.

Again, it is important to note that the states are constructed knowing only the production rules, therefore it can be done before the actual parsing occurs and without having to read the entire stack content.

**Why don't we construct states in such a way that they tell us all the terminals necessary immediately to recognize a handle so that we can simply consume and match the scanner's output against those foretold terminals until either a mismatch occurs or the handle is recognized, instead of just telling what immediate next step to do?** We don't do this because **it is brute force**. Starting with the start symbol, if we construct a state that tells us all terminals necessary to recognize the start symbol, it is equivalent to generating all possible sentences that the grammar can produce and then match the scanner's input against all these possible sentences. Furthermore, **the goal of a parser is not only to accept or reject the scanner's output but also to efficiently** **build the parse tree that represents why the output was accepted**. Using bruteforce does accept or reject a sentence and build the parse tree, but it builds the parse tree top-down and backtracking multiple of times, therefore **bruteforce is not efficient.**

Let's see if we can have more insights. State **n0** means three things as dicussed:

![sc_69.png](images/sc_69.png)

Now assuming **A** isn't the start symbol and is just a terminal on the right-hand side of the start symbol production, namely **S -> Aa**. How to establish the connections between these states since we have to start from the start symbol.![sc_70.png](images/sc_70.png)

To establish the connection between **m0** and **n0**, we observe the connection between the first information **n0** encodes and the third information **m0** encodes.

![sc_71.png](images/sc_711.png)

In **m0**, the next step to recognize S is to recognize A,  but since A is a nonterminal we can only recognize A by reducing some handles to A. These two statements are equivalent, and therefore we simply add an empty string transition between them to describe this equivalence. The connections between **m0**, **m1** and **m2** is obvious.

![sc_72.png](images/sc_721.png)

We add a production rule for **U** to conclude this example. The relationships between the states are constructed followed the same old logic.![sc_74.png](images/sc_74.png)

Because of the relationship between **m0** and **n0** being represented by an empty string transition, what we have is an NFA, not a DFA. But then again, computers do not like NFA because computers are deterministic. Facing with the same input, with the same logic, it has to produce the same output again and again and again - that is deterministic. Therefore we have to convert NFA to a DFA using **subset construction** ( [this post](https://dafuqisthatblog.wordpress.com/2017/09/18/compiler-scanners/) ).

![sc_75.png](images/sc_75.png)

Formally, the process of establishing empty string transitions between states is called **closure** and the process of establishing non-empty string transitions is called **goto**. We are lacking a transition on **S** symbol, we know once **S** appears on the stack the parse is finished, but the DFA itself doesn't have that kind of knowledge. Therefore we augment the current grammar with a production **S\`-> S**. This augmentation enables a transition on **S**. A final note, as far as the DFA concerns, **s6** is the only accepting state. As far as we concern, there are multiple accepting states that signal reducing handles. We do not really need a transition on **S** because once **S** appears we can conclude our success. The augmented production is added so that our model of **DFA** states is complete. ![sc_76.png](images/sc_76.png) 

**DFA state is a data type that is used on the stack**. For the above grammar, it recognizes **_β__βa_**. The stack operation occurs as followed:

1. First **s0** is pushed on because it represents the initial state, then we swallow a _**β**_**.** **s0** transitions on _**β**_ to reach **s1**. We push **s1** on the stack, we swallow another **_β_**. **s1** transitions on **_β_** to reach **s2**. **s2** is an accepting state so we know we have to reduce **_β_** to **U**.
2. We pop **s2** and **_β_** off the stack, revealing **s1** underneath and we push **U** on. **s1** transitions on **U** to reach **s3**. **s3** is an accepting state and thus we reduce **_βU_** to **A**.
3. We pop **s3**, **U**, **s1, _β_** off the stack, revealing **s1** underneath and push **A** on. **s1** transitions on **A** to reach **s4**. We push **s4** on and consume **_a_**. **s4** transitions on **_a_** to reach **s5**. **s5** is an accepting state and thus we reduce **Aa** to **S**.
4. We push **s5, a, S4, A** off the stack and push **S** on. Once **S** is on the stack, we know the parse is successful. 

But as far as the stack is concerned, it only reveals the top element. It is true that the **DFA** states encode information about handles, but the stack has no knowledge of this and is just a data structure to hold stuffs. This means that we have to have an mechanism to dictate wether what to push or pop. The simplest mechanism often used is tables. Given a **DFA** state and a terminal / nonterminal, the table specifies what actions should be performed.

There are two tables involved, specifically. _**goto**_ and _**action**_. **goto\[ i , j \] = k** says that state **i** transitions on the symbol **j** to reach state **k**. **action\[ i , j \]** can contain one of the three things: 

1. **"shift i"**:  The current state on the stacktop is **i** , push the next input symbol **j** and  **goto\[ i , j \]** on the stack.
2. **"reduce A -> a"**: pop 2x|a| elements off the stack ( or |a| pairs of states and symbols ). The stack revealled by the pop off is **i**, A is **j**. Push goto\[ **i** , **j** \] on the stack. 
3. **"accept"**: Finish parsing.

**goto\[ i , j \]** is constructed as we construct the DFA. Each time a transition from a DFA state on a symbol we fill in **goto\[ i , j \]** with the destination state. 

**action\[ i , j \]** is initialized to error initially. Then as we traverse the **DFA** and fill the entries in the following way:

1. If **\[ A -> c _•_ dB \]** is in state **i** and **goto\[ i , d \] = j** then **action\[ i , d \] = "shift j"**. Here **d** must be a terminal. This is because actions on seeing a nonterminal is encoded in "**reduce A->a**" already.
2. if **\[ A -> a _•_ \]** is in state **i**, then set **action\[ i , b \] = "reduce A->a"** for all **b** in **FOLLOW(A)**. **A** may not be **S\`**. This is saying that if we are in the state of being able to reduce a handle to a nonterminal **and** a terminal that follows that nonterminal shows up, we can safely reduce the handle. If a terminal that does not follow that nonterminal shows up then the parse fails.
3. if **\[ S\` -> S \]** is in state **i**, then set **action \[ i , $ \] = " accept"**. **$** marks the end of a sentence.

 

**SLR parsers** are the easiest implementation of **LR** parsing algorithm that works. But it is not powerful enough, there are cases where an unambiguous grammar can cause a shift/reduce conflict in a **SLR parser**.

# **LR(1) parsers**

Let's examine a problem with the way **SLR** parser work. The following image illustrates clearly the shift / reduce conflict:![sc_77.png](images/sc_772.png)  **Follow(R)** contains "**\=**" because **S->L=R, L->\*R** leads to **S->\*R=R**. The grammar is not ambiguous, yet **SLR** parsers still fail to construct an appropriate action table.

The problem shows its symtomp on the table construction, specifically on filling the **_Action_** table.

The mechanism that fills an _**Action**_ table with a **shift** is straight-forward, if the next immediate step to reduce the current handle is to recognize a terminal then simply transition to a new state that contains an **SLR item** that has that terminal in the stack's content.

An **SLR item** **\[ A -> a _•_ \]** signals that we already have enough stack's content to recognize **A**, and that we initiate the reduction to **A** as soon as we observe a terminal in **FOLLOW(A).** This method is employed because it's the only logical condition to initiate the reduction given the amount of information we have. **FOLLOW(A)** gives the terminals that can follow **A** . Suppose a production that involves **A** contributes to **FOLLOW(A)** some specific terminals. These specific terminals only follow **A** using only some derivations, this means that these specific terminals do not follow **A** in other derivations. But if we just look at **FOLLOW(A)**, there is no way to tell which productions contribute which terminals. Working our way bottom-up, we have to trace back the correct derivation in order to parse successfully. However **FOLLOW(A)** does not tell us which terminals follow **A** in which derivation. Thus if the next input symbol is a terminal in **FOLLOW(A)** and we reduce immediately, we are automatically biased towards the derivations in which that next input symbol appears after **A** and biased against the derivations in which that next input symbol does not appear after A. This bias is unreasonable because tracing back derivations where the next input symbol appears after **A** might not lead us to the start symbol by the time the input stream is exhausted.

To make the problem more concrete, consider the attempt to construct the parse tree in the following image:

![sc_78.png](images/sc_781.png)

Suppose the input token we have swallowed is an **id**. We immediately conclude that we have a unique handle **L -> id** so we reduce id to **L** . At this step, two equal possibilities arise: the right cell shows a potential available handle, the left cell shows another handle in progress. From the recognized **L**, we cannot choose correctly using **SLR parsers**. With **LR parsers**, we have something new at hand. Suppose that we know what terminals can follow L in each case. In the left cell, **"="** is the only terminal that can follow **L**, as opposed to **"eof"** that follows **L** in the right cell. If we peek one symbol in the input stream without pushing it onto the stack, the lookahead symbol combined with the current stack's content is enough to decide which way to continue parsing.

Let's integrate these information into the current mechanism. Firstly in the **NFA** **states** that encode 3 pieces of information, we need to add another piece of information about the terminal such that if the terminal is seen **AND** the current handle is ready to be reduced, a reduction is triggered.

![sc_79](images/sc_791.png)

By reconstructing the **NFA** states to encode 4 pieces of information, we can safely resolve the **shift / reduce conflict** that **SLR** **parsers** fail. The **NFA states** that encode 4 pieces of information are called **LR(1) items**. The picture above shows the meaning of a typical **LR(1) item**: the four pieces of information encoded within.

We have in mind what an **LR(1) item** would look like, but one item alone does not solve anything, it's the set of items collectively that makes up a **DFA** state that we will push on the stack during the parsing. We need a default item to start constructing and a method for construction. Like in **SLR**, the initial item involves the start symbol, but now modified to having the terminal that can follow it, namely the end-of-file symbol. Therefore the initial **LR(1) item** would be **< S\` ->_•_ S , eof >**. The **closure** procedure needs to be modified to accommodate the change in the item.

![sc_80.png](images/sc_801.png)

For example, in the image above, the transition on empty string remains as before, with the exception being the fourth piece of information in the implied item. We have established that this fourth piece represents a terminal that can follow **B** in the implicator item. The terminals that follow **B** in the implicator item is naturally the terminals that are in **FIRST(C)**. If **C** can derive an empty string then the terminals that follow **B** are the terminals that follow **A**, which in this case is **b**. Combined these two cases, we have **FIRST(Cb)** is the set of terminals that can follow **B** in the implicator item.![sc_81.png](images/sc_811.png)

With every possible production of **B** and every terminal that belongs to **FIRST(Cb)**, we have an implied item. To deliver the point home, here's a concrete example:![sc_82.png](images/sc_82.png)

So back to the original example that demonstrates the shift / reduce conflict, we will see how adding an additional piece of information along with modifications on the **closure** procedure help us resolve the conflict.![sc_83.png](images/sc_831.png)

In state 2, a handle is available, with the old **SRL** approach, if the next input is "=", we reduce the handle because "=" is in **FOLLOW(R)**, and that causes a conflict. In the new approach, we only reduce if the next input is **eof**, which "=" is not and hence "=" will not trigger the reduction and we shift to state 3 like we're supposed to.

# **LALR parser**

In practice, however, **LR(1)** parsers are not often implemented due to the considerable amount of states generated. This itchy aspect comes from integrating the lookahead component into each item. Such integration leads to splitting states that contains the same core ( the first, unchanged component of an item ) but different lookaheads. Imagine the automaton of SLR being enlarged by splitting state. For a typical programming language, **LR(1)** parsers may have to compute over thousands of states in order to cover all possible lookaheads. Although the computers generally have great computational power, reality has shown that **LR(1)** parsers are slow because constructing large tables are time-consuming. Thus we need a compromise. 

We have said that **LR(1)** split states based upon the lookahed, this leads to a situation where multiple states have the same set of core items ( again, the first component of an item is the core of that item ) but are considered separate solely because of the different lookaheads. Merging these states result in the same effect with considerably smaller overall amount of states. However merging isn't always harmless, there are cases where doing so could open the door to reduce/reduce conflict. This section explores why this conflict arises, how to merge states successfully and the trade-off between **LR(1)** parsers and the type of parsers resulted from mergeing states that have the same core - **LALR** parsers.

To have a firm grasp as to why **LR(1)** parsers often generate more states, observe the two following images representing **SLR** automata and **LR(1)** automata:![sc_84](images/sc_841.png)

**SLR** automata has 6 states.

![sc_85](images/sc_852.png)

**LR(1)** automata has 9 states. If we just look at the core then state 7, 8, 9 of **LR(1)** automata is exactly like state 4, 5, 6 of **SLR** automata. This is why we say **LR(1)** splits states based on the lookahead.

The main concerns about **LR(1) parsers** is table size. For example, suppose a programming language contains **50** terminals and **50** nonterminals , and the **LR(1)** parsers use the grammar of that programming language to produce **10,000,000** states. The resulted table will have a **(50+50)\*(10,000,000) = 1 billion**  entries. Note that these characteristics are fairly common among programming languages and not as extreme as intuition suggests. Now you have two ways to hold this table, one is that you generate it before distributing the parser, in which case your application should be about **1Gb**. The second way is to generate the table dynamically in which case you need **1Gb** of **RAM** to populate it. Either way, it is impractical even if you have enough memory to pull such tasks off. A server can be hosted to store the table, but that also means the parser would have to work on a network, which is a luxurious requirement in many cases.

**LALR** parsers reduce tremendeously the amount of states generated by mergin those with the same core. As with the above example, an **LALR** parser would merge state 4 with state 7 into one state, state 5 with state 8 into one state, state 6 with state 9 into one state. The result automata is similar to that of a **SLR** parser but reductions are still done according to **LR(1)** mechanisms. A **LR(1)** automata with 10,000,000 states could be implemented equivalently with a **LALR** automata with just a few thousand states. That's why **LALR** is most often implemented in practice.

However, such merging can also lead to a reduce / reduce conflict, as demonstrated in the following example of **LR(1)** automata:![sc_86.png](images/sc_86.png)

**LALR** automat would merge the marked states together:![sc_87.png](images/sc_87.png)

We have a reduce / reduce conflict having observed terminal **c** with the lookahead being **d** or **e**. But things were fine using **LR(1)** automata, therefore we can say that **LALR** parsers cover only a subset of language of **LR(1)** parsers.

Now that we are aware of the motivation and the possible conflict for **LALR** parsers, now it's finally the time to discuss **LALR** automata construction. The first and foremost method is to generate the **LR(1)** automata then merge the states that have the same set of core items together. The second way is to merge states as we construct **LR(1)** automata.

With the first approach, we merge states together based on their core items. Then we construct the table that is the skeleton for the parser, if no conflict arises then we say the language is **LALR** and the parser is complete, otherwise we say the language is not **LALR**. 

Now onto the second approach. We try to cut away anything that is neither informative nor convenient. Considering a single **LR(1)** state, we have inside both items whose dots are at the beginning of the right-hand side and the rest. The rest, i.e, the items whose dots are anywhere BUT at the beginning of the right-hand side ( with the exception of the initial item **( S\`->_•_ S, eof )** ), is individually considered a **kernel**.

The first observation is that we can represent a state by its kernels. In the first state we can imply all other items from the initial item therefore only the initial item is retained. Any state that is not the first state can be reached from the first state via:

**GOTO( GOTO( GOTO( GOTO(....( GOTO ( s0, A ),.... ), H, I, K, L ) with A,B,C,...H,I,K,L being either terminals or nonterminals.**

Consider **GOTO( Sn, A )** ( **Sn** is any state, **A** is either a terminal or nonterminal ). If **A** is the first symbol on the right-hand side of an item in **Sn** then the dot is already behind **A** in **GOTO( Sn,** **A)**, which means the item is a kernel. Furthermore **GOTO( Sn, A )** returns the closure of such kernels, meaning we can imply all other items from the kernels.

The second observation is that parsing actions for a state can be computed by its kernels alone. An item calls or a **reduction** if the dot is at the end of the right-hand side, therefore such an item is still a kernel. A reduction on empty string is called from an item that is not a kernel. Suppose ( **A -> **_•_** ε, a )**  is in a state, this means that there exists **C** such that   **C -\*-> An** and there exists a kernel such that **( B -> d_•_ Ch, b )** in that state. Because the item ( **A -> **_•_** ε, a )** is not a kernel, we don't use it to represent a state. To resolve this decision of representing states by their kernels and nonterminals that can derive empty string, we have to precompute the set of nonterminals **A** such that **C -\*-> An** ( that includes the case where **A** = ****ε and C = A**** ) and the set of terminals in **FIRST(nhb)** that triggers a reduction for each **C**. This way we for each kernel, we have a mechanism to check for reductions on empty string.

A **shift** occurs if we have a kernel **( B -> d _•_ Ch, b )** and **C -\*-> ax** and the last production doesnt derive empty string. If the last production derives an empty string then we never shift but use **GOTO**.

**Goto** transitions are computed as followed: If **( B -> d _•_ Ch, b )** is in state **Sn**, then **( B -> dC _•_ h, b )** is in **GOTO( Sn, C ).** If a kernel such as **( E -> f _•_ Dg, k )** is in **Sn** and **D -\*->** **Cm**, then **( D -> C _•_ m, a )** is in **GOTO( Sn, C )**. 

The state that contains only of kernels are called **LALR(1)** states. We start with the kernel S\`-> **_•_** **S** of the initial state **s0**. Then, we compute goto transitions from **s0**. For each new state generated, we compute goto transitions again until we have a complete set of **LR(0)** states.![sc_88](images/sc_88.png)

Then we expand the kernels by adding the lookaheads into **LR(1)** items. There are two ways a lookahead can appear in a **Sm = GOTO( Sn, X )**. Either an item in **Sn** causes spontanous lookaheads or propagated lookaheads in **Sm**.![sc_89.png](images/sc_891.png)

Naturally, we start off by attaching **eof** into **S\`->S** as a spontanous lookahead, thus obtain **( S\`-> _•_ S,  eof )**. The algorithm to check for spontanous lookaheads and propagations is as followed, here # is just a dummy lookahead and is used just to detect propagations. This algorithm is based on the observation that propagations and spontanous lookaheads all lie in the **closure** of a kernel item.

![sc_90.png](images/sc_90.png)

We apply the algorithm to the set of **LR(0)** items above:![sc_91.png](images/sc_91.png)

To attach the lookaheads, we create a table for each kernel in each state and start making repeated passes over the kernels in all sets. Each time we visit a kernel **k**, we look up the kernel **j**  from which **k** propagates its lookaheads to and add lookaheads of **k** into the set of lookaheads of **j**.  We initialize the entries with the spontanous lookaheads obtained in the previous step.![sc_92.png](images/sc_92.png)

Finally, we obtain **LALR(1)** automata:![sc_93.png](images/sc_93.png)
