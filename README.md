# Symplectic Flow 

This repository presents the SymplecticFlow implementation, a normalizing flow utilizing symplectic integration techniques, specifically tailored for Hamiltonian systems.

## Hamiltonian Dynamics and Canonical Coordinates

Hamiltonian dynamics describe a system using a set of canonical coordinates \((p, q)\), often referred to as momentum and position respectively. Hamilton's equations in canonical coordinates are given by:

1. $dq/dt = \frac{\partial H}{\partial p}$
2. $dp/dt = -\frac{\partial H}{\partial q}$

where $H(p, q)$ is the Hamiltonian, representing the total energy of the system. For the class of Hamiltonians considered here, which separate into kinetic and potential energy terms $H(p, q) = K(p) + U(q)$, these equations simplify to the system solved in the code:

1. $\(dx/dt = v\)$
2. $\(dv/dt = -\nabla U(x)\)$

where $x$ denotes position and $v$ denotes velocity. Here, $\nabla U(x)$ represents the force acting on a particle at position $x$, derived from the gradient of the potential energy $U(x)$.

## Symplectic Integration with Leapfrog Algorithm

The dynamics of the Hamiltonian system are discretized using the leapfrog algorithm, a symplectic integration scheme, which performs the updates in the following manner:

1. $v_{1/2} = v_0 - \frac{1}{2} \Delta t \, \nabla U(x_0)$
2. $x_1 = x_0 + \Delta t \, v_{1/2}$
3. $v_1 = v_{1/2} - \frac{1}{2} \Delta t \, \nabla U(x_1)$

## Learning the Potential

The potential $U(x)$ is not predefined but learned in a time-dependent manner. This potential serves to transport the particles back to a base distribution, taken to be a standard Gaussian $p_z(z)$.

## Probability Density Transformation

Due to the symplectic nature of the transformation, the Jacobian determinant is unity. This leads to a significant simplification in computing the transformed probability density, according to the change of variables formula:

$$\log p_x(x) = \log p_z(z)$$

Here, $p_x(x)$ denotes the probability density of the transformed distribution and $p_z(z)$ is the probability density of the base Gaussian distribution.

TODO:

- [ ] Use ODE solvers
- [ ] Add multiple potentials (multiple flow transforms)
- [ ] Extend to arbitrary dimensionality by decoupling position and momentum dimensionality 