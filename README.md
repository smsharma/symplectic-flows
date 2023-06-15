# Symplectic Normalizing Flow 

Experimental implementation of a normalizing flow that uses (reversible) symplectic integration to define the transformation. The time-dependent potential is optimized to pull back data points into the base distribution.

## Hamiltonian dynamics and canonical coordinates

Hamiltonian dynamics describe a system using a set of canonical coordinates $(p, q)$, often referred to as momentum and position respectively. Hamilton's equations in canonical coordinates are given by:

1. $\mathrm dq/\mathrm dt = \frac{\partial H}{\partial p}$
2. $\mathrm dp/\mathrm dt = -\frac{\partial H}{\partial q}$

where $H(p, q)$ is the Hamiltonian, representing the total energy of the system. For the class of Hamiltonians considered here, which separate into kinetic and potential energy terms $H(p, q) = K(p) + V(q)$, these equations simplify to the system solved in the code:

1. $\mathrm dx/\mathrm dt = v$
2. $\mathrm dv/\mathrm dt = -\nabla V(x)$

where $x$ denotes position and $v$ denotes velocity.

## Symplectic integration with leapfrog

The dynamics of the Hamiltonian system are discretized using the leapfrog algorithm, a symplectic integration scheme, which performs updates in the following manner:

1. $v_{1/2} = v_0 - \frac{1}{2} \Delta t \, \nabla V(x_0)$
2. $x_1 = x_0 + \Delta t \, v_{1/2}$
3. $v_1 = v_{1/2} - \frac{1}{2} \Delta t \, \nabla V(x_1)$

## Probability density and sampling

The potential $V(x; t)$ and its time dependence are the time-dependent parameters of the flow. It is modeled by a simple MLP. 

Due to the symplectic nature of the transformation, the Jacobian determinant is unity. The change of variables formula is therefore:
$$\log p_x(x) = \log \pi_z(z)$$
Here, $p_x(x)$ denotes the probability density of the transformed distribution and $\pi_z(z)$ is the probability density of the base Gaussian distribution. Sampling is done by drawing from the base Gaussian, and then transporting particles in the forward direction using the learned potential. Running the flow backwards,  the potential transports the particles back to a base distribution, a standard Gaussian $\pi_z(z)$. 