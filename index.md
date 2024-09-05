---
title: Introduction
layout: home
nav_order: 1
---

## What is FTLE?

FTLE (Finite Time Lyapunov Exponent) is a method of quantifying material flow, whereby you identify the most robust structures which act as the finite-time analogues of stable and unstable manifolds (See [Reference](https://en.wikipedia.org/wiki/Lagrangian_coherent_structure) ).



## How do we calculate FTLE?

To compute the FTLE field we start of by describing the dynamical system $\frac{d \mathbf{x}}{dt} = \mathbf{v}(\mathbf{x},t)$, where $\mathbf{v}(\mathbf{x},t)$ is the velocity field generated from the material flow. A conventional procedure to generate the velocity field fom a series of snapshots of the flow proccesses is to use PIV methods (See [Ref](https://en.wikipedia.org/wiki/Particle_image_velocimetry),[PIVLab](https://pivlab.blogspot.com/p/blog-page_19.html) is a very nice software to easily compute PIV from series of images).



## Why you should use FTLE on your flow dataset 

Structures identified using LCS methods are objective, as in they are invariant to time-dependent rigid rotations and  translations. They are also mathematically shown to be the most robust structures in the flow, thereby making it robust to noise. 


