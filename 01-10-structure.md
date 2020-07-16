---
title: "Separating Code into Modules"
teaching: 0
exercises: 45
questions:
- "How should I organize my code?"
objectives:
- "Separate code into related modules."
keypoints:
- "When working on a large project, you should organize your code into modules."
---

As new features are implemented in codes, it is natural for new functions and objects to be added. In many projects, this often leads to a large number of functionalities defined within a single module. For small, single developer codes, this is not a major issue, but it can still make it difficult to work with. With large or multi-developer codes, this can slow development progress to a crawl as it is difficult to both understand and work with the code.

In this lesson, we will break our Monte Carlo code into modules. Our project is not very large, so we wouldn't necessarily need to do this, but it will help us keep our code cleaner and easier to understand. 

We will move from the Jupyter notebook to our text editor for this lesson. We will be dividing our code into different `.py` files based on what the functions do.

The easiest way to start is to see what we currently have and try and decide what is related to one another. Looking through our code, we see a number of different functions. We list them here:

1. `calculate_LJ` - written in class
1. `calculate_distance` - periodic boundary version.
1. `calculate_total_energy` - version with cutoff
1. `read_xyz` - provided in class
1. `accept_or_reject` - written in class
1. `adjust_displacement` - homework problem
1. `calculate_pair_energy` - written in class
1. `generate_random_coordinates` - written for homework
1. `generate_lattice_coordinates` - **bonus**, written for homework
1. `radial_distribution_function` - provided or written for homework.

Looking at these functions initially, we can see a set that seems to belong together. These functions have to do with atomic coordinates:

1. `read_xyz`
1. `generate_random_coordinates`
1. `generate_lattice_coordinates`
1. `calculate_distance`

We will put these into their own python modules called `coord.py`. 

Before we do that, we will create a directory to hold our python modules. In the top level of your project directory, make a new directory called `mcsim`. In the `mcsim` directory, create a file called `coord.py`, and add the listed functions to the file:

~~~
"""
Functions related to coordinates.
"""

import math

def generate_random_coordinates(num_atoms, density):
    ** YOUR CODE HERE **

def generate_lattice_coordinates(num_atoms, density):
    ** YOUR CODE HERE **


def read_xyz(filepath):
    """
    Reads coordinates from an xyz file.
    
    Parameters
    ----------
    filepath : str
       The path to the xyz file to be processed.
       
    Returns
    -------
    atomic_coordinates : list
        A two dimensional list containing atomic coordinates
    """
    
    with open(filepath) as f:
        num_atoms = float(f.readline())
        box_length = float(f.readline().split()[0])
        coordinates = f.readlines()
    
    atomic_coordinates = []
    
    for atom in coordinates:
        split_atoms = atom.split()
        
        float_coords = []
        
        # We split this way to get rid of the atom label.
        for coord in split_atoms[1:]:
            float_coords.append(float(coord))
            
        atomic_coordinates.append(float_coords)
        
    
    return atomic_coordinates, box_length

def generate_random_coordinates(num_atoms, density):
    """
    Generate random coordinates in a box.

    Parameters
    ----------
    num_atoms : int
        The number of atoms to place
    density : float
        The target system density

    Returns
    -------
    coordinates : list
        The generated coordinates
    box_length : float
        The box length for the number of atoms and desired density.
    """

    box_length = math.pow(num_atoms/density, (1/3))
    coordinates = []

    for i in range(num_atoms):
        x_val = random.uniform(0, p)
        y_val = random.uniform(0, p)
        z_val = random.uniform(0, p)
        coordinates.append([x_val, y_val, z_val])
    
    return coordinates, box_length
    

def calculate_distance(coord1, coord2, box_length=None):
    """
    Calculate the distance between two 3D coordinates.
    
    Parameters
    ----------
    coord1, coord2: list
        The atomic coordinates
    
    Returns
    -------
    distance: float
        The distance between the two points.
    """
    
    distance = 0
    for i in range(3):
        dim_dist = (coord1[i] - coord2[i]) 
        if box_length:
            dim_dist = dim_dist - box_length * round(dim_dist / box_length)
        
        dim_dist = dim_dist**2
        distance += dim_dist
    
    distance = math.sqrt(distance)
    return distance
~~~
{: .language-python}

Notice two things about this file - we have added a docstring at the top of the module explaining the module's purpose, and we have added our import of the `math` package here. We must add appropriate imports for the functions, or we will not be able to use them properly when we import our modules.

Also notice now that since we've added these functions to a module, we can now import that modules in a Jupyter notebook or in the interactive Python interpreter. From the top level of your directory, type 

~~~
$ python
~~~
{: .language-bash}

To open an interactive python interpreter. Once you are in the interpreter, import your newly created module:

~~~
>>> import mcsim.coord
~~~
{: .language-python}

We can see the docstrings we've written for functions by using the python `help` function:

~~~
>>> help(mcsim.coord.calculate_distance)
~~~
{: .language-python}

This will display the docstring for our function:

~~~
Help on function calculate_distance in module mcsim.coord:

calculate_distance(coord1, coord2, box_length=None)
    Calculate the distance between two 3D coordinates.

    Parameters
    ----------
    coord1, coord2: list
        The atomic coordinates

    Returns
    -------
    distance: float
        The distance between the two points.
(END)
~~~
{: .output}

Press the `q` button to exit.

Next, we'll group together functions that have to do with energy calculations:

1. `calculate_LJ`
1. `calculate_tail_correction`
1. `calculate_total_pair_energy`
1. `calculate_pair_energy`

After we group these functions together, we should think about our imports. Just like the previous module, we will need to import the `math` module. 

You'll also notice that since `calculate_distance` is no longer in the same module, we will need to import as well. This time, we will be using a *relative import** since the module we are importing is in the same directory as our current module.

A relative import can look like this:

~~~
from . import module_name
~~~
{: .language-python}

Here, our import says to import a module with name `module_name` from `.`, or the current directory. For our imports, we will add 

~~~
from . import coord
~~~
{: .language-python}

Then, we use the calculate distance function by prepending coord:

~~~
coord.calculate_distance
~~~
{: .language-python}

Your final module will look like:

~~~
"""
Functions related to energy calculation.
"""

import math

from . import coord

def calculate_LJ(r_ij):
    """
    Calculate the Lennard Jones energy based on a separation distance.
    
    Parameters
    ----------
    r_ij : float
        The distance between the two particles.
        
    Returns
    -------
    pairwise_energy : float
        The Lennard Jones potential energy.
    """
    
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy

def calculate_tail_correction(num_particles, box_length, cutoff):
    """
    Calculate the long range tail correction
    """
    
    const1 = (8 * math.pi * num_particles ** 2) / (3 * box_length ** 3)
    const2 = (1/3) * (1 / cutoff)**9 - (1 / cutoff) **3
    
    return const1 * const2

def calculate_total_pair_energy(coordinates, box_length, cutoff):
    total_energy = 0
    num_atoms = len(coordinates)
    cutoff = 3

    for i in range(num_atoms):
        for j in range(i+1, num_atoms):
            # Calculate the distance between the particles - exercise.
            dist_ij = coord.calculate_distance(coordinates[i], coordinates[j], box_length=box_length)

            if dist_ij < cutoff:
                # Calculate the pairwise LJ energy
                LJ_ij = calculate_LJ(dist_ij)

                # Add to total energy.
                total_energy += LJ_ij
    return total_energy

def calculate_pair_energy(coordinates, i_particle, box_length, cutoff):
    """
    Calculate the interaction energy of a particle with its environment (all other particles in the system)
    
    Parameters
    ----------
    coordinates : list
        The coordinates for all particles in the system
        
    i_particle : int
        The particle number for which to calculate the energy
        
    cutoff : float
        The simulation cutoff. Beyond this distance, interactions are not calculated.
    
    Returns
    -------
    e_total : float 
        The pairwise interaction energy of he i_th particle with all other particles in the system.
    """
    
    e_total = 0.0
    i_position = coordinates[i_particle]
 
    num_atoms = len(coordinates)

    for j_particle in range(num_atoms):
        if i_particle != j_particle:

            j_position = coordinates[j_particle]
            rij = coord.calculate_distance(i_position, j_position, box_length)

            if rij < cutoff:
                e_pair = calculate_LJ(rij)
                e_total += e_pair

    return e_total
~~~
{: .language-python}

Finally, we create a module called `monte_carlo.py` which has functions related to Monte Carlo simulation. We put our loop for running MC into a function called `run_mc`

~~~
"""
Functions related to the Monte Carlo Loop
"""

import math
import random
from copy import deepcopy

from mcsim import coord
from mcsim import energy

def accept_or_reject(delta_e, beta):
    if delta_e <= 0.0:
        accept = True
    else:
        random_number = random.random()
        p_acc = math.exp(-beta*delta_e)
        if random_number < p_acc:
            accept = True
        else:
            accept = False

    return accept

def adjust_displacement(acc_rate, max_displacement):
    """
    Adjust the max displacement to achieve ideal acceptance rate.
    """

    if (acc_rate < 0.380):
        max_displacement *= 0.8
    elif (acc_rate > 0.42):
        max_displacement *= 1.2

    return max_displacement

def run_mc(coordinates, box_length, reduced_temperature, num_steps, max_displacement, cutoff, freq):
    """ 
    Run MC Simulation with given input parameters.

    Parameters
    ----------
    initial_coordinates : list
        The starting coordinates for the system.
    reduced_tempearture : float
        The reduced temperature of the simulatin
    num_steps : int
        The number of Monte Carlo steps to attempt.
    max_displacemt : float
        The maximum particle displacement for a Monte Carlo move.
    cutoff : float
        The distance cutoff for energy calculation
    """

    print(F"the number of steps is {num_steps}")

    # Calculated quantities
    beta = 1 / reduced_temperature

    num_particles = len(coordinates)

    # Make a copy of the coordinates so we keep the original
    initial_coordinates = deepcopy(coordinates)
    delta_energy = 0

    total_energy = energy.calculate_total_pair_energy(coordinates, box_length, cutoff)
    total_energy += energy.calculate_tail_correction(num_particles, box_length, cutoff)

    n_accept = 0
    freq = 1000

    steps = []
    energies = []

    for step in range(num_steps):
        
        # Randomly pick one of N particles
        random_particle = random.randrange(num_particles)
        
        # Calculate interaction energy of selected particle with the system
        current_energy = energy.calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # Generate random x, y, z displacement
        x_rand = random.uniform(-max_displacement, max_displacement)
        y_rand = random.uniform(-max_displacement, max_displacement)
        z_rand = random.uniform(-max_displacement, max_displacement)
        
        
        # Modify coordinate of Nth particle by generated displacements
        coordinates[random_particle][0] += x_rand
        coordinates[random_particle][1] += y_rand
        coordinates[random_particle][2] += z_rand
        
        proposed_energy = energy.calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # Calculate change in potential energy for particle move.
        delta_energy = proposed_energy - current_energy

        accept = accept_or_reject(delta_energy, beta)
        
        if accept:
            total_energy += delta_energy
            n_accept += 1
        else:
            # Move is not accepted, roll back coordinates
            coordinates[random_particle][0] -= x_rand
            coordinates[random_particle][1] -= y_rand
            coordinates[random_particle][2] -= z_rand
        
        if step % freq == 0:
            print(step, total_energy/num_particles)
            steps.append(step)
            energies.append(total_energy/num_particles)

    return coordinates
~~~
{: .language-python}

Finally, we'll add a script in the top level that runs our simulation called `run_mc.py`.

~~~
import mcsim.monte_carlo as mc

from mcsim.coord import read_xyz

coordinates, box_length = read_xyz("data/sample_config1.xyz")

# Simulation parameters
reduced_temperature = 0.9
num_steps = 5000
max_dispacement = 0.1
cutoff = 3
freq = 1000


mc.run_mc(coordinates, reduced_temperature=reduced_temperature,
num_steps=num_steps, box_length=box_length, max_displacement=max_dispacement,
cutoff=cutoff, freq=freq)
~~~
{: .language-python}

You can now run your Monte Carlo simulation by changing the input parameters in `run_mc.py` and executing

~~~
python run_mc.py
~~~
{: .language-bash}

in your terminal.