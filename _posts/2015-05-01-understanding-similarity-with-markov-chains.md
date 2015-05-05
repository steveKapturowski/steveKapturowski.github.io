---
layout:    post
title:     Understanding Similarity With Markov Chains
date:      2015-05-01
summary:   Facebook is a graph. Twitter is a graph. The internet is a graph. Almost any other kind of data you can think of probably has some sort of graph structure. So if you're a data scientist, it's pretty important to know how to deal with graphs. It's a common question to ask how one can find things that are similar in a graph, but finding a good answer may not be as simple as you think
permalink: understanding-similarity
---

Facebook is a graph. Twitter is a graph. The internet is a graph. Almost any other kind of data you can think of probably has some sort of graph structure. So if you're a data scientist, it's pretty important to know how to deal with graphs. It's a common question to ask how one can find things that are similar in a graph, but finding a good answer may not be as simple as you think.

## Shortest Path

Starting off with a simple first guess we could choose to measure similarity by the shortest path connecting two nodes, which can easily be computed using Dijkstra's algorithm. 

For certain types of graphs and applications this might be good enough, but many naturally occurring graphs such as social networks and link graphs have degree distributions which roughly follow a power law: 

$$ P(deg(v)=k) \sim k^{-\gamma} $$ 

for some constant $\gamma$. Most vertices will have small degree, but the presence of vertices which connect to a substantial proportion of $$\{v\}$$ enables even the most disparate vertices to be connected by a short sequence of hops. This phenomena is well exemplified by the ["Six degrees of Kevin Bacon"](http://en.wikipedia.org/wiki/Six_Degrees_of_Kevin_Bacon) game.

<!-- ## Bipartite Graphs

Bipartite graphs exhibit a special structure whereby the vertices can be partitioned into two sets such that the only edges in G are those <i>between</i> the two sets.

We can compute the singular value decomposition on large matrices via the Lanczos Method or Stochastic SVD -->

## Markov Chains

The picture to have in your head when you think about Markov Chains is just a (possibly directed) graph where every node's outgoing edge weights sum to one and so can be interpreted as probabilities. 

<center>
	<img src="/images/sum_to_one.png" height="220" width="200">
</center>

Given any weighted graph we can always normalize the edge weights to satisfy this condition:

$$ P = D^{-1}W $$

where W is the weight matrix of the original graph, and D is a diagonal matrix defined by $$ D_{ii} = \sum_j W_{ij} $$

Starting at any vertex we can generate a random walk by repeatedly choosing a new vertex to move to according to the transition probabilities $$ P_{ij} $$ for the edge leaving i and arriving at j. A Markov Chain is said to be <i>ergodic</i> if (I) given any two states i and f there exists a path from i to f that can be traversed with nonzero probability, and (II) every state is <i>aperiodic</i>. Note, the Markov chain above is not ergodic because it fails condition (I): you get trapped in either states j or k and can't make your way back to the other states. I won't rigorously define what periodicity is meant here because [wikipedia](http://en.wikipedia.org/wiki/Markov_chain) does a good enough job and this article is long enough as it is, but below are a couple of simple examples in which every state is periodic, with the periodicity of each state labeled:

<center>
	<img src="/images/periodicity.png" height="220" width="200">
</center>

Consider a vector $$\pi$$ which represents a probability distribution over each of the vertices. If we start with all of the probability mass centered on one vertex  and iterate the distribution according to 

$$ \pi_k = \pi_{k-1}P \tag{1}$$ 

then in fact this is the random walk we just described. More generally we can start with any initial distribution and we'll end up with a kind of weighted average over random walks with different starting points. 

A distribution $$\pi_0$$ is called an equilibrium distribution if it satisfies:

$$ \pi_0^T = \pi_0^T P $$

## What do Random Walks Give us that Shortest Paths Don't?

Consider a vertex i adjacent to a high degree vertex j, and a vertex k which has i as its only neighbor. Intuitively, it should make sense that $$ Sim(j, i) > Sim(i, k) $$ because their association is more uniquely distinguishing, but under path similarity they are the same.

Consider a random walk starting at i. If it hops to vertex j then at the next step the probability distribution would get smeared out over <i>every</i> vertex that j is connected to, so ultimately j will tend to end up with a low probability. Conversely, if the random walk hops over to k, then at the next step the <i>only</i> choice is for it to come back to i. In other words, the random walk can tell that there's less randomness associated with these connections!

It's possible to define a variety of different notions of similarity based on Markov chains, but they basically all capture this key feature. There's no clear way to say which is theoretically <i>best</i>, and if you have time it might be advisable to try several of them out and compare their performance on your particular data. 

Below I discuss some possible choices, how to compute them, and some of their important properties.

## PageRank

We can compute the personalized PageRank for some initial distribution via a power iteration as follows:

$$ \pi_t^T = \beta \pi_0^T + (1-\beta) \pi_{t-1}^T P $$

{% highlight python %}
from scipy import sparse


# pi0 is any initial distribution
def personalizedPageRank(W, pi0, beta=.1, n=30):
	D_inv = sparse.dia_matrix(W.shape)
	D_inv.setdiag([(not num or num**-1+1)-1 for num in W.sum(axis=0).tolist()[0]])
	P = D_inv * W

	pi_n = pi0.copy()
	for i in range(n):
		pi_n = beta*pi0 + (1-beta)*pi_n*P

	return pi_n
{% endhighlight %}

If you don't need or want a symmetric notion of similarity then this might be enough: given $$\pi_0$$ centered on vertex $$v$$ you compute the PageRank and you take the vertices assigned the highest probability as the most similar.

Alternatively, given any <i>two</i> vertices, we compute their PageRank's separately, so each vertex gets transformed into a probability distribution. Now we simply use some standard method for comparing probability distributions such as [KL Divergence](http://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) (which is also non-symmetric) or [Bhattacharyya distance](http://en.wikipedia.org/wiki/Bhattacharyya_distance) (which <i>is</i> symmetric).

## Commute Times and Hitting Times

[Hitting times](http://en.wikipedia.org/wiki/Hitting_time) are a very interesting property that capture a considerable amount of the connectivity structure of a graph. But they might not map to the notion of similariy we want because it's not symmetric, i.e.: the hitting time from a to b is not the same as the hitting time from b to a. Fortunately there a very easy way to fix this: define the <i>commute time</i> $$ C_{ij} \equiv h_{ij} + h_{ji} $$ as the expected time to travel from i to j and then return back to i. 

Great! Now that we have a nice, symmetric notion of Markov similarity, how do we compute it?

$$ h_{ij} = \frac{L_{jj}^\dagger}{\pi_j} - \frac{L_{ij}^\dagger}{\sqrt{pi_i \pi_j}} $$

where $$ L^\dagger = (L^T L)^{-1}L^T $$ is the [<i>Moore-Penrose pseudoinverse</i>](http://en.wikipedia.org/wiki/Moore%E2%80%93Penrose_pseudoinverse), $$L = I - D^{-1/2} W D^{-1/2}$$ is the [<i>normalized graph Laplacian</i>](http://en.wikipedia.org/wiki/Laplacian_matrix), and $$\pi$$ is the stationary distribution of $$P$$. Now personally, I think this is a very cool formula. The only problem is that it's totally useless for most practical calculations. Assuming the graph we're dealing with is large enough to require exploiting its sparsity we're out of luck because this formula relies entirely on computing the inverse of a very large, sparse matrix. And generically the inverse of a sparse matrix will be dense.

This would seem to put us in a difficult position, but fear not brave programmer, for all hope is not lost!

## Power Iteration

The hitting time can be computed by the following expectation value:

$$ h_{ij} = \sum_{t=0}^\infty t(P^{t})_{ij}[1 - \sum_{k=0}^{t-1}(P^k)_{ij}] $$

Truncating this series at some $$T-1$$ and adding in a remainder term we get the <i>truncated hitting time</i>:

$$ h_{ij}(T) = \bigg(\sum_{t=0}^{T-1} t(P^{t})_{ij}[1 - \sum_{k=0}^{t-1}(P^k)_{ij}]\bigg) + T[1-\sum_{k=0}^{T-1}(P^k)_{ij}] $$

Finally, we can rewrite the above equation as follows to avoid having to compute powers of the transition matrix:

$$ \rho_k^T(\hat{e}_i) = \rho_{k-1}^T(\hat{e}_i) P \mid \rho_0 = \hat{e}_i $$

$$ h_{ij}(T) = \bigg(\sum_{t=0}^{T-1}t(\rho_t^T)_j[1 - \sum_{k=0}^{t-1}(\rho_k^T)_j]\bigg) + T[1-\sum_{k=0}^{T-1}(\rho_k^T)_j] $$

This approximation will always underestimate the true hitting time, as all the unused probabily mass after $$T-1$$ gets lumped into the $$T$$ term. Thus we'll have to play with cutoff to make sure the range $$[0, T]$$ has sufficient resolution to capture most of the interesting behavior.


{% highlight python %}
from scipy import sparse


def truncatedCommuteTimes(W, epsilon=.01, T=30):
	E_n = truncatedHittingTimes(W, epsilon, T)
	return E_n + E_n.T


def truncatedHittingTimes(W, epsilon=.01, T=30):
	D_inv = sparse.dia_matrix(W.shape)
	D_inv.setdiag([(not num or num**-1+1)-1 for num in W.sum(axis=0).tolist()[0]])
	P = D_inv * W
	I = sparse.identity(P.shape[0])

	P_n = I
	rho_n = sparse.csr_matrix(P.shape)
	E_n = sparse.csr_matrix(P.shape)

	n = 1
	while n < T:
		print n, len(P_n.data), len(rho_n.data), len(E_n.data)
		rho_n = rho_n + P_n - P_n.multiply(rho_n)
		P_n = P_n*P
		E_n = E_n + n*(P_n - P_n.multiply(rho_n))
		n += 1

	#Calculate remainder
	rho_n = rho_n + P_n - P_n.multiply(rho_n)
	T_matrix = (E_n / E_n.max()).ceil()*T
	E_n = E_n + T_matrix - T_matrix.multiply(rho_n)

	return E_n
{% endhighlight %}

That's all! Hopefully you've found it all an interesting read and are able to break out some of these ideas the next time you're faced with a nasty graph of data you need to make sense of. I've tried to be fairly thorough, but it's very possible I let errors slip by or left out interesting alternatives; so as always, feedback and corrections are greatly appreciated!

## References

[D. Rao, D. Yarowsky, C. Callison-Burch, Affinity Measures based on the Graph Laplacian.](http://www.cis.upenn.edu/~ccb/publications/graph-laplacian-affinity-measures.pdf)



