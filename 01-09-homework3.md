---
title: "Homework Assignment #3"
teaching: 0
exercises: 120
questions:
- "What is the homework for the third day?"
objectives:
- "Improve your MC simulation."
- "Use your simulation to investigate conditions not reported by NIST."
keypoints:
- "Monte Carlo methods calculate values through random sampling."
---

## Analysis - Radial Distribution Function

Often when analyzing molecular simulation functions, we will analyze the structure of molecules or atoms using something called a **radial distribution function**, or RDF. The RDF describes local structure of particles. 

For example, consider a particle at the origin. To calculate RDF, you consider a distance, r, and a distance interval, dr. You count how many particles are a distance of r + dr from the particle of interest, as illustrated in the figure:

<img src="../fig/rdf_schematic.svg" height="400">

A sample radial distribution function for a fluid is shown below. A peak indicattes a higher probability of particles being located at that distance. This graph shows that particles in a fluid will have neighboring particles (the peak), and then the RDF will flatten out to a constant value. The radial distribution function is typically normalized so that this value is equal to 1 (as shown in the plot below).

<img src="https://upload.wikimedia.org/wikipedia/commons/3/31/Lennard-Jones_Radial_Distribution_Function.svg" height="400">

Something that has more order like a lattice structure will look more like the next figure. Notice that when atoms are on a lattice (regularly spaced), you also see regularly spaced peaks in the RDF.

<img src='https://github.com/msse-2020-bootcamp/lessons/blob/gh-pages/fig/square_lattice.png' height='400'>


## Homework Tasks

1. **Exploring the Acceptance Criteria** - Calculate the probability of accepting a Monte Carlo move for energies ranging from -2 to 2 for T = 0.9, T = 0.4, and T = 1.4. What is the effect of temperature on the probability of a MC move being accepted? Create a plot showing your results. Note that you aren't going to be able to use your function `accept_or_reject` for this. You will have to take `p_acc` out of it to make the plot.
1. **Initial Configuration** - So far we have only used an initial configuration from a file. Write a function which can generate initial system configurations from a number of particles and box size (you could choose to have the user specify density instead of box size if you wished). Make sure your function includes docstrings! You will need to generate random coordinates on the range (0, box_length) to place the particles. You might want to check out other functions in the `random` library for this!

    The function should return `coordinates, box_length` the same way our `read_xyz` function did so we can switch out the two without changing our code.
        - Write one function which places the specified number of particles randomly in the box.

1. **Radial Distribution Function** 

Your homework is to calculate the radial distribution function for the final coordinates after 50,000 steps of MC simulation. You should pick another temperature to perform a second simulation and compare the two RDFs. A function for computing the RDF can be provided to you upon request, but it is a **bonus** if you write your own. You can see some instructions for writing calculating the RDF [here](http://www.physics.emory.edu/faculty/weeks/idl/gofr2.html).

## Bonus
**Square Lattice**
Write a function called `square_lattice` which places a specified number of particles on a square lattice. The function should take a number of particles and a box length.
- Turn your code for a bonus using [this link](https://classroom.github.com/a/ZvOfBPS_). The due date for a bonus is in one week, August 20, 2020.

