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

The code was built on MATLAB R2023a in a Windows 10 system. The following MATLAB addons need to be installed to run the code, 
- [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing.html) : The code uses parallelization methods provided in this toolbox to run the advection of tracer particles.
- [Lidar Toolbox](https://www.mathworks.com/help/lidar/index.html?s_tid=CRUX_lftnav) : Provides mesh processing capabilities.
- [Computer Vision Toolbox](https://www.mathworks.com/products/computer-vision.html) : Provides mesh processing capabilities.

We have tested these codes on a *Mac OSX 15* and *Ubuntu 20* operating systems. Since we couldn't test our code on a lot more versions of MATLAB and OS distributions, there could be possible unexpected errors when running this code on other system specifications. We also assume that git is installed and set up in the system, as all the code are set up on GitHub. If not, we refer you to this [link](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), explaining the same.


## Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the github repository using the code 

```
git clone  https://github.com/SreejithSanthosh/curvedSurfaceFTLE.git
```
This will generate a directory called **curvedSurfaceFTLE** which contains all the code. To check if all the necessary components are working, run `main.m` on MATLAB. This runs the deformation analysis on an example dataset of motion of cells on the pancreatic spheroids given in `./Data/staticMesh.mat` and would present the following result, 

![Result of FTLE Analaysis](../../Images/resultCodeMAIN.png)

In addition the visalization of the velocity field, forward and backward advection of tracer particles would be given in `/saveResults` directory as `vizVelocity.mp4` , `forAdvct.mp4` and `bckAdvct.mp4` respectively.

Further developement of this code to improve robustness of this method and speed it up is currently ongoing. To get those updates use the command  
```
git pull
```

## Data required for Langrangian analaysis and Formating

To perform the Lagrangian analysis, we require the velocity field $$ \mathbf{v}(\mathbf{x},t) $$ that quantifies the material flow on a manifold $$\mathbf{x} \in \mathcal{M}(t)$$. The Lagrangian Analysis method that we describe here works well for both static surfaces where the manifold on which the flow happens is time-independent and dynamic surfaces where the manifold on which the flow happens is time-dependent. 

  > > **NOTE:** Obtaining velocity data from tissue mechanics and active nematic simulations are quite straight forward, but obtaining them from experimental live imaging of biological systems are more difficult. Several methods exist to extract this information such as [ImSAnE](https://github.com/idse/imsane)  and [TubULAR](https://npmitchell.github.io/tubular/). 



The manifold information $$ \mathcal{M}$$ is stored as a mesh with discrete node points $$ \mathbf{x}_i = [x_i,y_i,z_i] \in \mathcal{R}^3$$ , where $$ i \in \{1,N_p(t)\}$$ and $$N_p(t)$$ is total number of nodes on the manifold at time $$t$$. The connectivity of the mesh is given by a triangulation $$ T $$ which is a set of all the mesh faces, which for say face $$ i \in \{1,N_f(t)\}$$ would be another set of three vertices $$ \{i_1,i_2,i_3\}$$ where $$ i_1,i_2,i_3 \in \{1,N_p(t)\}$$ are the indices of the nodes that form face $$ i $$, where $$N_f(t)$$ is the total number of faces on the mesh at time $$t$$. The velocity field is stored as $$\mathbf{v}_i(t) = [v_i^1(t),v_i^2(t),v_i^3(t)] $$ where $$v_i^1(t),v_i^2(t)$$ and $$v_i^3(t)$$ are the x,y and z-component of the velocity at node $$i$$ at time $$t$$.
 
Before you run the Lagrangian analysis, the velocity field data and the manifold on which it exists need to be stored in a `.mat` file to read by the MATLAB code, where the variables are 

- x : cell array of size ($$ N_t,1$$), where the cell array element `x{i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_p(i),1)$$ with the x-coordinate $$x_i$$ of all the mesh nodes, where $$N_p(i)$$ is the total number of points on the manifold at $$t = i$$.

- y : cell array of size ($$ N_t,1$$), where the cell array element `y{i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_p(i),1)$$ with the y-coordinate $$y_i$$ of all the mesh nodes, where $$N_p(i)$$ is the total number of points on the manifold at $$t = i$$.
- z : cell array of size ($$ N_t,1$$), where the cell array element `z{i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_p(i),1)$$ with the z-coordinate $$z_i$$ of all the mesh nodes, where $$N_p(i)$$ is the total number of points on the manifold at $$t = i$$.

- TrianT : cell array of size ($$ N_t,1$$), where the cell array element `TrianT{i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_f(i),3)$$ with mesh connectivity (For example, $$[i_1,i_2,i_3] $$ for a mesh face with nodes $$i_1,i_2$$ and $$i_3$$) are appended along the rows, where $$N_f(i)$$ is the total number of mesh faces on the manifold at $$t = i$$.

- time : double array of size ($$ 1,N_t$$), which stores information regarding the time

- v : cell array of size ($$ 3, N_t$$) , where the cell array element `v{1,i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_p(i),1)$$ with the x-coodinate of the velocity $$v^1_i$$ of all the mesh nodes, where $$N_p(i)$$ is the total number of points on the manifold at $$t = i$$. Similarily the y and z component of the velocity is stored in `v{2,i}` and `v{3,i}` respectively.

An example dataset is given in `./Data/staticMesh.mat` in the code directory, which can be visualized by runnning the code `./Data/vizExampleData.m`.


  > > **NOTE:** The accuracy of the Lagrangian Analysis code requires that the mesh representation of the manifold is sampled uniformly, whereby the mesh faces are approximately the equal sizes, deviation from this results in the spurious results. The more finer the mesh faces, the better the accuracy of the advection and deformation computed. 


## Performing Lagrangian Analysis for a Single Time Interval

We will now explain how to run the code `main.m` to compute the Lagrangian deformation information for a single chosen time interval $$[t_0,t_f]$$. 

1. **Load the data** : Once the data is formatted appropriately as mentioned in [Data Formatting](#data-required-for-langrangian-analaysis-and-formating), it can be loaded onto the script by proving the right path 
```
load(PATH TO THE DATA FILE); Nt = size(time,2);
```
2. **Setting parameters for the simulation** :  There are a few parameters that needs to be set depending on the type of data and the system configuration on wich you are running the script, as described below 
- ``isStatic``  : should be set to ``isStatic = 1`` if the mesh surface on which the motion happens is time-independent and ``isStatic = 0`` is the mesh changes over time. The code runs considerably faster for static meshes.
- ``cpu_num`` : The code parallelizes the Lagrangian analysis using the [parfor](https://www.mathworks.com/help/parallel-computing/parfor.html) method. Therefore, set this variable to ``cpu_num = Nc ``, where $$Nc$$ is the number of cpu cores available. Note that a copy of the dataset goes to each core, whereby the total data that exists on the RAM might exceed the system capabilities. For example, if your data is x GB and you paralellize over Nc cores, the total RAM required is $$ \approx$$ > x * Nc GB.
- Plotting parameters ``Nplot`` and ``fntSz``: ``Nplot`` sets the number of frames that is saved in the video while plotting the advection results. ``fntSz`` similarly sets the font size of the text and elements on those plots
- Advection parameters ``ct_f`` and ``ct_i`` and ``dt``: If you need to analyze the Lagrangian deformation from $$t = t0$$ to $$t = tf$$, input ``ct_f`` and ``ct_i`` so that ``time(ct_f) = tf`` and ``time(ct_0) = t0``. <span style = "color:red">The time-step ``dt`` for the advection </span> 
3. **Running Code** : After setting these parameters as mentioned above, run the code. The code will visualize the velocity data on the surface, forward advection $$t0\to tf $$ and backward advection $$ tf \to t0 $$ of tracer particles. This will be saved in the ``./SaveResults`` folder. The deformation information will be displayed as a MATLAB plot using the code written in  ``%% Calculate and Visualize the FTLE values``. 
<!-- To interpret these results, refer to <span style ="color:red">documentationLagrangianDeformation</span> -->

<!-- ## Performing Lagrangian Analysis for a Multiple Time Interval

Quite regularly we would want to see how the deformation evolves for increasing time intervals $$[t0,tf]$$ where for a constant $$ t0$$ you vary $$tf$$, whereby you can see the emergence of the structures in the flow. The code provided in ``mainMultTimeInterval.m`` does that for you. We will now go through how to set up this code and visualize the results using the code given in ``plotMultTimeInterval.m`` -->




