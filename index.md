---
title: Introduction
layout: home
nav_order: 1
---

## What are Coherent Structures?

Coherent Structures are persistent, robust structures in a flow that summarize complex fluid trajectories and material deformations [1]. Lagrangian Coherent Structures (LCSs) [1] summarize material transport over a finite time interval. Eulerian Coherent Structures [2], on the other hand, do so over short-time intervals. There exist different types of Coherent Structures and different methods for computing them from velocity or trajectory flow data (see, e.g., [3]). Here, we focus on Attracting and Repelling Coherent Structures---special surfaces in 3D flows or curves in 2D flows where nearby tracer particles maximally converge or separate. We identify Attracting and Repelling Coherent Structures based on the Finite Time Lyapunov Exponent (FTLE) and instantaneous Lyapunov Exponents [4] fields, which are the most common due to their simplicity and straightforward visualization. 

<figure>
<img src="./Images/MainIntroBanner.png" alt="Alt text">
<figcaption>(a) Deformation of a fluid patch close to a saddle point (i.e., a fixed point) of a steady (i.e., time independent) velocity (left), and to Attracting and repelling LCSs in an unsteady (i.e., time dependent) velocity (right). Attracting and Repelling Coherent Structures are special surfaces in 3D flows or curves in 2D flows where nearby tracer particles maximally converge or separate. (b-i) Examples of Coherent Structures in physical and biological flows. (b) Short-time attractors (or Attracting Eulerian Coherent Structures) aid search and rescue operations at sea [Ref]. (c) Attracting and Repelling LCSs in atmospheric flows [Ref]. (d) WRITE what it is and replace it with laboratory flow [REF]. (e) FTLE and Attracting LCSs in active nematic flows [REF]. (f) FTLE and Attracting LCSs in a beating zebrafish heart [ref]. (g) FTLE and Attracting LCSs in multicellular flows during avian gastrulation [ref]. [SREEJITH, MAKE THE CHANGES WE DISCUSSED AND USE  [t_0,t] for time intervals as below.]</figcaption>
 </figure>

## Why is coherent structure identification useful?
Consider the flow field around a saddle point (Panel (a)): a nearby patch of fluid (cyan) converges to the fixed point along its stable manifold (red curve) and separates along the unstable manifold (blue curve). Stable and unstable manifolds of saddle points, however, have important limitations: i) are meaningful only in steady velocities ($$\mathbf{v}(\mathbf{x})$$); ii) describe the evolution of material patches only in the infinite-time limit (not over finite or short times); iii) are static (do not deform/move in time). Repelling and Attracting LCSs generalize stable and unstable manifolds of fixed points, overcoming the above limitations. i) They are computable for both steady and unsteady ($$\mathbf{v}(\mathbf{x},t)$$) velocity fields; ii) they shape material patches over the desired time interval of interest $$[t_0, t]$$; iii) they are dynamic (move and deform over time); iv) they reveal regions on the flow with the highest Lagrangian deformations; v) they are objective; i.e., invariant to time-dependent rotations and translations of the coordinate system used to describe motion. 

Overall, Repelling and Attracting LCSs provide a reduced representation of a temporally and spatially varying flow, enabling robust quantification of complex and noisy velocity datasets, a systematic comparison between flows in different experiments and comparisons with theoretical models. Coherent structures also provide a simplified understanding of flow geometry, identify dynamic domain boundaries, and provide a new perspective for optimal flow control. While Coherent Structures were initially motivated to analyze atmospheric and oceanic flows, recent works have demonstrated their utility in studying biological systems and active matter, see e.g. panels b-XXX above and examples of recent papers [xx1-xx5] summarized [here](./docs/LivingandActiveMatter.md). 

## How to calculate FTLE?
Finite-Time-Lyapunov-Exponents enable identifying Repelling and Attracting LCSs as ridges (or regions with high FTLE values) of a scalar field. We describe how to compute FTLE and Attracting/Repelling LCSs for a 2D flow described by the velocity field $$\mathbf{v}(\mathbf{x},t)$$. For the formalism to compute FTLE for flows on 2D curved surfaces embedded in 3D, please refer to [ref].

>> We assume a simulated or measured velocity field is given. If one needs to reconstruct the velocity from experimental flow data, Particle Image Velocimetry methods are common (See [Ref](https://en.wikipedia.org/wiki/Particle_image_velocimetry), and [PIVLab](https://pivlab.blogspot.com/p/blog-page_19.html) is a MATLAB toolbox). 
>> 
To study the Lagrangian features of the flow, we compute the flow (or trajectory) map 
<div> 
$$ \mathbf{F}_{t_0}^t(\mathbf{x}_0)  = \mathbf{x}_0 + \int_{t_0}^t \mathbf{v}(\mathbf{F}_{t_0}^\tau(\mathbf{x}_0),\tau) d\tau,$$
</div>
that maps initial positions $$(\mathbf{x}_0)$$ at $$t_0$$ to their time $$t$$ position $$\mathbf{F}_{t_0}^t(\mathbf{x}_0)$$. The deformation gradient $$\mathbf{\nabla}_{\mathbf{x}_0}\mathbf{F}_{t_0}^t(\mathbf{x}_0))$$ quantifies derivatives of the flow map with respect to small changes in initial conditions and is represented as a 2x2 matrix for any $$(\mathbf{x}_0)$$ and time interval $$[t_0,t]$$. 

The FTLE field is given by 
<div>
$$ \Lambda_{t_0}^t(\mathbf{x}_0) = \frac{1}{|t-t_0|}\text{log}(\lambda_2(\mathbf{x}_0)),$$
 </div>
where $$\lambda_2(\mathbf{x}_0)$$ is the largest singular eigenvalue of $$\mathbf{\nabla}_{\mathbf{x}_0}\mathbf{F}_{t_0}^t(\mathbf{x}_0))$$. 
<figure>
<img src="./Images/deformationIntroFTLE.png" alt="Alt text">
<figcaption> </figcaption>
</figure>
High $$\lambda_2(\mathbf{x}_0)$$ (or $$\Lambda_{t_0}^t(\mathbf{x}_0)$$) mark initial fluid positions $$(\mathbf{x}_0)$$ where initially close tracers will highly separate by time $$t$$ (left panel). Repelling LCSs are identified by ridges (i.e. regions of high values) of the $$\Lambda_{t_0}^t(\mathbf{x}_0)$$ (or $$_{f}\Lambda $$) computed from trajectories in forward time (left panel).  By contrast, Attracting LCSs are identified by ridges (i.e. regions of high values) of the $$\Lambda_{t}^{t_0}(\mathbf{x}_t)$$ (or $$_{b}\Lambda $$) computed from trajectories in backward time (right panel).  Attracting LCSs locate the final positions $$(\mathbf{x}_t)$$ where initially distinct tracers will maximally converge. 

## References

[1] : Haller, G., 2015. Lagrangian coherent structures. Annual review of fluid mechanics, 47(1)

[2] : Serra, M., & Haller, G. (2016). Objective Eulerian coherent structures. Chaos: An Interdisciplinary Journal of Nonlinear Science, 26(5)

[3] : Hadjighasem, A., Farazmand, M., Blazevski, D., Froyland, G., & Haller, G. (2017). A critical comparison of Lagrangian methods for coherent structure detection. Chaos: An Interdisciplinary Journal of Nonlinear Science, 27(5).

[4] : Nolan, P. J., Serra, M., & Ross, S. D. (2020). Finite-time Lyapunov exponents in the instantaneous limit and material transport. Nonlinear Dynamics, 100(4), 3825-3852.

[xx1] : Serra, M., Streichan, S., Chuai, M., Weijer, C. J., & Mahadevan, L. (2020). Dynamic morphoskeletons in development. Proceedings of the National Academy of Sciences, 117(21), 11444-11449.

[xx2] : Lange, M., Granados, A., VijayKumar, S., Bragantini, J., Ancheta, S., Kim, Y. J., ... & Royer, L. A. (2024). A multimodal zebrafish developmental atlas reveals the state-transition dynamics of late-vertebrate pluripotent axial progenitors. Cell, 187(23), 6742-6759.
  
[xx3] : Serra, M., Lemma, L., Giomi, L., Dogic, Z., & Mahadevan, L. (2023). Defect-mediated dynamics of coherent structures in active nematics. Nature Physics, 19(9), 1355-1361.

[xx4] : Sinigaglia, C., Braghin, F., & Serra, M. (2024). Optimal control of short-time attractors in active nematics. Physical Review Letters, 132(21), 218302.

[xx5] : Serra, M., Serrano Nájera, G., Chuai, M., Plum, A. M., Santhosh, S., Spandan, V., ... & Mahadevan, L. (2023). A mechanochemical model recapitulates distinct vertebrate gastrulation modes. Science advances, 9(49), eadh8152. 

*This is a restricted set of references on Coherent Structures. We refer the reader to the above publications and references for an extensive reference list.  
