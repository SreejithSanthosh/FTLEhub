---
title: FTLE with Uniform Trajectories — MATLAB (2D & 3D)
parent: Tutorial - FTLE codes
layout: home
nav_order: 3
---

## Introduction

This MATLAB package computes coherent structures using the Finite-Time Lyapunov Exponent (FTLE) for **flat Euclidean domains** in **2D** and **3D**. It advects a uniform grid of particles through a (possibly time-dependent) velocity field sampled at scattered points, then estimates the deformation gradient from the advected grid to produce FTLE and a simple isotropy measure.

Repository: **bfencil/EuclideanFTLEPythonMatlab**

This page documents the **MATLAB** side. A Python implementation with the same directory layout is also included in the repository.

---

## Pre-requisites

- MATLAB **R2022b** or later (earlier versions likely work; tested on Windows 10).  
- No special toolboxes required; uses base functions like `scatteredInterpolant`, `griddata`, `eig`, `det`.

---

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/bfencil/EuclideanFTLEPythonMatlab.git
   cd EuclideanFTLEPythonMatlab
   ```
2. Add the MATLAB code folders to your path (from inside MATLAB or at the top of your script):
   ```matlab
   addpath(fullfile(pwd,'FTLE','MatlabCode2D'));
   addpath(fullfile(pwd,'FTLE','MatlabCode3D'));
   % Optionally persist:
   % savepath;
   ```

---

## Repository layout (MATLAB)

```
FTLE/
  MatlabCode2D/
    Run_FTLE_2D.m        % entry point: forward & backward FTLE
    FTLE_2D.m            % internal driver
    RK4_advection_2D.m   % RK4 advection on scattered data (2D)
    FTLE_2D_compute.m    % deformation gradient, Cauchy–Green, FTLE/isotropy (2D)
    subdivide_time_steps.m
    Plot_FTLE_2D.m

  MatlabCode3D/
    Run_FTLE_3D.m
    FTLE_3D.m
    RK4_advection_3D.m
    FTLE_3D_compute.m
    subdivide_time_steps.m   % (shared; duplicate or place once on path)
    Plot_FTLE_3D.m

  Examples/
    BickleyFlow2D_Example.m   % 2D usage
    ABC_FlowMatlab.m          % 3D usage
    Data/
      bickley_flow_data.mat / .h5
      abc_flow_data.mat     / .h5
```

---

## Data format 

The 2D and 3D codes differ only by the presence of a third spatial axis `z`. We refer to the spatial dimension as `d ∈ {2,3}`.

- **Time steps**: vector of length `T` (integers or floats) indexing velocity snapshots.  
- **Velocity sample locations**: `velocity_points` of size `[M × d]` (fixed in time).  
- **Velocity values**: `velocity_vectors` of size `[M × d × T]` for time-dependent data (or `[M × d]` for time-independent; a shape of `[M × d × 1]` also works).  
- **Initial particle grid**:
  - **2D**: `X`, `Y` as `[Nx × Ny]` arrays (e.g., from `meshgrid` or `ndgrid`).  
  - **3D**: `X`, `Y`, `Z` as `[Nx × Ny × Nz]` arrays (e.g., from `ndgrid`).

**Important**: choose the particle grid so it lies **inside the convex hull** of `velocity_points`. Outside the hull, the MATLAB interpolator returns `NaN`; the advection functions replace those with `0` to mimic the Python behavior.

---

## Workflow overview

1. **Subdivide time**: build a fine time vector between the chosen `initial_time` and `final_time` using a step `dt ∈ (0,1]`.  
   - Here `dt` is a **fraction of one frame interval**; for unit-spaced frames, `dt=0.2` creates five RK4 substeps per frame.

2. **Advection (RK4)**: integrate particle positions with a classical RK4 solver that:
   - linearly **interpolates in time** between the two neighboring velocity frames; and
   - uses **scattered linear interpolation in space** over `velocity_points` each substep (`scatteredInterpolant` with `'linear','none'`, then `NaN→0` outside the hull).
     
3. **Deformation/FTLE**:
   - estimate the **deformation gradient** on the advected grid via **centered finite differences**;
   - form the **Cauchy–Green** tensor
     
     $$C = F^\top F$$;
   - compute **FTLE** from the largest eigenvalue $$\lambda_{\max}(C)$:
     
     $$\mathrm{FTLE} = \frac{1}{2\,|t_f - t_i|}\,\log \big(\sqrt{\lambda_{\max}(C)}\big)$$.
   - compute a simple **isotropy** measure:
     
     $$\mathrm{Iso} = \frac{1}{2\,|t_f - t_i|}\,\log \det(C)$$.

Both **forward** and **backward** FTLE are supported by reversing the time axis and flipping the sign of `dt` internally.

---

## API summary (MATLAB)

### 2D
- **`Run_FTLE_2D(velocity_points,velocity_vectors,X,Y,dt,initial_time,final_time,time_steps,plot_ftle,save_plot_path)`**  
  Runs forward and backward FTLE. Returns `[ftle,traj,iso,back_ftle,back_traj,back_iso]`.  
  - `traj` has size `[N_particles × 2 × K]`; `ftle` and `iso` are flattened vectors (length `N_particles`).

- **`FTLE_2D(...)`** — internal driver invoked by `Run_FTLE_2D`.

- **`RK4_advection_2D(velocity_points,velocity_vectors,trajectories,dt,fine_time)`** — RK4 integration with time/space interpolation.

- **`FTLE_2D_compute(X0,Y0,Xf,Yf,ti,tf)`** — compute FTLE and isotropy on a grid (returns flattened vectors).

- **Utilities**: `subdivide_time_steps`, `Plot_FTLE_2D`.

### 3D
- **`Run_FTLE_3D(velocity_points,velocity_vectors,X,Y,Z,dt,initial_time,final_time,time_steps,plot_ftle,save_plot_path)`**  
  Returns `[ftle,traj,iso,back_ftle,back_traj,back_iso]` with `traj` sized `[N_particles × 3 × K]`.

- **`FTLE_3D(...)`**, **`RK4_advection_3D(...)`**, **`FTLE_3D_compute(...)`**, `Plot_FTLE_3D` mirror the 2D versions with a \(3	imes3\) deformation gradient.

---

## Running the examples

From MATLAB, `cd` into `FTLE/Examples` and run:

- **2D** (Bickley jet): `BickleyFlowMatlab.M`  
  Loads `Examples/Data/bickley_flow_data.(mat|h5)` and calls `Run_FTLE_2D(...)` with plotting enabled.

- **3D** (ABC flow): `ABC_FlowMatlab.M`  
  Loads `Examples/Data/abc_flow_data.(mat|h5)` and calls `Run_FTLE_3D(...)` with plotting enabled.

Both example scripts add the corresponding `MatlabCode2D`/`MatlabCode3D` folders to the path automatically.

---

## Practical tips

- **Use `ndgrid` for 3D** (or `meshgrid(...,'ij')` in Python) to avoid transposes. In 2D either `meshgrid` or `ndgrid` is fine as long as shapes match what the code expects.  
- **Convex hull coverage**: if many particles exit the hull of `velocity_points`, large regions may advect with zero velocity (due to the `NaN→0` policy). Reduce the grid extent or expand the data domain.  
- **`dt` vs. `time_steps`**: `dt` is a fraction of one frame interval. If your frames aren’t unit-spaced, scale `dt` accordingly.  
- **Borders**: centered differences skip the outermost ring; FTLE there is returned as `NaN`.  
- **Performance**: scattered interpolation dominates runtime. Fewer particles or a larger `dt` (fewer substeps) speeds things up. If your data are on a rectilinear grid, grid-based interpolation will be faster.

---

## Troubleshooting

- **“Undefined function or variable”**: the code folders are not on the MATLAB path. Call `addpath` as shown above.  
- **Dimension mismatch**: ensure `velocity_vectors` is `[M × d × T]` (or `[M × d × 1]`). If using `.h5`, verify dataset ordering and permute if needed.  
- **All zeros during advection**: particles likely outside the convex hull; extend the data or shrink the particle grid.  
- **All `NaN` FTLE on the border**: expected; centered differences require neighbors.

---


