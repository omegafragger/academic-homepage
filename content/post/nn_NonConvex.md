+++
date = "2016-10-01T03:52:46+05:30"
title = "NonConvexity of neural networks"
description = "A short sweet reasoning to prove that neural networks are non-convex."
tags=["academic"]
+++

I don't remember where I heard this arguement but it was a pretty interesting one and I couldn't remember the entire reasoning and so as I began to think about it, I figured some of it out and I believe it is pretty reasonable. We have all argued that neural networks are difficult to optimize  because they are non-convex(or non-concave) for that matter. What we don't talk about so much is why is it that they are non-convex. It probably has simpler answers where you will argue something about the non-linearities involved. This post has to do with a much more interesting answer.

To understand the reasoning, we have to argue that if the function has atleast two local minimas such that their mid-point is not a local minima, the function is non-convex. In formal terms, $$\text{If }\exists x,y \in Dom(f) \hspace{5pt }s.t.\hspace{5pt} g(x) = g(y) = 0 $$ and $$\forall z\in [x,y] \text{ s.t. } g(z) \neq 0  $$ where $g(x)$ is a subgradient at $x$ the function is non-convex.

This is pretty simple to follow from the Mid-point value theorem and the definition of convexity.

Consider a neural network  $F(\cdot)$ with a few layers and name three layers $L_1, L_2$ and $ L_3$ Consider nodes $a$ and $b$ in $L_1$ and $c$ and $d$ in $L_2$.Let the parameters connecting node $i$ to $j$ be called $w\_{ij}$.

Thee arguement to the function can be thought of as $A=[\cdots w\_{ac}, w\_{ad}, \cdots , w\_{bc}, w\_{bd}\cdots]$ and the function evaluation as $f(A)$. Consider $B=[\cdots w\_{ad}, w\_{ac},\cdots, w\_{bd}, w\_{bc},  \cdots]$. I can claim that there exists a $B$ i.e. where $ w\_{ad}, w\_{ac}$ and $ w\_{bd},  w\_{bc} $  are swapped respectively, there exists an ordering of the other weights(permute the other edges) such that the function evaluation remains same. It is pretty easy to see how it can be true. 

> Look at the following picture where the reg edges come into one node and the green into another.

{{< figure src="/before.png" title="Before permutation!" >}}
For reference consider 

 * The first red edge coming out of the top left node as $w\_{ac}$ 
 * The gree edge out of the same node as $w\_{ad}$. 
 * The red edge out of the second node in the same layer as  $w\_{bc}$
 * The green edge out of the second node as   $ w\_{bd} $.
 
Connect all edges coming in to $L_2$ from $L_1$ into node $d$ to node $c$ and vice versa and then shift the origin of  all outgoing edges from node $c$ to $L_3$ to node $d$ and vice versa. What you have acheived is the network that you would have achieved by holding the two nodes by your hand and manually exchanging their positions while keeping the connecting edges intact. And Voila! now you have $A, B \in dom(F)$ where $F(A) = F(B) $.


> Now look at them after the edges are shifted.
{{< figure src="/after.png" title="After permutation!" >}}


The natural doubt you might suddenly have is `What about a linear network ? `. Well, it is a well known fact that such a function can be stated as $F(x; A, b) = Ax + b$ for some matrix $A$ and vector $B$. And as you know, linear layers do not have a maxima or a minima and hence the whole assumption we made about having $g(x) = 0$ i.e. zero subgradients, do not hold. Thus, it does not contradict the statement given earlier.

We will also need to understand how it holds the other point about there existing some point between the local optimas such that it does not have the same value as the optima. To understand this lets look at simplified representation of $F(\cdot)$. Instead, of taking the weights of the edges to be the arguements of $F(\cdot)$, let the nodes be  the arguements.  
$$F(n\_{1,1}, n\_{1, 2}, \cdots, n\_{3,1}\cdots)$$ 
where $n\_{i, j}$ is the $j^{th}$ node in $i^{th}$ layer.
$$n\_{i,j} = \sum\_{j = 1}^k w\_{j,k}^{i-1}\sigma (n\_{i-1, k})$$ where $w\_{j,k}^{i-1}$ is the weight connecting the $k^{th}$ node in ${i-1}^{th}$ layer to the $j^{th}$ node in layer $i$. 

Note that the transformation from the earlier definition to this definition is many-to-one and not one-to-one, which is easy to guess because of the reduction in the number of parameters. 

Now, it is also easy to observe that if we permute $n\_{1,1}$ amd $n\_{1,2}$, $F(\cdot)$ doesn't change it's value.To see how this permuation relates to the original neural network,  we barely need to change the weights in the original function as mentioned above(In the diagram interchange the two nodes in the second layer) i.e. the permutation of the weights mentioned above is equaivalent to this permutation. 

> What we can infer from this is that for each unique optimal value, there exists $\prod\_{i=1}^k n\_k!$ points in the parameter space that can achieve that local optima, where $n\_k$ is the number of nodes in the $k^{th}$ layer. Turns out that the number of saddle points are exponentially more! 

The above statement however holds true only when the values of the $n\_k$ nodes in that layer are also distinct but I guess the idea is clear and it is more of discrete mathematics.
 
> What this might look like is shown below. Note that this is not the loss function of a neural network but rather a function called Rastrigin function taken from wikipedia.
 
 
{{< figure src="/Rastrigin.png" title="Many many optimas!" >}}

To observe that the points between the local minimas are suboptimal, you will probably have to figure something out more rigorous as I do not have a very tight arguement, but lets look at a very interesting intuition. 

Let's assume that the function is convex. This would allow the function to be hit by jensen's inequaltiy which states that for a convex function $g(\cdot)$ $$g(\frac{1}{k}\sum\_{i = 1}^k x\_i) \le \frac{1}{k} \sum\_i g(x\_i)$$
We know that the nodes are permutable within a layer. Let there be $M$ layers and $N\_i$ nodes in each layer. We can thus get $$\prod\_{i=1}^M N\_i !$$  total permutations(Doesn't really matter if they are unique or not). If you apply jensen's inequality to this, what we will have is that the function evaluation at the average is less than or equal to the average of these optimal points, which are all equal and hence what we get is that the evaluation at the average point is also optimal(If it is not, the function is not convex). 

But notice that we are actually averaging all the permutations possible in a layer and thus what we will get is a set of arguements such that the nodes that belong to the same layer have the same value. To understand this look at the matrices below and assume one layer corresponds to a column. The two matrices are thus independant permutations of the two columns.\[
M\_1=
  \begin{bmatrix}
    1 & 2 \\\\\\
    3 & 4 
  \end{bmatrix}
\]

\[
M\_2=
  \begin{bmatrix}
    3 & 4 \\\\\\
    1 & 2 
  \end{bmatrix}
\]

\[
\frac{M\_1 + M\_2}{2}=
  \begin{bmatrix}
    2 & 3 \\\\\\
    2 & 3 
  \end{bmatrix}
\]
What this effectively means is that if the neural network was indeed convex, there would be a value which could be given to all nodes in a layer and yet the network would have represented a local optima. This is higly absurd and is very apparent if you consider a classification network where the final outputs should not have the same value in all nodes given some value in the input layer(which is also a part of the function arguement).

To generalize this to any neural network, you will have to look at the activation functions and their properties. The claim you will be trying to verify is that the average of two permutations of an optimal point need not be optimal.

I guess this was an interesting read. A very interesting thing this says is that

> A neural network cannot have a unique optima if it has atleast two distinct node values in the same layer. Amazing , isn't it ?

I guess I haven't been mathematically rigorous at all and have taken many assumptions throughout. Maybe I will list them someday!
