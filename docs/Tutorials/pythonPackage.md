---
title: FTLE on Curved Surfaces-Python Implementation 
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---


The python package for computing the coherent structures and Lagrangian deformation for flow on a curved surface is given in this [link](https://github.com/bfencil/ftlePackage). To understand the mathematical background or additional information on the methods discussed here, we refer you to the accompanying manuscript [S. Santhosh et al](Necessary Link).




## Installation 

To install the python package you can use the following command in your python environment:

```
pip install -e https://github.com/bfencil/ftlePackage.git#egg=ftlePackage
```

This pacakge was made in python version 3.11.8.




## Dense FTLE Computations in $$ \mathbb R^2$$ and $$\mathbb R^3$$




## Sparse FTLE Computations $$ \mathbb R^2$$ and $$\mathbb R^3$$




## Curved Surfaces in $$ \mathbb R^3 $$


The data for a surface in $$ \mathbb R^3 $$ is given as a mesh with: 


- Positions: A staggered list of dimensions (T , N_t, 3), where T corresponds to the amount of time steps and N_t is the amount of nodes at time t, each of dimensions (x,y,z).

- Velocities: A staggered list of dimension (T, N_t 3), where each index corresponds to the node position of the same index.

- Time steps: A list of the time indexes of the data of size T, indexed starting at $$ t=0 $$

- Node connections: A staggered list of dimension (T, M_t, k) consisting of integer values corresponding to node indexes for positions and velocities. M_t is the amount of faces at a time step, with k possible connections from that node to another other nodes.


The primary function used to run the FTLE anaylys is ftle_mesh below along with a description of its functionality:






