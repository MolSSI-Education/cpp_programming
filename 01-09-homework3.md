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

1. **Exploring the Acceptance Criteria** - Calculate the probability of accepting a Monte Carlo move for energies ranging from -2 to 2 for T = 0.9, T = 0.4, and T = 1.4. What is the effect of temperature on the probability of a MC move being accepted? Create a plot showing your results.
1. **Initial Configuration** - So far we have only used an initial configuration from a file. Write a function which can generate initial system configurations from a number of particles and box size (you could choose to have the user specify density instead of box size if you wished). Make sure your function includes docstrings!
    - Write one function which places the specified number of particles randomly in the box.
    - **Bonus** - Write a second function which places particles on a square lattice.

1. **Radial Distribution Function** - Often when analyzing molecular simulation functions, we will analyze the structure of molecules or atoms using something called a **radial distribution function**, or RDF. The RDF describes local structure of particles. 

For example, consider a particle at the origin. To calculate RDF, you consider a distance, r, and a distance interval, dr. You count how many particles are a distance of r + dr from the particle of interest, as illustrated in the figure:

<img src="../fig/rdf_schematic.svg" height="400">

Your homework is to calculate the radial distribution function for the final coordinates after 50,000 steps of MC simulation. You should pick another temperature to perform a second simulation and compare the two RDFs. A function for computing the RDF can be provided to you upon request, but it is a **bonus** if you write your own. You can see some instructions for writing calculating the RDF [here](http://www.physics.emory.edu/faculty/weeks/idl/gofr2.html).
