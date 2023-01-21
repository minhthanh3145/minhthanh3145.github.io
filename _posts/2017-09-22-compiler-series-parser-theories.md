---
title: "Top-down parsers"
date: "2017-09-22"
categories:
  - "parser"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

# **Introduction**

Parsing is what happens after scanning the input stream and recognize words and their syntatic categories. The parser sees this stream of words and determine wether it fits a predefined grammar structure. 

Formally, for a string _**s**_ and a grammar **_G_**, the parser tries to build a constructive proof that _**s**_ can be derived in _**G**_ - a process called _**parsing**._

# **1) Regular expression's limitations**

A **regular language** is the language that **can be described by regular expressions**, and thus can be described by Finite Automaton. Any _finite_ language is regular, but the converse is not true. For example, the RE ( a | b )\* describes an _infinite_ language. So the limitation of regular expressions is that they are applicable only for regular language. **But not every language is regular.** For example: { w | w has equal number of 1's and 0's }.

Since regular language is named so due to regular expressions, and regular expressions can be transformed to _finite automata_, the limit of regular language is the limit of _finite automate_, which lies in the word "_**finite**". _ To be able to build a DFA ( or NFA ), we h**ave to know all** components of the mathematical object (_Q_, Σ, δ, _q0_, _F_), i.e, the set of states, the alphabet, the transition function, the start state and a set of accepting states. Notice that all of these must be known **prior to the construction of DFA ( or NFA )**. It is because of this reason that the set of language { w | w has equal number of 1's and 0's } is not regular, since it is not finite in the sense that the number of states and the transition function cannot be known beforehand. 

Another non-regular language is the set of characters bounded by arbitrary number of balanced parenthesis. This type of input string which is prevalent across many programming languages does not qualify as a regular languge. This motivates us to find another notation that bears more descriptive power. 

# **2) Context-free grammars**

**A context-free grammar is a set of rules that describe how to form a sentence**.

Similarly to regular language, the set of language that can be described by context-free grammars is called context-free language. The notations are straight-forward.

![sc_30.png](images/sc_30.png)

An example is a CFG that accepts _a + a + b_:

![sc_31.png](images/sc_31.png)

Since the parse tree represents the rules applied but not the order in which they are applied, leftmost derivation and rightmost derivation yield the same result.

It is important when constructing the compiler that leftmost ( or rightmost ) derivations are unique. If not, then at some point during the formation of the parse tree, there would be multiple options to expand the leaves, which in turn imply multiple interpretations to a sentence. This is not desirable for programming languages for it would cause misbehaviors when a sentence intended by the programmer takes on another valid meaning but inappropriate in the context.![sc_33.png](images/sc_33.png)

# **3) Big picture of parsers**

Classes of context-free grammars and their parsers![sc_32.png](images/sc_32.png)

Before going into details of top-down parsers and bottom-up parsers, it is necessary to discuss the input and output of a parser in general. The parser at the second stage of the compiler sees the output of the scanner. Therefore for _a + b x c_ the parser would observe **_(name, a) + (name, b) x (name, c)_**. The parser then takes this and produces a derivation based on the predefined CFG. Since a parse tree is equivalent to a derivation, it is safe to think of the parser's output as a parse tree.

There are two ways to build a parse tree: leftmost derivation and rightmost derivation, best demonstrated by this [answer](https://cs.stackexchange.com/questions/54814/different-between-left-most-and-right-most-derivation) from [Yuval Filmus](https://cs.stackexchange.com/users/683/yuval-filmus)

![sc_46.png](images/sc_461.png)

There are two approaches for constructing a parse tree:

1. **Top-down parsers**: Start with the root and grow downwards. At each step, a nonterminal symbol from the fringe is selected and expanded into a subtree which represents a right-hand side of a production.
2. **Bottom-up parsers**: Start with the leaves and grow toward the root. At each step, the parser identifies a substring that matches the right-hand side of a production. Then this portion is replaced by the left-hand side of the producton which is a nonterminal symbol. The process keeps on going until the root is reached. 

# **4) Top-down parsing**

Top-down parsing starts with the root and grow the tree downwards. The process continues until either:

1. The leaves contain all terminal symbols, and the input stream is exhausted.
2. A mismatch occurs between the fringe of a subtree and the input stream.

If it is the first case then the parsing is complete. In second case, two situations are possible. The parse may have selected a wrong production to expand the tree at some earlier point, in which case it backtracks and considers the remanining productions. If the input string is valid then backtracking would ultimately lead to successully parsing, otherwise the input string cannot be recognized by the CFG and an error should be thrown.  The algorithm for such a parser is not tricky at all.

![sc_34.png](images/sc_34.png)

_**backtrack()**_ sets _focus_ to the parent of the current subtree and abandons the current children. If there is an untried rule for which _focus_ is on the right-hand side, then it grows the tree by pushing the the left-hand side's symbol on the stack and set _focus_ to point at the first child. If there is no untried rule, then _focus_ is moved up another level and repeat the process. If all possibilities are exhausted, an error is thrown. Another note that the input stream must also be rewinded when backtracking. The parse simply place each matched terminal back into the input stream. This could be done as the parser disconnects the discarded children fron the parse tree in a left-to-right traversal.

Top-down parser is as efficient as its ability to pick the correct choice of production during the parse tree's construction. If the parser always make the right choice, then top-down parsing is efficient. 

**4.1) Left-recursion:** 

Consider the following classical expression grammar with the input _a + b x c_

![sc_35.png](images/sc_35.png)

Now imagine a parser with consistent choice, which means it always selects productions in the way they appear in the grammar. In this case, since rule 1: _Expr -> Expr + Term_ contains Epxr on both side, there is no restriction to always choosing this rule to expand wether a _Expr_ is encountered on the left hand. Then the tree would expand indefinitely because _Expr_ is permanently a non-terminal symbol and it can never match the first input character ( _a )._ In nature, this phenomenon is known as _**left-recursion**._ Basically **a nonterminal symbol appears on both sides of a production.**

**4.2) Intuition for eliminating left-recursion:**

# **A -> Aa | b**

The above production is left-recursive because A appears on both sides. It also has another possible derivation in which the result is a terminal symbol. Almost all left-recursions are in this form. The production that is left-recursive is present because we are trying to eliminate left-recursion. The production with left-hand side being the left-recursive nonterminal symbol but the right-hand side is a terminal is present because otherwise there will be no possible termination once we encounter an **_A_**. 

Let imagine a grammar that contains only those 2 productions. What kind of sentence would this grammar accept ? One possible derivation would be: 

# **A -> Aa -> Aaa -> Aaaa -> baaa**

So the grammar is essentially saying that it accepts an **b** followed by some numbers of  **a.** Therefore we can rewrite the productions as: 

# **A -> bC and C -> aC** 

But wait, the grammar also accepts a single **b** since A -> b is valid. Therefore the rewrite is complete by ammending an empty-string acceptance into C: 

# **A -> bC and C -> aC | ε**

This is how we eliminate direct left-recursion. However there are also situations where there are indirect left-recursions such as  _α_→_β_, _β_→_γ_ , and _γ_ → _αδ_ that makes  _α_→+_αδ_. Indirect left-recursions are not obvious and obscured by long chains of productions. The following algorithm presents a method to convert indirect left-recursion to direct left-recursion then use the method above to eliminate left-recursion.

![sc_36.png](images/sc_36.png)

The visulization as followed makes things easier to comprehend:

![sc_37.png](images/sc_37.png)

Any indirect left-recursion by the end of the while loop will have been converted to direct left-recursion. Any left-recursion is then eliminated. 

**4.3) Backtrack - free parsing:**

As we have discussed, the efficienty of a top-down parser lies in minimizing calls to **_backtrack()_.**  Backtracking is needed if there are productions whose some prefixes are the same. A language is not backtrack-free the moment there exists two productions whose first terminals are the same. To ensure a backtrack-free language, we have to make sure that there are no such productions. That is why we need the two sets: **FIRST** and **FOLLOW**. **FIRST(A)** contains the first terminals that A can derive. **FOLLOW(A)** contains the terminals that follow A in all productions. The intuition why we need these two sets is explained more clearly after introducing them.

**4.3.1) FIRST and FOLLOW set:**

**FIRST**:

**FIRST(A)** contains the set of all terminal symbols that appear as the first symbol on the right-hand side of any production from A. 

**FIRST(a)** contains **a** itself.

**FOLLOW**:

**FOLLOW(A)** contains all the words that can occur to the immediate right of a string dervied from A. 

**FOLLOW(a)** is **FIRST(a)**. 

The **FIRST** set is necessary because we are testing if grammar productions are distinct given one lookahead symbol. This lookahead symbol is used to match the right-hand side's first symbol. Furthermore, this lookahead symbol is used to match the right-hand side's first **terminal** symbol, because nonterminal symbols only act as a mean to reach terminal symbols. Therefore the set of all right-hand side's first **terminal** symbol is necessary to have anything to match against.

The **FOLLOW** set's neccessity is evident through the following example:

# **A -> Bc and B -> ε** 

# **\=> A -> c is a valid production**

**FIRST(A) = {ε}** does not predict the first terminal symbol in the above derivation where a nonterminal symbol can derive an empty string. **FOLLOW** set is necessary to capture this kind of knowledge, namely, **FOLLOW(A) = {c}**. 

![sc_38.png](images/sc_38.png)

The above is the algorithm to construct **FIRST** set of all nonterminal symbols. Concisely, what happens in the while loop is that for each production of **A** , we expore every possible right-hand side's derivation, grab the first terminal symbol and throw it in the **FIRST(A)**. Finally we append an ε in **FIRST(A)** if **A** can derive an empty string. To exemplify, we will compute the FIRST set of the right-recursive version of classic expression grammar ( the one presented earlier but is removed of left-recursion ):

![sc_39.png](images/sc_39.png)

![sc_40.png](images/sc_40.png)

The following algorithm is for computing **FOLLOW** set:![sc_41.png](images/sc_41.png)

And the **FOLLOW** set result for the right-recursive version of classic expression grammar

![sc_42.png](images/sc_42.png)

**4.3.2) FIRST and FOLLOW makes up the conditions for a backtrack-free grammar:**

For a production _A_ → _β_, define its **augmented FIRST** set,  **FIRST+**, as followed:

**FIRST+**( _A_ → _β_ ) = **FIRST**(_β_),                                   if **ε** is not in **FIRST**(_β_)

**FIRST+**( _A_ → _β_ ) = **FIRST**(_β_)  ∪ **FOLLOW**(A),       otherwise.

A backtrack-free grammar has the property that, for any nonterminal A with multiple right-hand sides,  _A_→_β_1 | _β_2 | ··· | _β__n_ 

**FIRST+( _A_→_βi_ )  ∩   FIRST+(  _A_→_β__j_ ) =  ∅,              ∀ 1 ≤ _i_, _j_ ≤ _n_, _i_ != _j_**

This proprety is essentially saying that there are no two productions with the same left-hand side for which their right-hand side's first symbols are the same.

This proprety implies that once we have the left-hand side nonterminal symbol, we can uniquely identify which production to use by looking at the right-hand side's first symbol to see if it matches the lookahead symbol. 

**4.4) Left-factoring to eliminate backtracking**

Take array indices access and function calls for example, there is no clear basis for the compiler to choose based on the lookahead ( _name_ ):

**Factor -> name** **| name\[ argList \]** **| name( ArgList )** 

**ArgList -> Expr MoreArgs**

**MoreArgs -> , Expr MoreArgs |ε** 

If we keep the grammar this way, then we have to do trials and errors and thus backtracking when necessary.  However, we can tranform these productions to create disjoin FIRST+ sets.

**Factor -> name Arguments**

**Arguments -> \[ ArgList \] | ( ArgList ) | ε** 

We keep the common prefix and group together what ever follows them into a new production. We leave right-hand sides that do not share common prefix untouched.

Formally:  **_A_ → _αβ_1 | _αβ_2 | ··· | _αβ__n_ | _γ_1 | _γ_2 | ··· | _γ__j_** where  **_α_** is the common prefix and  **_γ__i_’s** are the right-hand sides that do not start with **_α_**_._ The left-factoring introduces a new nonterminal to encapture the set of symbols that follow the common prefix:

**_A_ → _α__B_ | _γ_1 | _γ_2 | ··· | _γ__j_** **_B_ → _β_1 | _β_2 | ··· | _β__n_**

Given an arbitrary context-free grammar, we can apply systematically left-recursion elimination and left-factoring to eliminate common prefixes. This may or may not lead to a backtrac-free grammar. In general, **we cannot foretell wether a backtrack-free grammar is possible for an arbitrary context-free language**.

# **Top-down Recursive-Descent Parsers**

A recursive-descent parser implements for each nonterminal symbol a function that handles all of the right-hand side of the production. This means that recursive-descent parsers can handle a wide range of grammars including ambiguous grammar, because within each function you have absolute control over the inner workings. In other words, for a nonterminal symbol, you can have as much as productions as you want and as much as left-recursion as you want, just need to implement the function to handle all of the alternatives.

The following code mimicks a simple calculator that handles simple addition and multiplication:

#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <sstream>

using namespace std;

void ignoreSpace(istream& in){
    while(isspace(in.peek()))
          in.get();
}

double getNum(istream& in){
    ignoreSpace(in);
    double val;
    in\>>val;
    ignoreSpace(in);
    return val;
}
double Expr(istream& in);
double Factor(istream& in){
    ignoreSpace(in);
    double ret;
    if(in.peek()\=='('){
        in.get();
        ignoreSpace(in);
         ret \= Expr(in);
        ignoreSpace(in);
        if(in.peek()\==')')
            in.get();
         ignoreSpace(in);
    }else{
        ret \= getNum(in);
    }

    return ret;
}
double Unary(istream& in){
    ignoreSpace(in);
    int sign\=1;
    while(in.peek()\=='+' || in.peek()\=='-')
    {
        char s \= in.get();
        if(s \=='-') sign\=-sign;
    }

    return sign\*Factor(in);
}

double Term(istream& in){
  ignoreSpace(in);
   double num1 \= Unary(in);
   double num2;
   char op;

   if(in.peek()\=='\*' || in.peek()\=='/')
       {
            ignoreSpace(in);
            op \= in.get();
            num2 \= Unary(in);
       }else
       {
            ignoreSpace(in);
            op\='\*';
            num2\=1;
        }

   if(op\=='\*')
    return num1\*num2;
   else
    return num1/num2;
}
double Expr(istream& in){
    ignoreSpace(in);
    double num1 \= Term(in);
    double num2;
    char op;

    if(in.peek()\=='+' || in.peek()\=='-')
     {
         ignoreSpace(in);
         op \= in.get();
        num2 \= Term(in);
     }else{
         ignoreSpace(in);
         op\='+';
         num2\=0;
     }

   if(op\=='+')
    return num1+num2;
   else
    return num1\-num2;

}

int main()
{
    cout<<Expr(ss)<<endl;
    return 0;
}

 

One catch is that the grammar isn't made explicit in a top-down recursive-descent parser because that knowledge is encoded within function and function calls.
