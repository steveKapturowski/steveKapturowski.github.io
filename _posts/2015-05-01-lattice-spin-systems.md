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

<video width="300" height="240" controls>
  	<source src="/videos/xy.m4v" type="video/mp4">
  	Your browser does not support the video tag.
</video>
</center>

The Ising model is a simplified description of ferromagnetism where spin-up and spin-down states sit on a d-dimensional lattice with an interaction defined between spins on neighboring sites. In one and two dimensions this model is analytically soluble, but because of that it makes a perfect playground to learn about Markov Chain Monte Carlo methods; a powerful tool with diverse applications in Bayesian statistics, statistical mechanics, and quantum field theory.

## Solving the Ising Model in 2D

$$ H = -\sum J_{ij}\sigma_i\sigma_j - \mu\sum h_j\sigma_j $$

$$ Z = \sum_{\{\sigma\}} e^{-\beta H}$$

## Criticality

$$ T_c = \frac{2J}{k ln(1+\sqrt2)} $$

## Detailed Balance in Markov Chains

$$ \pi_i P_{ij} = \pi_j P_{ji}$$ 

## Metropolis Algorithm

## A Curious Link to Quantum Field Theory

$$ Z = \int \mathcal{D}\phi e^{\frac{i}{h}\int dx^4 [\partial_\mu\phi\partial^\mu\phi - \frac{1}{2}m^2\phi^2 - V(\phi)]} $$

## Links and Resources

[Source code](https://github.com/steveKapturowski/QFT_Project)

[G.P. Lepage, Lattice QCD for Novices](http://arxiv.org/pdf/hep-lat/0506036v1.pdf)
