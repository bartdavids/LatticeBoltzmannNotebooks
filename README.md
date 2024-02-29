# LatticeBoltzmannNotebooks
Notebooks with explanation and step-by-step instructions to implementing the Lattice Boltzmann method.

Huge thanks to [Machine Learning and Simulations](https://www.youtube.com/@MachineLearningSimulation) on Youtube for inspiring me to go on this journey. The code is based originally on [this video](https://www.youtube.com/watch?v=ZUXmO4hu-20&list=LL&index=17&ab_channel=MachineLearning%26Simulation) for implementing the Lattice Boltzmann method in 2D.

The lattice Boltzmann method (LBM) is a computational fluid dynamics technique that divides the fluid into a lattice of cells. It uses distribution functions to represent the probability of particle velocities at each cell. The method involves two steps: collision, where particles exchange momentum and energy, and streaming, where particles move to neighboring cells based on their velocities. LBM is parallelizable, efficient, and widely used for simulating fluid flow in various applications.

```math
f_i(\mathbf{x}, t) = f_i(\mathbf{x}, t) - \frac{1}{\tau} \left(f_i(\mathbf{x}, t) - f_i^{eq}(\mathbf{x}, t)\right)
```

The collision step is represented as: $f_i(\mathbf{x}, t) = f_i(\mathbf{x}, t) - \frac{1}{\tau} \left(f_i(\mathbf{x}, t) - f_i^{eq}(\mathbf{x}, t)\right)$
