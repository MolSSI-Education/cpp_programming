---
title: "Group Project Assignment"
teaching: 0
exercises: 60
questions:
- "What are the potential projects for the final presentation?"
objectives:
- "Read the potential project ideas."
keypoints:
- ""
---

## Final Project Description

For the final project, you will choose one of the following two projects to investigate:

### Code Performance Assessment
For this project, you should do a systematic assessment of the performance of the three versions of your Monte Carlo code. You can investigate code speed for increasing number of steps and constant number of particles, or for increasing number  of particles with a constant number of steps. Once you have these timings, you should create a plot or plots which shows your average timing results. Which version is fastest? Are there differences in the way each version scales? What could be the cause for these differences? When you do this assessment, you should also check that all versions of the code give consistent values for energy.

#### Considerations: 
This will involve running 3 versions of code for 3 conditions each, with each data point being an average of 3 trials (27 simulation runs!). You might find it useful to construct a bash script to run your simulations and timings. Comparisons between each version of code should be investigated on the same computer, but to look at performance for varying number of steps or particles, you can use different computers if necessary. You can divide this in your group so that each person runs benchmarks on a different version of the software.

### Prediction of Chemical Properties using Monte Carlo Simulation
For this project, you should delve further into using Monte Carlo simulation to predict chemical properties. Use the [NIST webbook](https://webbook.nist.gov/chemistry/fluid/) to find a condition where Argon (or another substance of your choice for which you can find Lennard Jones parameters) is a vapor, and a second condition where it is a liquid (you should be looking for a temperature and density). Convert your chosen temperature(s) and densities to reduced units and run simulations under these conditions. Analyze the radial distribution function to determine if your simulation predicts the same phase behavior as reported by NIST. You should average the rdf calculation of several simulation frames together to get a more accurate RDF analysis.

#### Considerations: 
You will need to either use your function which generates a random initial state or your function which generates a cubic lattice initial state. For this purpose, a function which takes in a desired density might be beneficial. 
You will need to run these simulations long enough to ensure equilibration, ie the initial state has no influence on the measured properties. You can assess this by considering the energy of the system. When it has reached a steady state, you can assume it is equilibrated (this is a very crude estimate of equilibration). 

## Final Presentation

The final presentation should be 15 minutes in length. The three aspects of this course were software engineering, molecular simulation, and programming. You should address all three in your final presentation along with results from your final project.

You can use the following questions to guide your group presentation (but you should not just answer these point by point, use them as a guideline).

### Molecular Simulations
- What are Monte Carlo methods?
- Why can we use MC methods to describe molecular systems?
- What potential function did we use to describe our system? What were its characteristics? What does the Lennard Jones potential describe in general?

### Programming
- What is the difference between an interpreted and compiled language? Which is Python? Which is C++?
- What are the advantages and disadvantages of each language?
- We had three implementations of our code - a version using the python standard library, a version using NumPy, and a version written in C++. Which was your preferred implementation and why? Which seems like the best for users, and under what conditions? Was one easier for you to develop than the others? 

### Software Engineering 
- What is version control? What software did we use for version control? What are the advantages of using version control?
- What work flow/method did you use to work on a project with your teammates?
- What is your impression of working with git/GitHub? Did it make it easier or harder to collaborate for you?
- During the bootcamp, we discussed the idea of software testing. How did we test our code? What is important when writing unit tests? Do you see the benefit of having a testing suite with software?
- In addition to changing programming language in order to make the code more performant, we were careful in the way we constructed calculations in our MC loop. What were the choice(s) we made to increase performance?
