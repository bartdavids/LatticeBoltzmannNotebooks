# Boundary conditions
In Computation Flow Dynamics (CFD) there are a few types of boundary conditions:
1. Dirichlet Boundary Condition: This condition allows us to specify the value of a flow variable, such as velocity or pressure, at the boundary. 
2. Neumann Boundary Condition: This condition allows us to specify the gradient or rate of change of a flow variable at the boundary. This is useful when you want to impose a direction of flow to the fluid at the boundary.
3. Wall Boundary Condition: This condition represents solid surfaces within the computational domain. It ensures that the fluid interacts appropriately with these surfaces. For example, we can set a "no-slip" condition, where the fluid velocity at the wall is zero, mimicking the effect of a solid object.
4. Periodic Boundary Condition: This condition is used when we want to simulate a periodic flow pattern. The fluid is magically (or computationally) transported to the other side of the boundary.

## Dirichlet boundary condition
In the Dirichlet boundary condition, one of the variables is set to a specific value. Generally in the LBM, this is either the velocity ($\mathbf{u}$) or the density ($\rho$). The set velocity controls the velocity of the fluid that flows into the domain (or out). When one sets the density, this is equivelant to setting the pressure at the boundary, for pressure ($p$) is equal to $c_s^2 \rho$, where $c_s$ is the velocity of sound.

The masters of the Dirchlet boundary condition in the LBM are Zou and He, that defined the Dirichlet boundary conditions for velocity and pressure for 2Dd [here](https://arxiv.org/pdf/comp-gas/9611001.pdf). For 3D I use [this paper](https://arxiv.org/pdf/0811.4593.pdf) for reference. The proposed methods are really nice, in that they are local: you only need to know the population velocities $f_i$ at your boundary.

### Velocity boundary condition
First, let's get into the setting of the velocity boundary condition. We set $\mathbf{u}$ at a certain value. $\mathbf{u}$ is a vector, but generally you want the velocity to be perpendicular to the boundary you are setting the flow at. If your flow is coming in from the left, at $x = 0$, you are setting $u\_x$. To generalize: you set $u_{\alpha}$, where the subscript $\alpha$ is the direction of the normal vector at the boundary. The normal vector is the vector that goes out or into your domain at that boundary.

You can set the velocity as the macroscopic variable, but in the LBM, we are not thinking in the macroscopic quantities, but in populations of particles along a lattice, $f_i$. for more on the meaning of the lattice variables $e_i$ and $f_i$ you can checkout the README file [here](https://github.com/bartdavids/LatticeBoltzmannNotebooks). The relationship between the macorscopic quantities are:

$(1) \quad \rho = \sum_{i} f_i$

$(2) \quad \rho\mathbf{u} = \sum_{i} f_i e_{i}$

It is important to note that the density should be seen more as the amount of particles at the lattice node than the physical density that is a unit of mass over a volume (like $kg/m^3$). 

When setting your $\mathbf{u}\_{\alpha}$, you want to set the $f_i$ that influence the flow in the direction $\alpha$. Let's call these populations $f_{\alpha^+}$. The populations going out of your domain are than $f_{\alpha^-}$ and the tangental populations where $e_{\alpha} = 0$ are $f_{\alpha^0}$. 

Then: 
$(1) \quad \rho = \sum_{i} f_i = \sum_{i^+} f_i + \sum_{i^0} f_i + \sum_{i^-} f_i$

And you can determine your macroscopic quantites based on known factors by using:

$(3) \quad \rho = \frac{1}{1-v_{\alpha}} (\sum_{i^0} f_i + 2\sum_{i^-} f_i)$

This allows you to determine your macroscopic variables without knowing $f_{i^+}$.

Now you still need to determine $f_{i^+}$!

In the Zou He scheme you can determine your $f_{i^+}$ after determining the equilibrium velocities ($f^{eq}$):

```math
(4)\quad f_i^{eq} = w_i \rho \left(1 + \frac{\mathbf{u} e_{i}}{c_s^2} + \frac{(\mathbf{u} e_{i})^2}{2c_s^4} - \frac{\mathbf{u}^2}{2c_s^2}\right)
```
Where $w_i$ is the weight in the distribution function. It represents the weight associated with a specific lattice $i$'s direction. $c_s$ is the speed of sound in the lattice.

The results determines the equilibrium population velocities for $\rho$ and $\mathbf{u}$, and you can just set your $f_{i^+}$ as:
$f_{i^+} = f_{i^+}^{eq}$

So each iteration over time folows these steps (ignoring any other types of boundaries):

1. Determine macroscopic quantities $\rho$ and $\mathbf{u}$ using (1) and (2)

2. set $u_x$ in $\mathbf{u}$ at your boundary

3. Determine inflow density at your boundary using (3)

4. Determine the discrete equilibria velocities with (4)

5. Set $f_{i^+} = f_{i^+}^{eq}$ at your boundary

6. Perform the collission step using your scheme of choice

7. Perform bounce-back with object

8. Perform the streaming step

9. Next timestep!

### Pressure boundary condition
Recall the relationship between density and pressure such that:

$p = c_s^2 \rho$

So we actually aim to set the density as boundary condition! 

So we already know our $\rho$. Next, we need to determine $\mathbf{u}$ profile, specifically $u_{\alpha}$. The latteral $u$'s we assume to be 0. So we can simply rewrite (3) such that:

$\quad (4) u_x = 1 - \frac{1}{\rho}\left(\sum_{i^0}f_i + 2\sum_{i^-}f_i\right)$

And follow the rest of steps in an iteration (ignoring other types of boundary conditions):
1. Determine macroscopic quantities $\rho$ and $\mathbf{u}$ using (1) and (2)

2. set $\rho$ at your boundary

3. Determine inflow $\u_x$ at your boundary using (3)

4. Determine the discrete equilibria velocities with (4)

5. Set $f_{i^+} = f_{i^+}^{eq}$ at your boundary

6. Perform the collission step using your scheme of choice

7. Perform bounce-back with object

8. Perform the streaming step

9. Next timestep!

## Neumann boundary condition
The Neumann boundary condition specifies a specific rate at the boundary. Regretfully, I'm not aware of a method to do this locally. 

You can then set the velocities going in your domain using a finite difference method. The population velocities going in your domain are $f_{i^{in}}$, where the discrete direction $e$ goes into the model domain. If you set the boundary at x = 0 the $f_{i^{in}}$ velocities are those where $e_x$ is 1.

You can get the gradient to 
