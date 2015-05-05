---
layout:    post
title:     Stellar Collapse, Or How does an event horizon form anyway?
date:      2015-05-01
summary:   Black holes present us with a seeming endless supply of paradoxes and that is certainly at root of why we find them so endlessly fascinating. While some of these are legitimate paradoxes which have yet to be satisfactorily resolved, there are others having perfectly sound explanations and yet seem immune to being widely communicated. Which brings us to the question; <i>how do black holes form in finite time anyway?</i>
permalink: stellar-collapse
---

The picture is familiar: Alice and Bob are outside a black hole in a rocket. The rocket is burning its thrusters to keep it at a fixed radius, and Alice, unable to contain her curiousity departs the rocket, plumeting toward the black hole. Bob sees Alice slow more and more as she approaches the event horizon, growing dimmer and dimmer, until the last remaining photons emitted from her are redshifted to nothing. 

Disregarding the quantum nature of photons and assuming Bob has access to a stupidly powerful photon detector, he would see it take an infinite amount of time for Alice to pass through the event horizon. But simultaneously, Alice's own clock continues to run as normal as she passes through the event horizon in short order without noticing anything special (and presumably not being vaporized by any firewall) and hitting the singularity in finite proper time. But we also know that black holes emit Hawking radiation, shrinking over time. This process occurs at an exceedingly slow rate, but nevertheless, the black hole's lifespan is ultimately finite. 

So now Bob has a new picture: Alice approaches the horizon and slows down as before, but now the horizon is always receding just ahead of her. Eventually it would seem the black hole must evaporate before Alice ever has a chance to fall in!

At face value it would <i>seem</i> that a black hole can never gain mass. More importantly, you realize, how can the event horizon even form in the first place!

At this point people tend to divide into camps; those that trade hand-wavey explanations, those that write off black holes as fantastical nonsense; and those that persist for a while seeking a good answer, don't find one from their professors or texts, walk away feeling disgruntled and ignore the problem for awhile until the question starts nagging at them too much and they repeat the process.

I was initially in the 3rd camp, but after a considerable amount of effort and calculation I seem to have arrived at a coherent explanation.

__________________________________________________

Let's first review some basics: 

In General Relativity you have the freedom to choose any coordinate system you please, and the underlying mathematics (that being differential geometry) is defined in such a way as to be completely indifferent to your choice. A coordinate system is not the same thing as an observer, but it's important to realize that your coordinate system can carry a physical meaning in the sense that it represents a choice of possible observers.

If you've learned a bit about differential geometry you might recall that generically you may need multiple coordinate patches in order to cover the entire manifold. These coordinate patches can be glued together smoothly where they overlap. 

An observer is just some given trajectory on the manifold, and in simple cases you can define this trajectory entirely in one coordinate chart. But if this trajectory hits the "edge" of the coordinate chart in finite proper time then either you've reached a true singularity in the manifold or you need to find a new coordinate system. Fortunately you can often distinguish between the two situations by looking at curvature invariants such as the Kretschmann scalar. If it blows up then the singularity theorem has struck again, but if it stays finite you could still be in good shape. This is precisely the situation at the event horizon at r = 2M in Schwarzschild coordinates.

So far, so good. So where's the problem? Well, given some physical process that you'd like to understand, there's probably some coordinate system you can choose that drastically simplifies the calculations because it makes the symmetries of that particular system more explicit. So we study things using the reference frames in which a problem is easiest to grasp, but somewhere down the line we want to compare two results that were obtained using different coordinate systems. <i>Unless you are comparing invariant quantities <b>every</b> calculation must come attached with the coordinates that produced it.</i>


__________________________________________________



Test particles obey the geodesic equation, but the geodesic equation is merely an approximation. Any sufficiently massive object must modify the spacetime according to the full gravitational field equations. This is assuredly the case in stellar collapse and it is invalid to reason that a star's collapse proceeds as if superimposed over a background Schwarzschild spacetime.


$$ ds^2 = (1-\frac{2M}{r})c^2dt^2 - (1-\frac{2M}{r})^{-1}dr^2 + r^2d\Omega^2] $$

$$ds^2 = c^2dt^2 - R(t)^2[d\chi^2 + sin^2(\chi)d\Omega^2] $$


## References

[[1] L. Rezzolla, An Introduction to Gravitational Collapse to Black Holes.](http://www.aei.mpg.de/~rezzolla/lnotes/mondragone/collapse.pdf)

[[2] S.N. Zhang, On the Solution to the “Frozen Star” Paradox, Nature of Astrophysical Black Holes, non-Existence of Gravitational Singularity in the Physical Universe and Applicability of the Birkhoff’s Theorem. Int.J.Mod.Phys.D20:1891-1899,2011 [arxiv.org:1003.1359]](http://arxiv.org/pdf/1003.1359.pdf)

[[3] A. Saini, D. Stojkovic, Radiation from a collapsing object is manifestly unitary. Phys.Rev.Lett. 114 (2015) 11, 11130 [arXiv:1503.01487 [gr-qc]]](http://arxiv.org/pdf/1503.01487.pdf)

[[4] T. Vachaspati, D. Stojkovic, L.M. Krauss, Observation of Incipient Black Holes and the Information Loss Problem. Phys.Rev.D76:024005,2007 [arXiv:gr-qc/0609024]](http://arxiv.org/PS_cache/gr-qc/pdf/0609/0609024v3.pdf)

[[5] E. Greenwood, D. Podolsky, G. Starkman, Pre-Hawking Radiation from a Collapsing Shell. [arXiv:1011.2219 [gr-qc]]](http://arxiv.org/pdf/1011.2219v2.pdf)









