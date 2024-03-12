# LatticeBoltzmannNotebooks
Notebooks with explanation and step-by-step instructions to implementing the Lattice Boltzmann method.

Huge thanks to [Machine Learning and Simulations](https://www.youtube.com/@MachineLearningSimulation) on Youtube for inspiring me to go on this journey. The code is based originally on [this video](https://www.youtube.com/watch?v=ZUXmO4hu-20&list=LL&index=17&ab_channel=MachineLearning%26Simulation) for implementing the Lattice Boltzmann method in 2D.

The lattice Boltzmann method (LBM) is a computational fluid dynamics technique that divides the fluid into a lattice of cells. It uses distribution functions to represent the probability of particle velocities at each cell. The method involves two steps: collision, where particles exchange momentum and energy, and streaming, where particles move to neighboring cells based on their velocities. LBM is parallelizable and computationally efficient. In these notebooks we take advantage of those properties using the Jax python module.

## Getting started
You need to install jupyter notebook, python and [jax](https://jax.readthedocs.io/en/latest/installation.html). However, these have been developed in [Google Colab](https://colab.research.google.com/), where you can make free use of their GPU's as well.

The notebooks are self contained. They have the step by step instructions and ready to run code withouth further dependencies.

## This repo
This repository is a series of notebooks with explanation and the code to perform the computations and display the results. They start out with the simplest applications of the LBM, growing in complexity. The goal is to cover the 2D and 3D application of the LBM, it's take on units, stress and forces, the Multi-relaxation Time (MRT) method, Immersed boundary method (IBM) and Large Eddy Simulations (LES). In the future I'm looking to tackle multiple fluids, adaptive gridding and coupling with temperature.

Below is a list of the available notebooks. They link to further down this README file to go into more detail.
- [**LBM_in_3D**](#lbm_in_3d)
- [**LBM_in_3D_drag**](#lbm_in_3d_drag): 

### LBM_in_3D
![image](https://github.com/bartdavids/LatticeBoltzmannNotebooks/blob/main/Images/LBM_in_3D.gif)
The original script by MLS is in 2D and I've edited his script to apply to 3D. This simulation is flow over a cylinder, to determine the similarity with the 2D solution. It goes into detail of the steps taken to add the additional dimension. Just like the original LBM script it determines the velocity fields of the simulation. 

So, what is the LBM? I really recommend the LBM video linked above to get you started, but for reference and completeness here it is as well.

The LBM is a Computational Flow Dynamics (CFD) application that doesn't model the macroscopic properties (like velocity and mass) directly. What it does model, is fictive particles along a lattice and how they move along those lattices. The lattice can be visualised for each point in a grid (in 3D) as follows ([source](https://link.springer.com/chapter/10.1007/978-3-031-25787-2_5)):

![image](https://media.springernature.com/lw685/springer-static/image/chp%3A10.1007%2F978-3-031-25787-2_5/MediaObjects/508023_1_En_5_Fig1_HTML.png)

Each lattice is considered a population of particles. How those particles travel along the lattice is determined by the Boltzmann equation, which is a statistical approach to how particles would behave moving towards equilibrium. When a fluid has a certain velocity, not all the water molecules will move in the same direction, or along the same lattice. Some would like to move down all the other directions due to particles bouncing into each other, Brownian motion, etc. 

#### Colission step
This behaviour can be approached statistically and the distribution is determined in the collision step of the LBM:

```math
(1)\quad f_i(\mathbf{x}, t) = f_i(\mathbf{x}, t) - \frac{1}{\tau} \left(f_i(\mathbf{x}, t) - f_i^{eq}(\mathbf{x}, t)\right)
```
where:
- $f_i(\mathbf{x}, t)$ represents the current distribution of the populations at position $\(\mathbf{x}\)$ and time $\(t\)$ for each population $i$.
- $f_i^{eq}(\mathbf{x}, t)$ is the equilibrium distribution function at the same position and time.
- $\tau$ is the relaxation time that determines the rate of collision and relaxation. It is related to the kinematic viscosity ($\mu$) and speed of sound in the fluid ($c_s$) according to $\tau = (1.0 / (\mu/(c_s^2) + 0.5))$.

The equilibrium distribution function is where the populations get distributed based on their previous popluation distribution according to the Boltzmann equations, in the Single-relaxation Time (SRT) LBM expressed as:

```math
(2)\quad f_i^{eq} = w_i \rho \left(1 + \frac{\mathbf{u} e_{i}}{c_s^2} + \frac{(\mathbf{u} e_{i})^2}{2c_s^4} - \frac{\mathbf{u}^2}{2c_s^2}\right)
```
where:
- $\mathbf{u}$ is the velocity vector.
- $\rho$ is the density scalar.
- $w_i$ is the weight in the distribution function. It represents the weight associated with a specific lattice $i$'s direction.
- $e_i$ Is the discrete direction of the lattice $i$. The rest lattice is (0, 0, 0), indicating no movement. The lattice moving from the centre towards the right is (1, 0, 0), etc.
At location $\mathbf{x}$ is implied for clearity of the formula.

Formula's 1 and 2 are the collission step. You can see the equilibrium distribution as the population distribution, after a disturbance (like the streaming step), trying to reach equilibrium. How far it gets to equilibrium, is determined by the relaxation time $\tau$.

#### Bounce-back rule
Now, the collission step is not to be confused with object interaction, which is resolved after the collision step. At the boundary of the object, the velocity of the fluid is 0 and in the LBM we enforce that by replacing the lattice distribution by its opposite lattice distribution. So for each lattice $i$ we set $f_i = f_{\hat{i}}$, where $\hat{i}$ is the lattice that has the opposite direction of lattice $i$.

#### Streaming step
After the collision  and bounce-back steps, is the streaming step. It is a good, descriptive term, because the populations move along their lattice direction ($e_i$) to the next grid point that lattice is pointing towards. The particle populations are streaming to where they are pointed at.

#### Macroscopic properties
You can get the macroscopic properties at location $\mathbf{x}$ from the population distribution $f_i$ and their direction $e_i$:

$(3)\quad \rho = \sum_{i} f_i$

$(4)\quad \mathbf{u} = \sum_{i} f_i e_{i}$

In conclusion, this gives us the tool to perform the Lattive Boltzmann Method. I'll let the description of the boundary conditions to the notebook. 

### LBM_in_3D_drag
This notebook determines the flow around a sphere and from it, determines the drag coefficient ($c_d$) and strain rate tensor. The drag coefficient we determine by:

$c_d = \frac{2F_d}{\rho u^2 A}$

Where $F_d$ is the drag force, or the force acting on the object over the axis we measure the drag over. $A$ is the wet surface of the sphere and $rho$ the denisty of the fluid. Our notation of $u$ is somewhat less buff here, to indicate it is the flow speed of the object, relative to the fluid.
