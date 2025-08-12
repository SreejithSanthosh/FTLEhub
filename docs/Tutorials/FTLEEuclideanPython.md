
---
title: FTLE with uniform particle placement: Python Implementation 
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---

## Introduction 

The Python code for computing Coherent Structures based on Finite-Time-Lyapunov Exponents (FTLE) and Lagrangian deformation for flow on flat domains is available at this [link](github.com/bfencil/EuclideanFTLEPythonMatlab). 


## Pre-requisites

This code was built in Python 3.11, on a Windows 10 system. The following python packages are needed and can be installed with `pip` in your Python environment: 

- Numpy
- Scipy
- Matplotlib
- Numba


## Installation 

To install the code, navigate to the path where you would want to install it on the terminal and clone the GitHub repository using the command: 

```
git clone  https://github.com/bfencil/EuclideanFTLEPython.git
cd EuclideanFTLEPythonMatlab
pip install -r requirements.txt
```



## Data required for Langrangian analaysis and Formating

The difference in data between usage in the 2D and 3D FTLE codes is a third spatial data axis `z`, we denote three or two spatial components as `xyz`


- Time steps: An array of discrete time steps, where each time step can be a float value.
  
- Initial positions : A mesh grid of positions (list the dimensions of this data

- Velocity positions : An array listing the positions of where the velocties were sampled from. The data format is of the form ($$V_N$$, `xyx`), where $$V_N$$ is the amount of smapled velocity points. This is associated in a one to one fashion to the Velocity values data.

- Velocity values : An array listing the value of the velocity at a velocity position. The data format is of the form ($$V_N$$, `xyx`), where $$V_N$$ is the amount of smapled velocity points. This is associated in a one to one fashion to the Velocity positions data.





## Performing Lagrangian Analysis







