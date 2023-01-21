---
title: "Scanner: Categories"
date: "2017-09-19"
categories:
  - "scanner"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

We have discussed about the theories of how a scaner should work in [this post](https://dafuqisthatblog.wordpress.com/2017/09/18/compiler-scanners/). Now move on to the implementation. Usually we have tools to automate implementation -  scanner generators, with which you can specify the regular expression for each syntatic cateogry and it will handle everything. The generator constructs an NFA for each RE, create a corresponding DFA, then may or may not minimize the DFA. Then it converts the DFA to executable code.

Generally, a scanner simulates DFA transitioned by an input character. If the DFA recognizes a word, then it's done. If the DFA has exhausted all of the input characters, it must roll back to the most recently accepted DFA, or if that's not possible, report an error. 

# **Table-Driven Scanners**

# **Core idea**

Table-driven scanners utilize some tables that encode language-specific information. To put it blankly, the code uses tables that encode information about the syntatic cateogries, the transition function and the token type all of which are for the sake of simulating the DFA for each input.

For example, the followings are taken straight out of the book Engineering a compiler 2nd edition by Cooper and Torczon. This is a table-driven scanner for register names:![sc_18.png](images/sc_185.png)

![sc_19](images/sc_19.png)

![sc_20.png](images/sc_20.png)

We use the stack as the primary structure to handle DFA states. The first step is to **initialize the variables**. _lexeme_ which represents the current word is initialized to an empty string, _state_ is the global variable to represent the current state, it is set to _s__0_ . We push a _bad_ state to represent bottom of the stack.

**The first while loop** reads the variable _state_ at every iteration_._ This effectively **simulates the DFA**. If this state is not an error state then read the next character and concatenate it into the current _lexeme_. Once we encounter an accepting state, we know we're done so we clear the stack to avoid possible overflow. Retrieve the category _cat_ of that character and use the transition table on _cat_ combined with _state_ to go to another state. Replace _state_ with this new state.

**The second while loop** handles the case where the input characters have been exhausted but the top of the stack is not an accepting state, which means we have to resolve in **finding the longest prefix of the input string**. As long as the top of the stack is not an accepting state and we haven't hit the bottom, keep popping the stack, truncate the _lexeme_ by the corresponding char and rolling back the input stream.

Finally, if our attempt in finding a longest prefix yields an accepting state then **return the category of that accepting state** and we're done, **otherwise return _invalid_** to signal that the current word cannot be recognized.

Notice that we could have skipped the _charCat_ in the construction of tables and let the transition table handles every character input. Obviously, the tradeoff would be the size of the tables involved. Had we used only one table, its size would have been big. In fact, the size of a transition table that handles every input character grows as the size of the product between the length of input string and the number of states. Using two tables would cut down the size, but evidently increase the access time. This tradeoff is specific to each case. But ideally, we would like the table to be in the cache for fast access, if the table is big then portions of it might not be in the cache. We should strive for the implementations that utilize the cache as much as possible. 

**How does the transition table and token type table are created ?** It is straight forward. The transition table starts out having each column for each character of the input, each row for each state of the DFA. The generator examines each state and its transitions and fills in the row the appropriate state. Then identical columns are collapsed together. As this happens, the generator can construct the token type table by grouping input characters whose columns are collapsed together into a class. 

# **Avoiding excess rollback**

The following DFA recognizes an _ab_ or any number of occurences of _ab_ followed by a _c._ 

![sc_21.png](images/sc_211.png)

Suppose the input is **abababab**. This input string would lead to a non-accepting state on top of the stack. Using the implementation above, we would have to iterate over all the characters and  discovere that the longest prefix is **ab**. This longest prefix is recognized and the input string for the next word is **ababab**. Again, we iterate through the entire string and against recognize the longest prefix **ab**. This leaves us with **abab**. Again, recognize an **ab** and we are left with an **ab**. At this point we have made a total number of 4 iterations, each of which read the entire input string. 

This problem is dependent of the language for which we want to recognize. We can avoid this problem by simply implementing a **bit-array** _**Failed**_ that has a row for each state and a column for each position of the input string. We also need a **global variable _InputPos_** to hold the current position of the input string. 

![sc_22.png](images/sc_22.png)

So how does the introduction of the _failed_ array solves the problem ? Take the RE and input above as an example. The input is **abababab** Here's the DFA again:

![sc_21](images/sc_211.png)

 

Before get into the NextWord() function, we have to run the InitializeScanner() first.

**The first iteration** takes us to a non-accepting state after exhausting all of the input characters. As before, we roll back to the previously accepted state, which is **_s2_**. At this stage, we have already popped **_s3,s4_** off the stack several times. That's not all, at this point, we also have turned on bits at _**failed\[s3,InputPos\]**_ and _**failed\[s4,inputPos\]** _ with respect to each corresponding **_InputPos_**.

**The second iteration** takes us to the accepting state immediately because it did not accept **_s3,s4_** anymore. Immediately the longest prefix **ab** is recognized. 

**The third and the fourth iteration** doess the same thing and recognize **ab** immediately.

As we can see, we have made the iteration speed a constant time independent of the length of the input string. 

# **Direct-Coded Scanners**

Table-driven scanners spend most of their times inside the first while loop where the category is returned and combined with the current state as an indice to transition to a new state. 

Essentially, here is what really happening inside the while loop.

![sc_23.png](images/sc_23.png)

If you know anything about assembly, you will know that the two assignments above are equivalent to four instructions. An address computation, a read, an address computation, and a read. Basically, you have to retrieve the address of each elements in the table and then you have to read it into the variable.

![sc_24](images/sc_24.png)

Even though the access to any table is a constant O(1), the number of instructions involved makes the run-time slower by increasing **_instruction per cycle ( IPC )_ \-** one aspect of processor's performance. 

How to get rid of this address computations and readings ? The key understanding is that a table, in its essence, is a tool to retrieve information given a specific column and a specific row. To interpret that in a different way, a table is basically a function within which contains a lot of _if-else_. Each _if_ returns a value corresponding to its conditions. Accessing an element of a table where row is state 5 and column is character b is equivalent to going inside an _if ( state == 5 && char =="b" ) { return value; }._ **The table encodes information from lots of _if-else_ by adding a layer of address computation and reading**. Therefore, the obvious solution is to entangle the table into a bunch of _if-else._

_![sc_25.png](images/sc_25.png)_

And that's basically what a hand-coded scanner is.

# **Hand-Coded Scanners**

As tempting as table-driven scanners and direct-coded scanners may seem, an official survey has found that a large percentage of compiler groups like to hand-code their scanners. 

The routine we have sticked with so far is to make a call to each character. In java, that would be equivalent to use InputStream and invoke the method read(byte\[\]) that in turn calls read() to read byte by byte repeatedly. Here's an answer on StackOverflow by [Thomas Mathew](https://stackoverflow.com/users/225074/thomas-matthews) that nicely illustrates the overhead of reading 1 byte data at a time.![sc_26.png](images/sc_26.png)

If your file contains 256 bytes, then the above process is going to be repeated 256 times. Therefore we should seek a way to reduce the overhead. The wisest solution would be to use a buffer. Instead of reading 1 byte a time, we read a chunk of bytes and store it into an array ( the array of this usage is called a **buffer** ). Then another problem arises and we have to use modulo to solve it. 

![sc_27.png](images/sc_27.png)

In practice, a scanner can implement RollBack and NextChar efficiently given a clear boundary. We can use double buffers to hold the data. ![sc_29.png](images/sc_29.png) 

![sc_28.png](images/sc_28.png)

_fence_ is used as a boundary to prevent rolling back too far to the zone where we no longer hold data in buffer.

Thus we conclude the implementation of scanners.
