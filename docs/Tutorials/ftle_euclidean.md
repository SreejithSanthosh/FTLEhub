---
title: FTLE from Flows on 2D/3D Euclidean Grids (MATLAB)
parent: Tutorial - FTLE codes
layout: default
nav_order: 2
---

# Lagrangian Coherent Structures for Flows in 2D/3D Euclidean Spaces

This tutorial explains how to use the MATLAB code for computing finite-time Lyapunov exponent (FTLE) fields for flows defined on 2D and 3D Euclidean grids.

The MATLAB code is available in the GitHub repository: [flow_coherent_structure](https://github.com/SreejithSanthosh/flow_coherent_structure.git). The code computes Lagrangian coherent structures by approximating the deformation gradient using finite differences. The FTLE field is then obtained from the singular values of the deformation gradient. The numerical method follows the approach described in [1].

## Prerequisites

The code was developed using **MATLAB R2025b** on a Windows 10 system. It has also been tested on:

* macOS 15
* Ubuntu 20

The recommended installation method is through Git, since the code is hosted on GitHub. This tutorial assumes that Git is already installed and configured on your system. If Git is not installed, follow the installation instructions here:

[Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

---

## Installation

Navigate to the directory where you want to install the code, then clone the GitHub repository:
```bash
git clone https://github.com/SreejithSanthosh/flow_coherent_structure.git
```

This command creates a directory called:
```text
flow_coherent_structure
```

which contains the MATLAB scripts, functions, and example files. To test the Euclidean-grid FTLE code, navigate to the folder:
```text
ftle_from_euclidean_grid
```
and set it as the root directory in MATLAB.
## Example: 2D Double-Gyre Flow

To test the 2D FTLE computation, run the following MATLAB script:
```matlab
example_euclideangrid2d.m
```
This script computes the deformation field and FTLE field for the double-gyre velocity field. The output is shown below.

![Result of 2D Euclidean FTLE Analysis](../../Images/result_2dEuclidean.png)

Here:
* $\Lambda$ is the FTLE field.
* $\xi$ is the axis of maximum deformation.

## Example: 3D ABC Flow
To test the 3D FTLE computation, run:

```matlab
example_euclideangrid3d.m
```
This script computes the deformation field and FTLE field for the ABC flow. The output is shown below.

![Result of 3D Euclidean FTLE Analysis](../../Images/result_3dEuclidean.png)

As in the 2D case, the code computes the FTLE field from the deformation of an initially defined grid over a prescribed time interval.

## Updating the Code
The code is under active development. To update your local copy with the latest changes, navigate to the repository directory and run:

```bash
git pull
```
## Performing Deformation Analysis on Your Own Data
The example scripts are written for specific test flows:

* `example_euclideangrid2d.m` uses the 2D double-gyre flow.
* `example_euclideangrid3d.m` uses the 3D ABC flow.

To apply the analysis to your own velocity field, you must first advect an initial grid from the initial time $t_0$ to the final time $t_f$.

---

## 2D and 3D Flow Data Format and Analysis

The two example scripts for 2D and 3D flows, `example_euclidean2D.m` and `example_euclidean3D.m`, are written for a specific flow field. To apply this analysis to your own flow field, you must advect an initial grid from time $$t_0$$ to the final time $$t_f$$. For a 2D flow, the initial grid coordinates `x0, y0` are advected to the final coordinates `xf, yf`. The function `compute_deform_euclidean2d.m` takes `x0, y0, xf, yf` as inputs and computes the singular values and their associated directions. The resulting FTLE field can then be visualized using the code provided in the `%% Visualize the result` section of `example_euclidean2D.m`. Similarly, for a 3D flow, the initial grid coordinates `x0, y0, z0` are advected to `xf, yf, zf`. These initial and final grids are passed to `compute_deform_euclidean3d.m`, which computes the deformation quantities needed to visualize the FTLE field.


## Summary of Workflow

The general workflow for computing FTLE fields on Euclidean grids is:

1. Define an initial grid at time $t_0$.
2. Advect the grid through the velocity field from $t_0$ to $t_f$.
3. Store the final grid positions at time $t_f$.
4. Use the appropriate deformation-analysis function:
   * `compute_deform_euclidean2d.m` for 2D flows.
   * `compute_deform_euclidean3d.m` for 3D flows.
5. Compute the singular values of the deformation gradient.
6. Visualize the FTLE field and the corresponding deformation axes.

---

## References
[1] Haller, G. (2015). Lagrangian coherent structures. *Annual Review of Fluid Mechanics*, 47(1).
