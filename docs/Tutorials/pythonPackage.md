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




## Dense FTLE Computations in $$\mathbb R^2$$ and $$\mathbb R^3$$




## Sparse FTLE Computations $$\mathbb R^2$$ and $$\mathbb R^3$$






## Surfaces in $\mathbb{R}^3$

### Overview

This section provides functionality for computing FTLE (Finite Time Lyapunov Exponent) fields for flows on triangulated surfaces in $\mathbb{R}^3$. 

This is distinct from the dense and sparse FTLE methods in Euclidean domains, as here the mesh itself evolves over time and particles are constrained to remain on the surface throughout the advection process.

The primary function for users is `ftle_mesh`, which wraps the entire process of particle advection and FTLE computation for a triangulated, staggered mesh surface.

Additional functions such as `FTLE_compute` are available if finer control is needed (e.g. using your own advection scheme or particle data).

---

## Example Usage

```python
from ftle.curved.mesh import ftle_mesh
import h5py


def load_mesh_data_h5(h5_file_path):
    """
    Load staggered curved surface mesh data from an HDF5 file.
    Expects groups:
        /node_cons/{t}
        /position/{t}
        /velocity/{t}
        /time_steps
    """
    with h5py.File(h5_file_path, 'r') as f:
        time_steps = f["time_steps"][:]
        keys = sorted(f["position"].keys(), key=lambda k: int(k))
        position = [f["position"][k][:] for k in keys]
        velocity = [f["velocity"][k][:] for k in keys]
        node_cons = [f["node_cons"][k][:] for k in keys]

    return {
        'node_cons': node_cons,
        'position': position,
        'velocity': velocity,
        'time_steps': time_steps
    }

file_path = "path/to/your/mesh_data.h5"

mesh_data = load_mesh_data_h5(file_path)

ftle, trajectories = ftle_mesh(
    node_cons = mesh_data['node_cons'],
    node_positions = mesh_data['position'],
    node_velocities = mesh_data['velocity'],
    particle_positions = mesh_data['position'][0],
    initial_time = 0,
    final_time = 28,
    time_steps = mesh_data['time_steps'],
    direction = "forward",
    plot_ftle = True,
    neighborhood = 15
)
```

## Inputs for `ftle_mesh`

The `ftle_mesh` function computes the FTLE field for flows on a triangulated surface in $\mathbb{R}^3$. It takes the following inputs:

- `node_cons`  
  A list of arrays of shape `(M_t, 3)` for each time step. Each array describes the mesh connectivity at that time step, where `M_t` is the number of faces at time `t`.

- `node_positions`  
  A list of arrays of shape `(N_t, 3)` for each time step, where `N_t` is the number of nodes at time `t`. These are the spatial coordinates of the mesh nodes.

- `node_velocities`  
  A list of arrays of shape `(N_t, 3)` for each time step. These are the velocity vectors at each node.

- `particle_positions`  
  An array of shape `(P, 3)` containing the initial positions of the particles to advect on the surface, where `P` is the number of particles.

- `initial_time`  
  An integer specifying the starting time index for the advection.

- `final_time`  
  An integer specifying the ending time index for the advection.

- `time_steps`  
  An array of time values corresponding to the available mesh data (usually consecutive integers starting at 0).

- `direction`  
  A string, either `"forward"` or `"backward"` indicating the direction of advection.

- `plot_ftle`  
  Optional boolean. If `True`, automatically generates a 3D PyVista plot of the FTLE field.

- `neighborhood`  
  Integer (default 15). The number of nearest neighboring particles used when computing the local FTLE gradient.

- `lam`  
  Regularization parameter (default $1 \times 10^{-10}$). Helps stabilize the least-squares fit of the deformation gradient in regions with near-linear configurations.

---

## Using `FTLE_compute` directly

The `FTLE_compute` function computes FTLE values directly given initial and final particle positions. This is useful if:

- You already have your own particle trajectories.
- You want to bypass `ftle_mesh` and just compute FTLE.

Example usage:

```python
from ftle.curved.mesh import FTLE_compute

ftle = FTLE_compute(
    node_connections_t = node_cons,
    node_positions_t = node_positions,
    centroids_t = centroids,
    initial_positions = my_initial_positions,
    final_positions = my_final_positions,
    initial_time = 0,
    final_time = 28,
    neighborhood = 15
)
```




