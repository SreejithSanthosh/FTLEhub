
---
title: FTLE with uniform particle placement: Python Implementation 
parent: Tutorial - FTLE codes
layout: home
nav_order: 1
---




## 2d Time Indepedent Velocity Field ##

For a constant time velocity field, there are two three options for what that velocity field can look like. The first is that the velocity field is given by a known equation, in which case no velocity interpolation needs to be done. The second is that the velocity field is distributed on a grid, which allows for the use of an interpolation package like ``griddedInterpolation`` from []. The last is that the velocity field is sparse sampled from your space, in which case we can use ``LinearNDInterpolator`` from scipy to interpolate the velocites. For ease of use the following code for advecting particles will assume a sparse velocity field. Here is the advectino code using RK4:

```
def advect_particles_rk4(velocity_points, velocity_vectors, particles, dt, time_steps):

    """
    Advects a uniform grid of particles using a sparse velocity field with RK4 integration.

    Parameters:
        velocity_points (ndarray): (M, 2) array of known velocity locations.
        velocity_vectors (ndarray): (M, 2) array of velocity vectors at those locations.
        particles (ndarray): (N, 2) array of initial particle positions.
        dt (float): Time step size for integration.
        time_steps (ndarray): Array of time steps to integrate over.

    Returns:
        ndarray: (N, 2, T) array of advected particle positions over time.
    """

    num_particles = particles.shape[0]
    num_time_steps = len(time_steps)

    
    trajectories = np.zeros((num_particles, 2, num_time_steps))
    trajectories[:, :, 0] = particles  

    # Interpolators for sparse or non sparse velocity field
    interp_u = LinearNDInterpolator(velocity_points, velocity_vectors[:, 0], fill_value=0)
    interp_v = LinearNDInterpolator(velocity_points, velocity_vectors[:, 1], fill_value=0)

    
    for t in range(1, num_time_steps):
        
        x_prev, y_prev = trajectories[:, 0, t - 1], trajectories[:, 1, t - 1]

        # RK4 steps
        k1_x, k1_y = interp_u(x_prev, y_prev), interp_v(x_prev, y_prev)
        
        k2_x, k2_y = interp_u(x_prev + 0.5 * dt * k1_x, y_prev + 0.5 * dt * k1_y), \
                     interp_v(x_prev + 0.5 * dt * k1_x, y_prev + 0.5 * dt * k1_y)
        
        k3_x, k3_y = interp_u(x_prev + 0.5 * dt * k2_x, y_prev + 0.5 * dt * k2_y), \
                     interp_v(x_prev + 0.5 * dt * k2_x, y_prev + 0.5 * dt * k2_y)
        
        k4_x, k4_y = interp_u(x_prev + dt * k3_x, y_prev + dt * k3_y), \
                     interp_v(x_prev + dt * k3_x, y_prev + dt * k3_y)

        x_new = x_prev + (dt / 6.0) * (k1_x + 2 * k2_x + 2 * k3_x + k4_x)
        y_new = y_prev + (dt / 6.0) * (k1_y + 2 * k2_y + 2 * k3_y + k4_y)

        # Update trajectory
        trajectories[:, 0, t] = x_new
        trajectories[:, 1, t] = y_new

    return trajectories

```




