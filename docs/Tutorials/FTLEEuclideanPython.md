---
title: FTLE with Uniform Particle Placement — Python (2D & 3D)
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---

## Introduction

This package computes coherent structures using the Finite-Time Lyapunov Exponent (FTLE) for **flat Euclidean domains** in **2D** and **3D**. It advects a uniform grid of particles through a (possibly time-dependent) velocity field sampled at scattered points, then estimates the deformation gradient from the advected grid to produce FTLE and a simple isotropy measure.

Repository: **[github.com/bfencil/EuclideanFTLEPythonMatlab](https://github.com/bfencil/EuclideanFTLEPythonMatlab)**

The MATLAB translation mirrors the Python pipeline and directory layout, but this page documents the **Python** side.

---

## Pre-requisites

Developed with **Python 3.11** on Windows 10. Required packages:

- `numpy`
- `scipy`
- `matplotlib`
- `numba`

Install them via:

```
pip install -r requirements.txt
```

---

## Installation

```
git clone https://github.com/bfencil/EuclideanFTLEPythonMatlab.git
cd EuclideanFTLEPythonMatlab
pip install -r requirements.txt
```

---

## Repository layout (Python)

```
FTLE/
  PythonCode2D/
    FlatFTLEMain2D.py      # entry points: run_FTLE_2d, FTLE_2d
    advection2D.py         # RK4_advection_2d
    FTLECompute2D.py       # FTLE_2d_compute
    utilities.py           # subdivide_time_steps, plot_FTLE_2d

  PythonCode3D/
    FlatFTLEMain3D.py      # entry points: run_FTLE_3d, FTLE_3d
    advection3D.py         # RK4_advection_3d
    FTLECompute3D.py       # FTLE_3d_compute
    utilities.py           # subdivide_time_steps, plot_FTLE_3d

  Examples/
    Data/
      bickley_flow_data.h5 / .mat  # 2D example data
      abc_flow_data.h5     / .mat  # 3D example data
    BickleyFlowPython.py          # 2D usage example
    ABC_FlowPython.py             # 3D usage example
```

---

## Data format (common ideas)

The 2D and 3D codes differ only by the presence of a third spatial axis `z`. We refer to the spatial dimension as `d ∈ {2,3}`.

- **Time steps**: array of length `T` (ints/floats) indexing your velocity snapshots.
- **Velocity sample locations**: `velocity_points` with shape `(M, d)`.  
  These are fixed in time.
- **Velocity values**: `velocity_vectors` with shape `(M, d, T)` for time-dependent data (or `(M, d)` for time-independent; broadcasting `T=1` also works).
- **Initial particle grid**:
  - **2D**: `x_grid_parts, y_grid_parts` as 2D arrays produced by `np.meshgrid` (or `np.ndgrid`-style layout).
  - **3D**: `x_grid_parts, y_grid_parts, z_grid_parts` as 3D arrays from `np.meshgrid(..., indexing='ij')` (or transposed equivalently).

**Important**: make sure the particle grid lies **inside the convex hull** of `velocity_points`. Outside the hull, the Python interpolator uses a fill value of 0 (see “Numerical details”).

---

## Workflow overview

1. **Subdivide time**: build a fine time vector between the chosen `initial_time` and `final_time` using step `dt ∈ (0,1]`.  
   - `dt` is a **fraction of one integer frame**; e.g., if your frames are at integers, `dt=0.2` creates 5 RK4 substeps per frame.
2. **Advection (RK4)**: integrate particle positions with a classical RK4 solver that:
   - linearly **interpolates in time** between the two neighboring velocity frames;
   - uses **scattered linear interpolation in space** over `velocity_points` at each substep.
3. **Deformation/FTLE**:
   - estimate the **deformation gradient** on the advected grid via **centered finite differences**;
   - form the **Cauchy–Green** tensor \(C = F^\top F\);
   - compute **FTLE** from the largest eigenvalue \(\lambda_{\max}(C)\):
     \[
     \mathrm{FTLE} = \frac{1}{2\,|t_f - t_i|}\,\log\!\big(\sqrt{\lambda_{\max}(C)}\big).
     \]
   - compute a simple **isotropy** measure:
     \[
     \mathrm{Iso} = \frac{1}{2\,|t_f - t_i|}\,\log\!\det(C).
     \]

Both **forward** and **backward** FTLE are supported by reversing the time axis and using \(dt<0\) internally.

---

## API summary (Python)

### 2D

- **`run_FTLE_2d(...)`** — single call to run forward & backward FTLE, optionally plot results.
  - Inputs: `velocity_points`, `velocity_vectors`, `x_grid_parts`, `y_grid_parts`, `dt`, `initial_time`, `final_time`, `time_steps`, `plot_ftle=False`, `save_plot_path=None`.
  - Outputs: forward `(ftle, trajectories, isotropy)` and backward `(back_ftle, back_trajectories, back_isotropy)`; `ftle` and `isotropy` are **flattened** to match plotting utilities; `trajectories` has shape `(N_particles, 2, K)`.

- **`FTLE_2d(...)`** — internal driver used by `run_FTLE_2d`.

- **`RK4_advection_2d(...)`** — RK4 particle integration with time/space interpolation.

- **`FTLE_2d_compute(...)`** — centered differences + Cauchy–Green + FTLE/isotropy (returns flattened arrays).

- **`subdivide_time_steps(...)`**, **`plot_FTLE_2d(...)`** — utilities.

### 3D

- **`run_FTLE_3d(...)`** — as above, with `z_grid_parts`.

- **`FTLE_3d(...)`**, **`RK4_advection_3d(...)`**, **`FTLE_3d_compute(...)`**, and utilities mirror the 2D versions with 3D arrays and a \(3\times 3\) deformation gradient.

---

## Numerical details & choices

- **Time interpolation**: linear interpolation between the integer frames bracketing each substep time \(t\).
- **Spatial interpolation**: `scipy.interpolate.LinearNDInterpolator` on a Delaunay triangulation of `velocity_points` each step; outside the convex hull, the **fill value is 0** (particles feel zero velocity).
- **Time stepping**: classical RK4 with constant `dt` across the fine time vector.
- **Deformation gradient**:
  - **2D**: centered differences along the advected grid axes to form \(F \in \mathbb{R}^{2\times 2}\).
  - **3D**: centered differences give \(F \in \mathbb{R}^{3\times 3}\).
  - Cauchy–Green \(C = F^\top F\); eigenvalues from the symmetric part for robustness.
- **Outputs**:
  - `trajectories`: `(N_particles, d, K)` positions for all substeps including the start.
  - `ftle`, `isotropy`: returned **flattened** (length \(N_{\text{particles}}\)) for easy plotting; reshape with the original grid dimensions if needed.

---

## Running the examples

Example scripts live in `FTLE/Examples/`:

- **2D** (Bickley jet): `BickleyFlowPython.py`  
  Loads `Examples/Data/bickley_flow_data.(h5|mat)` and runs `run_FTLE_2d(...)` with plotting.

- **3D** (ABC flow): `ABC_FlowPython.py`  
  Loads `Examples/Data/abc_flow_data.(h5|mat)` and runs `run_FTLE_3d(...)` with plotting.

Run them from the repo root or `FTLE/Examples` (they set up relative paths internally).

---

## Practical tips

- **Grid inside data hull**: choose the particle grid so it stays inside the convex hull of `velocity_points` during the integration window. Otherwise, parts of the domain will behave as if the velocity were zero.
- **`dt` vs. `time_steps`**: `dt` is a fraction of one unit gap in `time_steps`. If your velocity frames are not unit-spaced, scale accordingly.
- **Boundaries**: centered differences skip the outermost ring of the grid; FTLE there is returned as `NaN`.
- **Performance**:
  - Advection is dominated by scattered interpolation. Coarser `dt` or fewer particles speeds things up.
  - If your samples lie on a regular grid, consider switching to grid-based interpolators for large problems.

---

## Troubleshooting

- **“Initial and final times must be in `time_steps`.”**  
  Use values from the provided `time_steps` array; the drivers index into it.
- **Particles stop moving in corners**  
  Likely outside the convex hull of `velocity_points`; expand the data region or shrink the particle grid/time window.
- **All `NaN` FTLE on the border**  
  Expected: centered differences require neighbors; only interior cells get FTLE values.

---

## References

- Standard FTLE formulation via Cauchy–Green strain and finite-time flow maps.
- Example flows: Bickley jet (2D), ABC flow (3D), widely used testbeds for Lagrangian coherent structure detection.

---

If you also plan to use the **MATLAB** implementation in this repo, see the corresponding MATLAB tutorial page; the inputs/outputs mirror the Python API with identical directory structure and naming (2D/3D capitalization preserved).
