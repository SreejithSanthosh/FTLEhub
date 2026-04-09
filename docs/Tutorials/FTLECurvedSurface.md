---
title: FTLE from flows on curved surfaces (MATLAB) 
parent: Tutorial - FTLE codes
layout: default
nav_order: 1
---
## Lagrangian coherent structures for flows on curved surfaces 

The MATLAB code for computing Coherent Structures based on Finite-Time-Lyapunov Exponents (FTLE) and Lagrangian deformation for flow on curved surfaces is available at this [link](https://github.com/SreejithSanthosh/flow_coherent_structure.git). 
The following tutorial provides instructions on how to use the code. To understand the mathematical background or additional information on the methods discussed here, we refer you to the accompanying manuscript 
[1]. 

![Introduction To Curved Surface FTLE](../../Images/FTLEBanner.png)

### Pre-requisites 



<!-- The following MATLAB add-ons need to be installed to run the code, 
- [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing.html) : The code uses parallelization methods provided in this toolbox to run the advection of tracer particles,
- [Lidar Toolbox](https://www.mathworks.com/help/lidar/index.html?s_tid=CRUX_lftnav) : Provides mesh processing capabilities,
- [Computer Vision Toolbox](https://www.mathworks.com/products/computer-vision.html) : Provides mesh processing capabilities. -->

The code was built on MATLAB R2025b in a Windows 10 system. We have tested these codes on a *Mac OSX 15* and *Ubuntu 20* operating systems. The most straightforwrd Installation method, is using Git, as all the code is hosted on GitHub. We assume that Git is installed and set up on the system. If not, we refer you to this [link](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git). 


### Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the GitHub repository using the code 

```
git clone https://github.com/SreejithSanthosh/flow_coherent_structure.git
```
This will generate a directory called **flow_coherent_structure**, which contains all the code. To check if all the necessary components are working, run `example_code_lagrangian.m` on MATLAB. This visualizes the velocity field on a deforming sphere from the dataset `example_data.mat` and runs the deformation analysis, presenting the results below. 

![Result of FTLE Analaysis](../../Images/result_LCS_curved.png)

The Lagrangian deformation results above are for the time interval $$(t0=0,tf=1)$$. $$\Lambda$$ is the FTLE field, $$\xi$$ is the axis of maximum deformation and $${}_{iso}\Lambda ({}_{aniso}\Lambda)$$  quantifies the isotropic (aniostropic) Lagrangian deformation experienced. For further details on these quantities and the algorithm used to compute them, we refer you to the accompanying manuscript [1].

Further development of this code is currently ongoing to improve the robustness of the method and increase its speed. To get those updates, use the command  
```
git pull
```

## Data required for Langrangian analaysis and Formating

To perform the Lagrangian analysis, we require the velocity field $$\mathbf{v}(\mathbf{x},t)$$ that quantifies the material flow on a manifold $$\mathbf{x}\in\mathcal{M}(t)$$. The Lagrangian Analysis method described here applies to both static surfaces, where the manifold is time-independent, and dynamic surfaces, where the manifold is time-dependent. 

  > > **NOTE:** Obtaining velocity data from tissue mechanics and active nematic simulations is relatively simple, but obtaining them from experimental live imaging of biological systems is more difficult. Several methods exist to extract this information, such as [ImSAnE](https://github.com/idse/imsane)  and [TubULAR](https://npmitchell.github.io/tubular/). 


The manifold information $$\mathcal{M}$$ is stored as a mesh with discrete node points $$\mathbf{x}_i = [x_i,y_i,z_i]$$ , where $$i\in\{1,N_p(t)\}$$ and $$N_p(t)$$ is total number of nodes on the manifold at time $$t$$. The connectivity of the mesh is given by a triangulation $$T$$ which is a set of all the mesh faces. Each face $$j\in\{1,N_f(t)\}$$ consists of set of three nodes $$\{i_1,i_2,i_3\}$$ that form face $$j$$. $$N_f(t)$$ denotes the total number of faces on the mesh at time $$t$$. The velocity field is stored as $$\mathbf{v}_i(t)=[v_i^1(t),v_i^2(t),v_i^3(t)]$$ where $$v_i^1(t),v_i^2(t)$$ and $$v_i^3(t)$$ are the x,y and z-component of the velocity at node $$i$$ at time $$t$$.
 
Before you run the Lagrangian analysis, the velocity field data and the manifold on which it is defined need to be stored in a `.mat` file to be read by the MATLAB code, where the variables are 

- `mesh_time` : Is a time-vector of size $(1,N_t)$, where $N_t$ is the number of time-steps.  
- `mesh_r` :  Cell array of size $(N_t,3)$. For each time index $i \in {1,2,\dots,N_t}$, the entries \texttt{mesh\_r\{i,1\}}, \texttt{mesh\_r\{i,2\}}, and \texttt{mesh\_r\{i,3\}} are column vectors of size $(N_q(i),1)$ containing the $x$-, $y$-, and $z$-coordinates of the mesh nodes at time $i$, respectively, where $N_q(i)$ denotes the number of mesh nodes at that time.
- `mesh_F` : Cell array of size $(N_t,1)$. Each entry in the \texttt{mesh\_F\{i\}} variable gives the mesh-connectivity as a matrix of size $(N_f(i),3)$ where $N_f(i)$ is the number of mesh faces at time $i$.
- `mesh_v` : Cell array of size $(N_t,3)$. For each time index $i \in {1,2,\dots,N_t}$, the entries \texttt{mesh\_v\{i,1\}}, \texttt{mesh\_v\{i,2\}}, and \texttt{mesh\_v\{i,3\}} are column vectors of size $(N_q(i),1)$ containing the $x$-, $y$-, and $z$-components of the velocity at the mesh nodes at time $i$, respectively, where $N_q(i)$ denotes the number of mesh nodes at that time.

An example dataset `example_data.mat` is provided in the GitHub repo.


  > > **NOTE:** An accurate Lagrangian Analysis requires that the mesh representation of the manifold is sampled uniformly, whereby the mesh faces are approximately of equal size; deviation from this may result in spurious results. The finer the mesh faces, the better the accuracy of the advection and deformation computed. If the original data does not meet this requirement, remeshing is recommended.       


### Performing Lagrangian Analysis

<!-- We now explain how to run the code `main.m` to compute the Lagrangian deformation information for a chosen time interval $$[t0,tf]$$. 

1. **Load the data** : Once the data is formatted appropriately as described above, it can be loaded onto the script by providing the right path 
```
load(PATH TO THE DATA FILE); Nt = size(time,2);
```
2. **Setting parameters for the simulation** :  A few parameters need to be set depending on the type of data and the system configuration on which you are running the script:  
- ``isStatic``  : should be set to ``isStatic = 1`` if the mesh surface on which the motion happens is time-independent and ``isStatic = 0`` if the mesh changes over time. The code runs faster for static meshes.
- ``cpu_num`` : The code parallelizes the Lagrangian analysis using the [parfor](https://www.mathworks.com/help/parallel-computing/parfor.html) method. Therefore, set this variable to ``cpu_num = Nc ``, where $$Nc$$ is the number of CPU cores available. Note that a copy of the dataset is sent to each core, which may cause the total data in RAM to exceed the system's capabilities. For example, if your data is x GB and you parallelize over Nc cores, the total RAM required is $$ \approx$$ > x * Nc GB.
- Plotting parameters ``Nplot`` and ``fntSz``: ``Nplot`` sets the number of frames that are saved in the video while plotting the advection results. ``fntSz`` similarly sets the font size of the text and elements on those plots.
- Advection parameters ``ct_f`` and ``ct_i`` and ``dt``: If you need to analyze the Lagrangian deformation from $$t=t0$$ to $$t=tf$$, input ``ct_f`` and ``ct_i`` so that ``time(ct_f) = tf`` and ``time(ct_0) = t0``. The parameter ``dt`` is the time-step for advection.
- Regularization parameter ``regulFac`` sets the strength of the regularization used while computing the Lagrangian deformation quantities for noisy velocity fields. For further information we refer you to SI Section S3 in the accompanying manuscript [1].  

3. **Running Code**: After setting the parameters mentioned above, run the code. The code will visualize the velocity data on the surface, forward advection $$t0\to tf$$ and backward advection $$tf \to t0$$ of tracer particles. This will be saved in the ``./SaveResults`` folder. The deformation information will be displayed as a MATLAB plot using the code written in  ``%% Calculate and Visualize the FTLE values``.  -->

To run the analysis for your dataset, load the velocity dataset in the \texttt{example.m} by changing the line `load('path\_to\_dataset')`. The user must input the parameter $$\delta$$, which sets the geodesic distance over which the deformation is computed. The user also needs to provide the parameters `ct0` and `ctf`, which specify the time range for the analysis from `mesh\_time(ct0)` to `mesh\_time(ctf)`.

## Eulerian coherent structures for flows on curved surfaces 
The MATLAB code to compute Eulerian coherent structures based on the eigenvalues of the strain rate tensor for flow on curved surfaces is available at this [link](https://github.com/SreejithSanthosh/flow_coherent_structure.git). The follwing tutorial provides an explanation on how to use the code. 
To understand the mathematical background or additional information on the methods discussed here, we refer the reader to the accompanying manuscript [1].

**Pre-requisites,data-formatting, Installation:** We recommend that users read the tutorial given above for Lagrangian analysis, as the Eulerian code has the same software pre-requisites and data-formatting requirements. To install the code, clone the GitHub repository using the code 
```
git clone  https://github.com/SreejithSanthosh/flow_coherent_structure.git
```
To ensure that the code works, run the `example_code_eulerian.m` script on MATLAB. This script runs the Eulerian analysis on the example dataset given in `example_data.mat` and presents the result below.

![Result of OECS Analaysis](../../Images/result_ECS_curved.png)
The result above visualizes the Eulerian coherent structures at $$t=0$$. The regions with positive high values of the largest eigenvalue of strain rate $$s_2(\mathbf{x},t=1)$$ corresponds to short-time repellers and regions with highly negative values of the smallest eigenvalue of the strain rate $$s_1(\mathbf{x},t=1)$$ corresponds to short-time attractors. The corresponding eigenvectors $$ e_2(\mathbf{x},t=1) $$ and $$ e_1(\mathbf{x},t=1) $$ correspond to the axis of maximum repulsion and attraction rates. 

### Performing Eulerian Analysis

We now explain how to run the code `main.m` to compute the Eulerian coherent structures for a given flow field.
 1. **Load the data** : Once the data is formatted as described above, it can be loaded onto the script by providing the right path 
```
load(PATH TO THE DATA FILE); Nt = size(time,2);
```
2. **Setting parameters for the Eulerian computation** : 
- `delta` : Regularization parameter used for estimating the strain-rate. Similar to the regularization parameter in the Lagrangian analysis. 
- `ct0`: Sets the time-point to do the Eulerian analysis. The code runs the analysis at time `mesh_time(ct0)`. 
3. **Running Code**: After setting the above parameters, run the code. The code will visualize the velocity data, the eigenvalues $$(s_1,s_2)$$ and eigenvectors $$(e_1,e_2)$$ of the strain rate at $$t = t0$$.

## References

[1] : Santhosh, S., Zhu, C., Fencil, B., & Serra, M. (2025). Coherent Structures in Active Flows on Dynamic Surfaces. bioRxiv, 2025-05.
