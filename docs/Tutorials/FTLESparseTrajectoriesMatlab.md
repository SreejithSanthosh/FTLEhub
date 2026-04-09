---
title: FTLE from 2-D/3-D Noisy and Sparse Trajectories (MATLAB)
parent: Tutorial - FTLE codes
layout: default
nav_order: 3
---

## Lagrangian coherent structures from Noisy and Sparse Trajectories for flows in 2-D/3-D Euclidean spaces

The MATLAB code to compute FTLE for flows defined on Euclidean spaces is available at this [link](https://github.com/SreejithSanthosh/flow_coherent_structure.git). The following tutorial provides instructions on how to use the code. We use the numerical method described in [1].

### Pre-requisites 

The code was built on MATLAB R2025b in a Windows 10 system. We have tested these codes on a *Mac OSX 15* and *Ubuntu 20* operating systems. The most straightforwrd Installation method, is using Git, as all the code is hosted on GitHub. We assume that Git is installed and set up on the system. If not, we refer you to this [link](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git). 

### Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the GitHub repository using the code 

```
git clone  https://github.com/SreejithSanthosh/flow_coherent_structure.git
```
This will generate a directory called **flow_coherent_structure**, which contains all the code. To check if all the necessary components navigate to the `ftle_from_sparse_trajec` folder and make it your root directory in MATLAB and run `example_sparsetrajec2d.m` on MATLAB. This runs the deformation analysis, presenting the results below for the double-gyre velocity field.

![Result of FTLE Analaysis](../../Images/result_2dEuclidean.png)

$$\Lambda$$ is the FTLE field, $$\xi$$ is the axis of maximum deformation. 

Further development of this code is currently ongoing to improve the robustness of the method and increase its speed. To get those updates, use the command  
```
git pull
```

### Performing deformation analysis for your data 

The two example code `example_sparsetrajec2d.m` is written for a specific flow field. To use this analysis for your data, you need for a 2D flow, the two coordinates of the intial position of the trajectories `x0, y0` at time $$t_0$$ and its final position `xf,yf` at time $$t_f$$. The Lagrangian analysis is done over the time-interval $$t_0\rightarrow t_f$$. The function `compute_deform_sparsetrajec.m` computes the singular values and its associated axis from `x0,y0,xf,yf` formatted as `r0` and `rf`. From the results of this function, the FTLE can be visualized using the code described in `%% Visualize the result` in `example_sparsetrajec2d.m`. The function `compute_deform_sparsetrajec.m` can also compute the deformation metrics for trajectories in 3D where `r0` and `rf` are formatted as a double array of size `Nq x 3` where `Nq` is the trajectory index an the second axis is the 3 coordinates of the trajectory.

## References

[1] : Mowlavi, S., Serra, M., Maiorino, E., & Mahadevan, L. (2022). Detecting Lagrangian coherent structures from sparse and noisy trajectory data. Journal of Fluid Mechanics, 948, A4.


---



