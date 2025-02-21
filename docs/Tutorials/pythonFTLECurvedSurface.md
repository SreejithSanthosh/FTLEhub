---
title: FTLE on Curved Surfaces: Python Implementation 
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---


The code for computing the coherent structures and Lagrangian deformation for flow on a curved surface is given in this [link](https://github.com/SreejithSanthosh/curvedSurfaceFTLE.git). To understand the mathematical background or additional information on the methods discussed here, we refer you to the accompanying manuscript [S. Santhosh et al](Necessary Link).


## Pre-requisites 


The python code was written in Python version 3.11.8. The following libraries were used: numpy, scipy, pyvista, numba, and itertools.



## Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the github repository using the code 

```
git clone  https://github.com/SreejithSanthosh/curvedSurfaceFTLE.git
```

Further developement of this code to improve robustness of this method and speed it up is currently ongoing. To get those updates use the command  
```
git pull
```




## Data required for Langrangian analaysis and Formating


The manifold information $$ \mathcal M $$ is stored as a mesh with discrete node points $$ \mathbf{x}_i = (x_i,y_i,z_i) \in \mathcal{R}^3$$ , where $$ i \in \{0,,1, cdots, N_t\}$$ and $$N_t)$$ is total number of nodes on the manifold at time step $$t$$. The connectivity of the mesh is given by a triangulation $$ T $$ which is a set of all the mesh faces. Where a face is defined by being a set of three node positions. Which for say face $$ f \in \{0,1, \cdots , F_t\}$$ would be another set of three vertices $$ \{i_1,i_2,i_3\}$$ for $$ i_1,i_2,i_3 \in \{0,1,\cdots,N_p(t)\}$$ are the indices of the nodes that form face $$ f $$, where $$F_t$$ is the total number of faces on the mesh at time $$t$$. The velocity field is stored as $$v_p(t) = (v_p^1(t),v_p^2(t),v_p^3(t)) $$ where $$v_p^1(t),v_p^2(t)$$ and $$v_p^3(t)$$ are the x,y and z-component of the velocity at node $$p$$ at time $$t$$.
 
The data for the mesh must be stored like so:

- node positions : an array of dimensions (node_number, 3) , which in the first, second, and third columns are the x,y,z coordinates respectively, and each row corresponds to a node index.



- node connections : cell array of size ($$ N_t,1$$), where the cell array element `TrianT{i}` $$i\in \{1,N_t\} $$ is a double array of size $$(N_f(i),3)$$ with mesh connectivity (For example, $$[i_1,i_2,i_3] $$ for a mesh face with nodes $$i_1,i_2$$ and $$i_3$$) are appended along the rows, where $$N_f(i)$$ is the total number of mesh faces on the manifold at $$t = i$$.



- time steps: an array of dimensions (total time steps), where each time step is a non-negative integer.

- node velocities : an array of dimensions (node_number, 3) , which in the first, second, and third columns are x,y,z velocities respectively, and each row corresponds to a node index.




## Performing Lagrangian Analysis for a Single Time Interval


Onece the data has been formatted correctly and loaded. The entiretity of the analysis can be done through the run_simulation function. Which has a few paramters. 

**Setting Parameters for the simulation**: The first paramters are the ``initial_time`` and ``final_time`` parameters, which control over what interval of your time stpes you want the simulation to cover, in general you want final_time to be greater than initial_time. 
The next parameter is ``direction`` which can be set to either "forward" or "backward", for the forward and backward advection respectively. If you backward is selected then your final_time and initial_time are swapped so final_time should be less than initial_time.
The next parameter is the ``scheme_type`` which allows you to select between three different advection schemes. The first scheme is Forward-Backward Euler(selected as "euler"), as the name implies this is a combination of the standard Forward Euler and Backward Euler schemes. What Forward-Backward Euler does specifically is that it alternates the use of the forward and backward part per time step. This creates a scheme that almost completly preserves energy due to the energy increase in Forward Euler cancelling out with the energy decrease in Backward Euler. The second scheme is what will be called Fast Euler(selected as "fasteuler"). This scheme uses Forward Euler but only projects points to the mesh and gets interpolated velocity values every other time step. Resulting in additional error terms that help to cancel out the error terms of Forward Euler. The last scheme is jsut Runge-Kutta 4(selected as "rk4"). These schemes all perform comparable to each other in terms of accuracy, but have some important differences. Firstly the RK4 scheme perfomrs the best for large time steps in terms of accuracy and speed. However for small time steps(<0.2) we have that Fast Euler is just as accurate as the RK4 and can run in well under half the time RK4 does. In general Forward-Backward Euler is the least accurate of the three but only by a small margin and recovers the same primary Lagragian Coherent Structures as the other two. The advantage of Forward-Backward Euler is that it is more accurate at very small time steps(on the order of 0.001 and lower) than Fast Euler and RK4.

The other parameters are the mesh data and velocity data disscussed in the Data required for Langrangian analaysis and Formating section.




