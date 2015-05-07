---
layout:    post
title:     MCMC, Renormalization, and the Ising Model
date:      2015-05-01
summary:   The Ising model is a simplified description of ferromagnetism where spin-up and spin-down states sit on a d-dimensional lattice with an interaction defined between spins on neighboring sites. In one and two dimensions this model is analytically soluble, but because of that it makes a perfect playground to learn about Markov Chain Monte Carlo methods; a powerful tool with diverse applications in Bayesian statistics, statistical mechanics, and quantum field theory.
permalink: ising-model
---

<center>
<video width="300" height="240" controls>
	<source src="/videos/ising.m4v" type="video/mp4">
	Your browser does not support the video tag.
</video>
</center>

The Ising model is a simplified description of ferromagnetism where spin-up and spin-down states sit on a d-dimensional lattice with an interaction defined between spins on neighboring sites. In one and two dimensions this model is analytically soluble, but because of that it makes a perfect playground to learn about Markov Chain Monte Carlo methods; a powerful tool with diverse applications in Bayesian statistics, statistical mechanics, and quantum field theory.

## Solving the Ising Model in 2D

$$ H = -\sum J_{ij}\sigma_i\sigma_j - \mu\sum h_j\sigma_j $$

$$ Z = \sum_{\{\sigma\}} e^{-\beta H}$$

This is just a simple algebraic equation, so in principle it tells us everything about the statistics mechanics of the system. The problem is that the number of terms in the sum is exponential in the number of states $$\sigma$$. There are two ways to deal with the situation: 1) figure out some fancy tricks to ananlytically coerce this unwieldy beast into submission, or 2) use numerical techniques which exploit the fact that only a relatively small number of states contribute appreciably to the partition function. Here, we'll do both.

Let's start with (1):

Let N be the length of the lattice in each direction and assume periodic boundary conditions such that $$\sigma_{Nj}$$ neighbors $$\sigma_{1j}$$ and $$\sigma_{iN}$$ neighbors $$\sigma_{i1}$$. For simplicity let's also turn off the external magnetic field ($$h=0$$). Bringing the sum in the exponential down into a product of exponentials and using the relation $$e^{lK} = \cosh(x) + l\sinh(K)$$ for $$l = \pm 1$$ we have:

$$ Z= \sum_{\{\sigma\}} \prod_{\langle i,j\rangle} e^{\beta J \sigma_i\sigma_j} = \cosh(\beta J)^{2N^2} \sum \prod (1+\sigma_i \sigma_j \tanh(\beta J))$$ 





## Criticality

As it turns out, the Ising Model in two dimensions has a phase transition.

$$ T_c = \frac{2J}{k \ln(1+\sqrt2)} $$

## The Metropolis Algorithm

Every physics treatment of this algorithm that I've read feels as if it's described backwards, so I'm going to try doing things a bit differently.

The overall idea is to build a markov chain whose equilibrium distribution of states matches the partition function. Let $$P$$ denote the $$2^Sx2^S$$ transition matrix where $$S$$ is the number of lattice sites and whose entries $$P_{AB}$$ represent the probability of transitioning from state $$A$$ to state $$B$$.

A <i>sufficient</i>, but not <i>necessary</i>, condition for the Markov chain to be in equilibrium is for it to satisfy the [<i>detailed balance</i>](http://en.wikipedia.org/wiki/Detailed_balance) condition: 

$$ \pi_A P_{AB} = \pi_B P_{BA}$$

where $$\pi$$ is the probability distribution over states. Since we want $$\pi$$ to give the partition function, we must have $$\pi_A = \frac{1}{Z}e^{-\beta E_A}$$. Thus: 

$$ \frac{\pi_A}{\pi_B} = \frac{P_{BA}}{P_{AB}} = e^{\beta[E_B-E_A]} $$

We have some freedom in choosing transition probabilities, they just need to satisfy this equation. So let's define:

$$P_{AB} = \frac{1}{S} \mathcal{A}_{AB}$$

$$
\mathcal{A}_{AB} = \left\{
     \begin{array}{lr}
       e^{\beta[E_B-E_A]} & : B < A\\
       1 & : otherwise
     \end{array}
   \right.
$$

for $$A \neq B$$ being nearest neighbors and $$0$$ otherwise. Finally, the requirement that $$P$$ be a stochastic matrix fixes $$P_{AA} = 1 - \sum_{B \neq A}P_{AB} $$. 

Now that we know all this, we can finally define the [<i>Metropolis Algorithm</i>](http://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm):

0. initialize lattice with some spin configuration $$\{\sigma\}$$
1. choose $$\sigma_i$$ uniformly at random (drawing from $$p(\sigma_i) = \frac{1}{S}$$)
2. set $$\sigma_i := - \sigma_i$$ defining the transition $$A \rightarrow B$$
3. accept new state with probability $$\mathcal{A}_{AB}$$
4. if new state is rejected reset $$\sigma_i$$ to its previous state
5. proceed to step (1)

Since the algorithm initializes the spins in an arbitrary configuration there is a certain thermalization time needed before the system actually reaches equilibrium. Once equilibrium <i>is</i> reached we can treat subsequent states as draws from the partition function. We will generally want each draw to be independent from the previous draw. To achieve this we can simply experiment to find a fixed number of iterations between draws for which the correlations are sufficiently close to zero.

## A Curious Link to Quantum Field Theory

The starting point for most quantum field theory is the path integral, such as this one describing a real scalar field:

$$ Z = \int \mathcal{D}\phi e^{\frac{i}{h}\int dx^4 [\frac{1}{2}\partial_\mu\phi\partial^\mu\phi - \frac{1}{2}m^2\phi^2 - V(\phi)]} $$

Fancifully put, a real scalar field is just the physics in the continuum limit of an infinitely large mattress. 

The path integral is essentially a sum over all paths in spacetime. The notation with $$\partial_\mu\phi\partial^\mu\phi$$ with [one index raised and one lowered](http://en.wikipedia.org/wiki/Einstein_notation) encodes the metric of [Minkowski space](http://en.wikipedia.org/wiki/Minkowski_space). Described using this geometry the physics of special relativity essentially all boils down to the fact that you can convert space and time into each other by rotating through an imaginary angle. 

Since the functions being described here are analytic we can [<i>rotate into imaginary time</i>](http://en.wikipedia.org/wiki/Wick_rotation) to do our calculations in euclidean space and then use [analytic continuation](http://en.wikipedia.org/wiki/Analytic_continuation) to put our results back into ordinary spacetime.

But when you rotate into Euclidean space the exponent gets shuffled around:

$$ Z = \int \mathcal{D}\phi e^{-\frac{1}{\hbar}\int dx^4 [\frac{1}{2}\delta^{\mu\nu}\partial_\mu\phi\partial_\nu\phi + \frac{1}{2}m^2\phi^2 + V(\phi)]}
= \int \mathcal{D}\phi e^{-\frac{1}{\hbar} H[\phi]} $$

If you identify $$\beta \leftrightarrow 1/\hbar$$ this is exactly analogous to the partition function in statistical mechanics. This is an example of a <i>duality</i>: every problem in quantum field theory has a corresponding problem in statistical mechanics, and vice versa. This greatly expands the variety of techniques at our disposal and has proven to be a powerful tool in physics. In particular the MCMC simulation we used here can be applied to quantum field theories and has been critical to understanding the [nonperturbative aspects of quantum chromodynamics](http://en.wikipedia.org/wiki/Lattice_QCD). But that's for a future post...

## Links and Resources

[[1] Source code](https://github.com/steveKapturowski/QFT_Project)

[[2] The Renormalization Group for Ising Spins](http://math.arizona.edu/~tgk/541/chap3.pdf)

[[3] Lattice QCD for Novices](http://arxiv.org/pdf/hep-lat/0506036v1.pdf)
