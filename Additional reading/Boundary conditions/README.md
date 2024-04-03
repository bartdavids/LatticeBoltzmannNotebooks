# Boundary conditions
In Computation Flow Dynamics (CFD) there are a few types of boundary conditions:
1. Dirichlet Boundary Condition: This condition allows us to specify the value of a flow variable, such as velocity or pressure, at the boundary. 
2. Neumann Boundary Condition: This condition allows us to specify the gradient or rate of change of a flow variable at the boundary. This is useful when you want to impose a direction of flow to the fluid at the boundary.
3. Wall Boundary Condition: This condition represents solid surfaces within the computational domain. It ensures that the fluid interacts appropriately with these surfaces. For example, we can set a "no-slip" condition, where the fluid velocity at the wall is zero, mimicking the effect of a solid object.
4. Periodic Boundary Condition: This condition is used when we want to simulate a periodic flow pattern. The fluid is magically (or computationally) transported to the other side of the boundary.

## Dirichlet boundary condition
In the Dirichlet boundary condition, one of the variables is set to a specific value. Generally in the LBM, this is either the velocity ($\mathbf{u}$) or the density ($\rho$). The set velocity controls the velocity of the fluid that flows into the domain (or out). When one sets the density, this is equivelant to setting the pressure at the boundary, for pressure ($p$) is equal to $c_s^2 \rho$, where $c_s$ is the velocity of sound.

The masters of the Dirchlet boundary condition in the LBM are Zou and He, that defined the Dirichlet boundary conditions for velocity and pressure for 2d [here](https://arxiv.org/pdf/comp-gas/9611001.pdf). For 3D I use [this paper](https://arxiv.org/pdf/0811.4593.pdf) for reference.

### Velocity boundary condition
First, let's get into the setting of the velocity boundary condition. We set $\mathbf{u}$ at a certain value. $\mathbf{u}$ is a vector, but generally you want the velocity to be perpendicular to the boundary you are setting the flow at. If your flow is coming in from the left, at $x = 0$, you are setting $\mathbf{u}\_x$. To generalize: you set $\mathbf{u}_{\alpha}$, where the subscript $\alpha$ is the direction of the normal vector at the boundary. The normal vector is the vector that goes out or into your domain at that boundary.

You can set the velocity as the macroscopic variable, but in the LBM, we are not thinking in the macroscopic variables, but in populations of particles along a lattice, $f_i$. for more on the meaning of the lattice variables $e_i$ and $f_i$ you can checkout the README file [here](https://github.com/bartdavids/LatticeBoltzmannNotebooks). The relationship between the macorscopic variables are:

$\quad \rho = \sum_{i} f_i$

$\quad \mathbf{u} = \sum_{i} f_i e_{i}$
