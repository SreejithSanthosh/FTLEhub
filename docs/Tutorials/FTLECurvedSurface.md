---
title: FTLE on Curved Surfaces 
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---

The code for computing FTLE for flow on curved surface are given at [source](https://github.com/SreejithSanthosh/curvedSurfaceFTLE.git). We will provide a detailed tutorial on how to use this code for flow on curved surfaces. To understand the mathematical background and methods used to compute FTLE on curved surface, we refer you to the accompanying manuscript [S. Santhosh et al](Necessary Link).

![Introduction To Curved Surface FTLE](../../Images/FTLEBanner.png)

Please [reach out to us](../Contact) in case of any issues with the code, and we will try to work with you to get it to work for your dataset. 

## Pre-requisites 

The code was built on MATLAB R2023a in a Windows 10 system. We have tested these codes on a *Mac OSX 15* and *Ubuntu 20* operating systems. Since we couldn't test our code on a lot more versions of MATLAB and OS distributions, there could be possible unexpected errors when running this code on other system specifications. We also assume that git is installed and set up in the system, as all the code are set up on GitHub. If not, we refer you to this [link](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), explaining the same.


## Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the github repository using the code 

```
git clone  https://github.com/SreejithSanthosh/curvedSurfaceFTLE.git
```
This will generate a directory called **curvedSurfaceFTLE** which contains all the code. To check if all the necessary components are working, run `mainSingleTimeInterval.m` on MATLAB. This runs the deformation analysis on an example dataset of motion of cells on the pancreatic spheroids given in `./Data/staticMesh.mat` and would present the following result, 

![Result of FTLE Analaysis](../../Images/exampleResult.png)

In addition the visalization of the velocity field, forward and backward advection of tracer particles would be given in `/saveResults` directory as `vizVelocity.mp4` , `forAdvct.mp4` and `bckAdvct.mp4` respectively.

Further developement of this code to improve robustness of this method and speed it up is currently ongoing. To get those updates use the command  
```
git pull
```

## Data required for Langrangian analaysis 

To perform the Lagrangian analysis, we require the velocity field $$ \mathbf{v}(\mathbf{x},t) $$ that quantifies the material flow on a manifold $$\mathbf{x} \in \mathcal{M}$$ and the manifold. The manifold information $$ \mathcal{M}$$ is stored as a mesh with discrete node points $$ \mathbf{x}_i = [x_i,y_i,z_i] \in \mathcal{R}^3$$ , where $$ i \in \{1,N_p\}$$ and $$N_p$$ is total number of nodes. The connectivity of the mesh is given by a triangulation $$ T $$ which is a set of all the mesh faces, which for say face $$ i \in \{1,N_f\}$$ would be another set of three vertices $$ \{i_1,i_2,i_3\}$$ where $$ i_1,i_2,i_3 \in \{1,N_p\}$$ are the indices of the nodes that form face $$ i $$, where $$N_f$$ is the total number of faces on the mesh. 
 
Before you run the Lagrangian analysis, the velocity field data and the manifold on which it exists need to be stored in a `.mat` file to read by the MATLAB code, where the variables are 
- x : cell array with size ($$ N_t,1$$)
- y : cell array with size ($$ N_t,1$$)
- z 
- TrianT
- time 
- v 

  > > **NOTE:** Obtaining velocity data from tissue mechanics and active nematic simulations are quite straight forward, but obtaining them from experimental live imaging of biological systems are more difficult. Several methods exist to extract this information such as [ImSAnE](https://github.com/idse/imsane)  and [TubULAR](https://npmitchell.github.io/tubular/). The Lagrangian Analysis method that we describe here works well for both static and dynamic tissues.
