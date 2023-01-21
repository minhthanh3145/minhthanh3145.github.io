---
title: "Linear Algebra -  Things unsaid"
date: "2017-07-22"
tags:
  - "connecting-the-dots"
  - "intutions"
  - "linear-algebra"
  - "why"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

This post is designed for students who have taken an elementary linear algebra course. There is a lot of assumptions deserving of explanation in the textbooks, that's why i think it's important for them to be clarified and be linked to a certain degree of intuition.

Oh and i can't cover all the materials relevant to Linear algebra, so this post is only going to cover the materials that usually are selected to teach at universities.

- _**[Vector space](#c1)**_
- [_**Linear independence**_](#c2)
- _**[Span](#c3)**_
- _**[Basis - Change of basis](#c4)**_ 
- _**[Matrix multiplication](#c5)**_
- _**[Determinant](#c6)**_
- _**[Eigenvectors & eigenvalues](#c7)**_
- _**[Diagonization](#c8)**_
- _**[Inner product space](#c9)**_
- _**[Orthogonal basis](#c10)**_
- _**[How to change an orthogonal basis](#c11)**_

# **Vector space = space of vectors**

Suppose we have some vectors of the same kind, and we need to find some methods to manipulate those vectors ( scaling, translating, transformating, etc. ). It naturally arises that we should define how to operate on those vectors and the context where they exist. **This context is the vector space which provides us with the abstraction to the manipulation of vectors**. 

These operations should preserve some of the characteristics of real numbers that we used to deal with so that it's useful.

A vector space must be closed under addition and scalar multiplication because we don't want these operations to take us to some weird, undefined space. Who would want to do anything if we don't even know wether a consequence exists. Hence an object which resulted from these 2 operations must be in the same vector space as their component vectors. Furthermore, we want vectors addition to be reversible, therefore we do not want vectors addition to cause any extra effect that might be inreversible. Distrubtivity ensures this characteristic because it ensures that effects on the sum is the sum of effects. This reflects nicely the consistency and reversibility that we want.

# **Linear independence = less complexity**

We need to study how the vectors in a vector space are related to each other in the sense that some can be represented by some other vectors. **The existence of such dependencies signals complexity in a system.** 

An example for such complications would be if we were to add two vectors and add three vectors respectively, if we are not aware about any dependency then perhaps the result of these two operations would be the same, only that we have wasted one vectors and one addition. In real life, we have to do things effectively **AND** economically.

A set of vectors are linearly independent if none of them can be represented by others via linear combinations. Why it's the case that none of them must be so ? Because if there exists one vector that can be represented by others then all of them can be represented. For example: a = 3b + 3c + 4d <=> b = ( a - 3c - 4d ) / 3.

![mth_8.png](images/mth_81.png)

# **Span = More complexity**

Suppose we are given a set of vectors in a vector space, what can we do with them ? Without any prior direction, the best we can do is to generate all the things that we can generate. Or in other words, we try to add complexity into the system.

![mth_10.png](images/mth_10.png)

# **Basis = Span + Linearly Independence**

So far we have:

- **Linear independence** - Characteristic of the **independency** of vectors.
- **Span** - The things that we can achieve from the **depdendency** of vectors.

\=====> **Basis** is the set of vectors that satisfies both characteristics.

The balance between two opposite ideas serves as a powerful skeleton from which we can re-create the whole vector space just from some vectors.

![mth_2.png](images/mth_21.png)

So we are interested in finding a set of vectors { v1, v2, ..., vm } from which we can derive the whole vector space of n dimensions. We are trying to find the simplest set of vectors that can constitute the whole vector space.

Obviously the number of vectors must be at least the dimensions ( At least each vector for each axis of dimensions ), so m>=n. But this doesn't tell use how to verify wether a given set of vectors can formulate the whole vector space or not, since it is of trivial matter to find a set that has more vectors than the dimension but cannot construct the whole space. Thus it follows that the first condition must be that the given set of vectors can be used to re-create all vectors in its vector space. Or in other words, **the first condition is to test wether the given set spans the vector space.**

However, it is not enough that it spans, it needs to be efficient, therefore it has to span with minimum effort. In other words, it has to span with minimum amount of vectors. How do we know if the vectors in the given set are more than enough or just enough ? The answer is to check for linear independence. Any vector that can be linearly derived from other vectors is unnecessary and can be removed without affecting the span. **Therefore the second condition is to test wether the given set is linearly independent.**

Basis is the set of vector that satisfies those two conditions. **It is the set of vectors that both are linearly independent and spans the vector-space.**

# **Change of basis = Perspectives**

![mth_3.png](images/mth_32.png)

We already have the basis of the old vector space, but we are interested in working in a new vector space, it would be nice if we can construct the new basis from the old basis somehow. **All bases are created equal, until we face a particular problem for which some bases are more appropriate than the others.** 

# **Matrix multiplication = Transformation**

We are all familiar with the notion of matrix multiplications, something like : **Ax = b** with all of them being a matrix. We also know that we can only multiply between (m×n) × ( n×k ) matrices and the result of such operations is a (m×k) matrix.

The interesting part is why.

**Ax = b** can be viewed as a **transformation of x by A.** For example:![mth_5.png](images/mth_51.png)

An equivalent expression would be, stricly from carrying out the multiplication by the definition and instead break it into the sum of two vectors.

**![mth_7.png](images/mth_7.png)**

Or let x be any \[2x1\] vectors.

![mth_12.png](images/mth_12.png)

It's the linear combination of two column vectors of A. Therefore **we can view matrix multplication as the span of A's column vectors with coefficients restricted by x.** Given any specific choice of a,b ( or equivalently, a well-defined vector x ), we can obtain a specific result from linear combination.

**Geometrically speaking, matrix multiplication is viewing x under the perspective of A.** That is, each column vector of A provide a vector which get scaled by the corresponding row vector of x.

It's evident now why the factors in matrix multiplication must be equivalent in term of the number of columns ( of A ) and the number of rows ( of x ). We want to be able to epxress x exactly under perspective of A, and there are 2 cases where we would fail:

- _There are more column vectors ( of A ) than row vectors ( of x )._ This means that at least one column vectors ( of A ) would lack the scaling factor ( that is, the corresponding row vector of x ( which is missing ) ). Thus we can choose arbitrarily any scaling factor along this extra column vector. **This means x will gain extra information when perceived under A.** 
- _There are less column vectors ( of A ) than row vectors ( of x )_. This means that at least one of the row vectors ( of x ) would not be expressible due to the lack of the corresponding column vector ( of A ). **This means x will lose information when perceived under A.** 

\====> It's necessary for x to retain all of its information, no more and no less, under any perspective.

Invert of a matrix is a matrix such that AA\` =  I,  where Ix = x. Therefore invert of a matrix is just a transformation that revert the transformation of A.

# **Determinant = how it's scaled**

We have established the foundation on matrix multipliation Ax = b by stating that we can perceive such multiplications as transformations of x to b by A. To investigate the new image of x under the transformation A ( that is b ), we would want to know how different the new image is from the old image.

Further more, we want to be able to deduce these things from as less information as possible. That's why we want to focus on the transformation ( A ) rather than the result ( b ). The information about how different the new image would be from the original source is encoded in A, as expressed by its determinant.

![mth_13](images/mth_13.png)

The determinant of A here is evidently 0.0 - (-2).2 = 4.

First, this means that A scales x by a factor of 4. But 4 is the magnitude only. Positive sign means that the resultant vector lies in the first quadrant of (0,2) and ( -2,0).

Geoemetrically speaking, determinant measures the volume magnification factor for the transformation A.

As a result, invertibility is dependent on determinant in the sense that the volume magnification factor must be non-zero for a transformation ( matrix ) to be invertible. If the determinant is zero, then it would mean that the transformation (matrix) collapse its target onto a lower dimension or even the origin. Zero determinant signals loss of information after transformation, therefore it's impossible to find a transformation that can undo such effects. Thus a transformation ( matrix ) with zero determinant is not invertible.

# **Eigenvectors & Eigenvalues = self-vectors**

Eigen is Dutch for "self" ( adjective) .

Generally, the new image of x after transformation A differs from the original both in terms of magnitude and directions. However, with each transformation A there exists certain vectors  such that the result after A would only be a multiple of those vectors.

We call those vectors eigenvectors of A. We call the corresponding scaling factors eigenvalues of A.

**Ax = λx** 

or, equivalently, deprive both sides of **λx** and group the common factor x together:

**(Iλ - A)x = 0** 

View this as a matrix transformation ( Iλ - A ) on x that results 0. This means that ( Iλ - A ) collapse x to lower dimensions or to the origin. Thus:

**det( Iλ - A ) = 0** 

The above equation is called the **characteristic equation of A**. Solve the equation for values of **λ** and plug in those values back to  (Iλ - A)x = 0  to obtain eigenvectors.

It follows that a transformation ( matrix ) A is invertible only if **λ=0** is not an eigenvalue of A. If **λ=0** is an eigenvalue, then it means that transformation can collapse a valid vector x to a lower dimension or to the origin, meaning that A is not invertible.

Transformations can really be messy and hard to visualize. But if we choose a basis of eigenvectors and express the transformation in term of this eigenbasis, then the transformation A would just be the scaling along the directions of eigenbasis vectors.

# **Diagonization = eigenbasis**

Diagonization is the process of finding some transformations that map A into the matrix Pˆ¹AP ( read P multipled by A multipled by inverse P ) where P is invertible and Pˆ¹AP is diagonal. If such P exists, then we say **P diagonize A**.

Why Pˆ¹AP is particularly desired ?

If we have a different equation:  dx/dt=Ax ( where x is a n-dimensional vector ). Then diagonizing A would decouple the equation, leading to n indendent 1-dimensional equations which simplifies the process of computing greatly.

Furthermore,  Pˆ¹AP and A has the same nullspace, determinant, eigenvalue, etc. Between two representations, one is easy for human and the other is easy for computers, we have to make a conscious choice which one to use under which circumstance.

Let P be a matrix of eigenbasis vectors, Pˆ¹ is the inverse of P, A is a linear transformation, the graph shows two ways of stating the same thing. We can choose the short path or the long path. The same destination is reached regardless of our approach.![mth_14.png](images/mth_14.png)

How to diagonize an nxn matrix, technically speaking:

- Determine the diagonizability by finding the total of basis vectors for each eigenvalues, if there are n such vectors then the matrix is diagonizable, otherwise not.
- If it's diagonizable, form P = \[ p1 | p2 | ... | pn) where p1,p2,...pn are the basis vectors obtained above.
- Pˆ¹AP is the diagonal matrix whose diagonal entries are the eigenvalues that correspond to columns of P.

# **Inner product space = vector space + angel and length**

Inner product space  = Vector space + Inner product.

**Why is there a need to define an additional structure that is inner product ?** 

Suppose at the beginning of product engineering, we have some specific goals in mind which we want our final product to satisfy. Represent these goals on their own axes such that they form a coordinate system.

The goals themselves are vectors whose starting point is the origin. Projection of the current product vector onto individual axis yields the length of the vector percevied by that axis.

![mth_16](images/mth_16.png)

Suppose we're halfway through launching the product, we want to see if the product is reaching the initial goals. Such projections tell us about the direction our product is going. Or in other words, we want to collapse the product vector onto the desired axis so that we can see wether the product is getting closed.

![mth_15.png](images/mth_151.png)

Therefore we need projection in vector space. Because we can have as many axis as we want, **we need to be able to perform projection regardless of our dimensions. Inner product allows us to do this exact thing.**

Moreover, human beings can only perceive the world in 3 dimensions. That and our seemingly innate tendency to spatialize 3-d objects through the notions of length and angel call out for generalization of length and angel which enables us to reformulating real world problems as high-dimensional problems. Such reconstructions are useful because once we have reduced a real world problem into parameters, then we have reduced it to a set of vectors. And if we can see the relations between these vectors then we can deduce the relations between real world parameters. R**elations between vectors are easiest to think about in terms of length and angel. This is another thing that inner product enables us to do.** 

**What is inner product ?** 

Since we have said that inner product allows projection, it must be something that uses projection ( lol ). Technically speaking, **inner product is a function that associates a real number to each pair of vectors.** But this definition has nothing to do with our definition, so we have to dig a little deeper. 

Here's a possible proposal to how we should think of inner product: ** Inner product is scalar projection of a vector multiplied with the vector on which the other is projected.**

But why should we think this way ? First of all, there must be projection involved, checked. Then the vector on which the other is projected must retain all information, checked. One vector must be projected, checked. The overall result must be a real number, checked. Therefore this way of thinking does not pose contradictions with what we intuitively think. From now on, let's assume this definition of inner product rather than the technical one.

**Because** two vectors always make up a plane, we can just instead forget about vectors for a second and let them be straight lines, thus geoemtric facts are applicable.

![mth_17.png](images/mth_17.png)

**Therefore** **inner product is symmetric** in the sense that order of projection does not matter.

**Because** inner product is a structure within vector space, it also needs to ensure that **what we do on the sum of two vectors** must bear **the same** result **as summation of what we do on individual vectors**. Otherwise, inconsistency may arise because vectors addition might cause some inreversible effect. **Therefore inner product is additive.** 

![mth_18.png](images/mth_18.png)

**Since** inner product represents projection, and projection is product of a bunch of lengths, scaling vectors just amounts to scaling the whole inner product. **Therefore inner product is homogenous ( homogenous functions are the functions with multiplicative scaling behavior ).**

Finally, **because** pojection of a vector on itself must always be positive unless the vector itself is the zero vector. **Therefore inner product is positive**.

To summarize, inner product must satisfy the following axioms:![mth_19.png](images/mth_19.png)

**Length and angel in inner product**

Remember that we have only defined length via geoemetry, not in the vector space yet . For all the mathematicians in us know, inner product is just a structure that satisfiy the above axioms, and thus it is the only mathematical object available at hand.

Again, our intuitive definition is that inner product is scalar projection multiplied by the length of the unprojected vector.

With this definition, then what is the inner product of one vector with itself ? It will still satisfy the definition. It just happens that the two factors are the same. This enables us to see projection of a vector onto itself in general is the square of its length.

Finally, we can define **length in term of inner product** with respect to the vector space.![mth_20.png](images/mth_20.png)

Now that we have defined product and lenght, let's use them to define angle.

Obviously from geoemtry, we can compute the cosine of the angle between two vectors ( lines, to be precise ). This formula should also hold for vectors, as every two vectors form a plane and it does not make sense to have different mechanisms. ![mth_21.png](images/mth_211.png)

Additionally, we have to ensure that the formula satisifies the constraint of cosine function: cosine functions must produce values that lie within \[ -1 , 1 \].

![mth_22.png](images/mth_22.png)

**Scalar projection of u onto v is always bounded by the length of u**. This makes sense, because any kind of projection is equivalent to collapsing information, therefore the amount of projected information can never exceed the original amount of information. **We allow sign to denote the similarity of direction of two vectors involved in projection**. The sign is negative if they are in opposite directions and positive otherwise. We have proved the validity of the cosine function, and consequently the validity of our angel definition. ![mth_23.png](images/mth_23.png)

Two vectors in an inner product space is orthogonal if (u,v) = 0, naturally.

From now on, the concepts are going to be introduced under the assumption of an inner product space.

# **Orthogonal basis**

So far we have covered bases in general, but in high school we are taught to think of 3 dimensional space in term of orthogonal axes x, y and z. This perspective is natural since trees grow perpendically to the ground and so do many obstacles, therefore we humans often see the world in orthogonal lines. Thus we are interested in studying such a special type of basis.

Intuitively, we think of a vector in an orthogonal basis as the product of scaling the orthogonal basis vectors and adding them together.

Or we can inversely think that a vector represented by an orthogonal basis is made up of its projections on each basis vectors.

For example, a projection of u in the vector space onto one of the orthogonal axis:

![mth_24.png](images/mth_24.png)

It follows naturally that a vector u is expressed in an orthogonal basis as:

![mth_25.png](images/mth_25.png)

With other general bases, we have to figure out the linear combination of the basis vectors to obtain a representation of u in that basis. This process of computation is not straight-forward for computer ( also manually exhausting ). Whereas with orthogonal bases, we only need to have a function to compute the dot product of two vectors, then just call the function at each orthogonal basis vector and obtain the coefficient easily.

# **How to change to an orthogonal basis**

Everything would be nice under an orthogonal basis. That said, most of the time we do not have in hand any basis or we have only normal one, how exactly would the concept of orthogonal basis entertain us then ? This calls for a method to convert any given basis into an orthogonal basis.

Suppose we have a normal basis { u1, u2, u3 } in 3 - dimensions. We want to find the orthogonal basis, denote this basis S = {}. First we include u1 into S and denote it v1:

S = { v1 }.

We find the second orthogonal basis vector  by projecting v1 onto u2 and subtract the result from u2. Include the resultant vector v2 into S:

S = { v1, v2 }

![mth_26](images/mth_26.png)

We find the third orthogonal basis vector by summing the projections of u3 onto v1 and v2, then subtract the result from u3. Include the resultant vector v3 into S:

S = { v1, v2, v3 }

![mth_27](images/mth_27.png)

The orthogonal basis is found !

![mth_28.png](images/mth_28.png)

This process is applicable to any finite number of dimensional vector space. I think it's easy to visualize how this works up to three dimensions, we can see that it works. But if we are to us e this process in higher dimensions, we need a more abstract proof.

The intuition stems from abstracting away the conditions. Given a vector space of dimension n, we always have some subspaces of lower dimensions. Thus if we construct our subspaces as spanned by orthogonal basis vectors, then finding a new orthogonal basis is equivalent to projecting a normal basis vector onto the current subspace and subtracting the projection from the normal basis vector. 

Suppose u is the normal basis vector that we are currently taking into account. Then u prime is the projection of u. Since u prime's orthogonal basis is just u's orthogonal basis except for the kth vector. Because projecting means loss of information, the kth dimension of u is collapsed into zero when represented by (k-1) orthogonal basis vectors.

![mth_29.png](images/mth_29.png)

Therefore the kth vector of the orthogonal basis is obtained by subtracting the projection of u onto the current orthogonal basis from u, where u is the kth vector of the normal basis. To have a firmer grip on the process, we also prove that this newly obtained vector is orthogonal to all the previous orthogonal basis vectors.

![mth_30.png](images/mth_30.png)

This process is also known as the **Gram - Schmidt Process** with an extra step of normalizng the orthogonal basis to obtain an orthonormal basis.
