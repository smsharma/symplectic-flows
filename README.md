# Symplectic Normalizing Flow 

Experimental implementation of a normalizing flow that uses (reversible) symplectic transformations. The time-dependent potential is optimized to pull back data points into the base distribution. Uses Jax, Flax, and Diffrax. 

The flow is not particularly expressive, and is mainly intended to be pedagogical. However, it does seem to work well when the canonical coordinates have a natural physical interpretation.

- Example in [demo.ipynb](symplectic-flows/demo.ipynb).
- [phase-space.ipynb](symplectic-flows/phase-space.ipynb) is an attempt to apply a discretized leapfrog-based transformation for phase-space density estimation while simultaneously tying it to a physical present-day potential $\Phi(x)$. In this case, we have physical positions and velocities (phase-space samples), and the loss is a sum of phase-space log-density and CBE terms,
$$\mathcal L = \sum_{i=1}^{N_\mathrm{stars}}\left[-\log f(x_i, v_i) + \left|\frac{\mathrm df(x_i, v_i)}{\mathrm dt}\right|\right]$$
where
$$\frac{\mathrm df}{\mathrm dt} = -v \frac{\mathrm df}{dx} - \dot v \frac{\mathrm df}{\mathrm dv}.$$

## Hamiltonian dynamics and canonical coordinates

Hamiltonian dynamics describe a system using a set of canonical coordinates $(p, q)$, often referred to as momentum and position respectively. Hamilton's equations in canonical coordinates are given by:

1. $\mathrm dq/\mathrm dt = \frac{\partial H}{\partial p}$
2. $\mathrm dp/\mathrm dt = -\frac{\partial H}{\partial q}$

where $H(p, q)$ is the Hamiltonian, representing the total energy of the system. For the class of Hamiltonians considered here, which separate into kinetic and potential energy terms $H(p, q) = K(p) + \Phi(q)$, these equations simplify to the system solved in the code:

1. $\mathrm dx/\mathrm dt = v$
2. $\mathrm dv/\mathrm dt = -\nabla \Phi(x)$

where $x$ denotes position and $v$ denotes velocity.

## Symplectic integration with leapfrog

The dynamics of the Hamiltonian system are discretized using the leapfrog algorithm, a symplectic integration scheme, which performs updates in the following manner.

1. $v_{1/2} = v_0 - \frac{1}{2} \Delta t \cdot \nabla \Phi(x_0)$
2. $x_1 = x_0 + \Delta t \cdot v_{1/2}$
3. $v_1 = v_{1/2} - \frac{1}{2} \Delta t \cdot \nabla \Phi(x_1)$

This is implemented as an ODE with Diffrax.

## Probability density and sampling

The potential 
$$\Phi(x; t): \mathbb R^{n+1}\rightarrow \mathbb R$$
along with its time dependence define the flow transformation. It is modeled by a simple MLP. 

Due to the symplectic nature of the transformation, the Jacobian determinant is unity. The change of variables formula is therefore:
$$\log p_x(x) = \log \pi_z(z)$$
Here, $p_x(x)$ is the probability density of the target distribution and $\pi_z(z)$ is the probability density of the base Gaussian distribution. Sampling is done by drawing from the base Gaussian, and then transporting particles in the forward direction using the learned potential. Running the flow backwards, the potential transports the particles back to a base distribution.
