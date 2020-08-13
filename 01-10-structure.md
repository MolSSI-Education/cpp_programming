---
title: "Separating Code into Modules"
teaching: 0
exercises: 45
start: true
questions:
- "How should I organize my code?"
objectives:
- "Separate code into related modules."
keypoints:
- "When working on a large project, you should organize your code into modules."
---

# Moving Code into a module

For the next section, we will move our code from the Jupyter notebook to a module. Our ultimate goal will be to make a small python package which contains our Monte Carlo code. We will be able to import that package into the Jupyter notebook to more cleanly run our simulations. 

Create a folder called `mcsim` in the top level of your project directory.

~~~
$ mkdir mcsim
~~~
{: .language-bash}

In that folder, open a file called `monte_carlo.py`. Gather all of your functions from your Jupyter notebook and paste them into the python module you created (`monte_carlo.py`). In a Python module, all import statements should be at the top of the module, followed by function definitions. When you are done, your file might look something like the following:

~~~
"""
Functions for running a Monte Carlo Simulation
"""

import math
import random
import os

def calculate_LJ(r_ij):
    """
    The LJ interaction energy between two particles.
    
    Computes the pairwise Lennard Jones interaction energy based on the separation distance in reduced units.
    
    Parameters
    ----------
    r_ij : float
        The distance between the particles in reduced units.
        
    Returns
    -------
    pairwise_energy : float
        The pairwise Lennard Jones interaction energy in reduced units.

    """
    
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy

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

def calculate_tail_correction(num_particles, box_length, cutoff):
    """
    Calculate the long range tail correction
    """
    
    const1 = (8 * math.pi * num_particles ** 2) / (3 * box_length ** 3)
    const2 = (1/3) * (1 / cutoff)**9 - (1 / cutoff) **3
    
    return const1 * const2

def calculate_total_energy(coordinates, box_length, cutoff):
    """
    Calculate the total Lennard Jones energy of a system of particles.
    
    Parameters
    ----------
    coordinates : list
        Nested list containing particle coordinates.
    
    Returns
    -------
    total_energy : float
        The total pairwise Lennard Jones energy of the system of particles.
    """
    
    total_energy = 0
    
    num_atoms = len(coordinates)
    
    for i in range(num_atoms):
        for j in range(i+1, num_atoms):
            
            #print(f'Comparings atom number {i} with atom number {j}')
            
            dist_ij = calculate_distance(coordinates[i], coordinates[j], box_length=box_length)
            
            if dist_ij < cutoff:
                interaction_energy = calculate_LJ(dist_ij)
                total_energy += interaction_energy
            
    return total_energy
    
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
        box_length = float(f.readline().split()[0])
        num_atoms = float(f.readline())
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

def accept_or_reject(delta_e, beta):
    """
    Accept or reject based on change in energy and temperature.
    """
    
    if delta_e <= 0:
        accept = True
    else:
        random_number = random.random()
        p_acc = math.exp(-beta*delta_e)
        
        if random_number < p_acc:
            accept = True
        else:
            accept = False
    
    return accept

def calculate_pair_energy(coordinates, i_particle, box_length, cutoff):
    """
    Calculate the interaction energy of a particle with its environment (all other particles in the system)
    
    
    Parameters
    ----------
    coordinates : list
        The coordinates for all the particles in the system.
        
    i_particle : int
        The particle index for which to calculate the energy.
    
    box_length : float
        The length of the simulation box.
    
    cutoff : float
        The simulation cutoff. Beyond this distance, interactions are not calculated
    
    Returns
    -------
    e_total : float
        The pairwise interaction energy of the i-th particle with all other particles in the system.
    
    """
    
    e_total = 0
    
    i_position = coordinates[i_particle]
    
    num_atoms = len(coordinates)
    
    for j_particle in range(num_atoms):
        if i_particle != j_particle:
            j_position = coordinates[j_particle]
            rij = calculate_distance(i_position, j_position, box_length)

            if rij < cutoff:
                e_pair = calculate_LJ(rij)
                e_total += e_pair
    
    return e_total

# Set simulation parameters
reduced_temperature = 1.5
num_steps = 50000
max_displacement = 0.1 
cutoff = 3

# Reporting information
freq = 1000
steps = []
energies = []
all_coordinates = []

# Calculated quantities
beta = 1/reduced_temperature

# Read initial coordinates
file_path = os.path.join('..','lj_sample_configurations', 'lj_sample_config_periodic1.txt')
coordinates, box_length = read_xyz(file_path)
num_particles = len(coordinates)

# Calculated based on simulation inputs
total_energy = calculate_total_energy(coordinates, box_length, cutoff)
total_energy += calculate_tail_correction(num_particles, box_length, cutoff)

for step in range(num_steps):
    
    # 1. Randomly pick one of num_particles particles
    random_particle = random.randrange(num_particles)
    
    # 2. Calculate the interaction energy of the selected particle with the system. Store this value.
    current_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    
    # 3. Generate a random x, y, z displacement range (-max_displacement, max_displacement) - uniform distribution
    x_rand = random.uniform(-max_displacement, max_displacement)
    y_rand = random.uniform(-max_displacement, max_displacement)
    z_rand = random.uniform(-max_displacement, max_displacement)
    
    # 4. Modify the coordinate of selected particle by generated displacements.
    coordinates[random_particle][0] += x_rand
    coordinates[random_particle][1] += y_rand
    coordinates[random_particle][2] += z_rand
    
    # 5. Calculate the new interaction energy of moved particle, store this value.
    proposed_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    
    # 6. Calculate energy change and decide if we accept the move.
    delta_energy = proposed_energy - current_energy
    
    accept = accept_or_reject(delta_energy, beta)
    
    # 7. If accept, keep movement. If not revert to old position.
    if accept:
        total_energy += delta_energy
    else:
        # Move is not accepted, roll back coordinates
        coordinates[random_particle][0] -= x_rand
        coordinates[random_particle][1] -= y_rand
        coordinates[random_particle][2] -= z_rand
    
    # 8. Print the energy and store the coordinates at certain intervals
    if step % freq == 0:
        print(step, total_energy/num_particles)
        steps.append(step)
        energies.append(total_energy/num_particles)
        all_coordinates.append(coordinates)
~~~
{: .language-python}

Note that we have moved all the imports to the top and also changed the location of our file path. If you have moved everything without problems, you should be able to run the simulation by typing the following command from your `mcsim` folder

~~~
$ python monte_carlo.py
~~~
{: .language-bash}

This seems great! But, we want to make our simulation code importable from another file like a Jupyter notebook. Let's make our simulation run into a function. For this function, we want to consider what are input simulation parameters. In other words, if we want to run several simulations, what would we change? We will want to make these our function arguments. 

~~~
def run_simulation(coordinates, box_length, cutoff, reduced_temperature, num_steps, max_displacement, freq=1000):
    """
    Run a Monte Carlo simulation with the specified parameters. 
    """

    # Reporting information
    steps = []
    energies = []
    all_coordinates = []

    # Calculated quantities
    beta = 1/reduced_temperature
    num_particles = len(coordinates)

    # Calculated based on simulation inputs
    total_energy = calculate_total_energy(coordinates, box_length, cutoff)
    total_energy += calculate_tail_correction(num_particles, box_length, cutoff)


    for step in range(num_steps):
        
        # 1. Randomly pick one of num_particles particles
        random_particle = random.randrange(num_particles)
        
        # 2. Calculate the interaction energy of the selected particle with the system. Store this value.
        current_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 3. Generate a random x, y, z displacement range (-max_displacement, max_displacement) - uniform distribution
        x_rand = random.uniform(-max_displacement, max_displacement)
        y_rand = random.uniform(-max_displacement, max_displacement)
        z_rand = random.uniform(-max_displacement, max_displacement)
        
        # 4. Modify the coordinate of selected particle by generated displacements.
        coordinates[random_particle][0] += x_rand
        coordinates[random_particle][1] += y_rand
        coordinates[random_particle][2] += z_rand
        
        # 5. Calculate the new interaction energy of moved particle, store this value.
        proposed_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 6. Calculate energy change and decide if we accept the move.
        delta_energy = proposed_energy - current_energy
        
        accept = accept_or_reject(delta_energy, beta)
        
        # 7. If accept, keep movement. If not revert to old position.
        if accept:
            total_energy += delta_energy
        else:
            # Move is not accepted, roll back coordinates
            coordinates[random_particle][0] -= x_rand
            coordinates[random_particle][1] -= y_rand
            coordinates[random_particle][2] -= z_rand
        
        # 8. Print the energy and store the coordinates at certain intervals
        if step % freq == 0:
            print(step, total_energy/num_particles)
            steps.append(step)
            energies.append(total_energy/num_particles)
            all_coordinates.append(coordinates)
~~~
{: .language-python}

Now we can try this out in a Jupyter notebook. The code in our `mcsim` folder now should only be functions, we will write our script in a Jupyter notebook in the top level of our directory.

In a Jupyter notebook, you can now run your code

~~~
import os

import mcsim.monte_carlo

help(mcsim.monte_carlo.run_simulation)
~~~
{: .language-python}

~~~
# Set simulation parameters
reduced_temperature = 1.5
num_steps = 50000
max_displacement = 0.1 
cutoff = 3
freq = 1000

# Read initial coordinates
file_path = os.path.join('lj_sample_configurations', 'lj_sample_config_periodic1.txt')
coordinates, box_length = mcsim.monte_carlo.read_xyz(file_path)
~~~
{: .language-python}

~~~
mcsim.monte_carlo.run_simulation(coordinates, box_length, reduced_temperature, cutoff, num_steps, max_displacement)
~~~
{: .language-python}

## Grouping Code 

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
from .module_name import function_name
~~~
{: .language-python}

Here, our import says to import a function named `function_name` from module with name `module_name` from `.`, or the current directory. For our imports, we will add 

~~~
from .coordinates import calculate_distance
~~~
{: .language-python}

~~~
import math
import random

from .coordinates import calculate_distance

def calculate_LJ(r_ij):
    """
    The LJ interaction energy between two particles.
    
    Computes the pairwise Lennard Jones interaction energy based on the separation distance in reduced units.
    
    Parameters
    ----------
    r_ij : float
        The distance between the particles in reduced units.
        
    Returns
    -------
    pairwise_energy : float
        The pairwise Lennard Jones interaction energy in reduced units.

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

def calculate_total_energy(coordinates, box_length, cutoff):
    """
    Calculate the total Lennard Jones energy of a system of particles.
    
    Parameters
    ----------
    coordinates : list
        Nested list containing particle coordinates.
    
    Returns
    -------
    total_energy : float
        The total pairwise Lennard Jones energy of the system of particles.
    """
    
    total_energy = 0
    
    num_atoms = len(coordinates)
    
    for i in range(num_atoms):
        for j in range(i+1, num_atoms):
            
            #print(f'Comparings atom number {i} with atom number {j}')
            
            dist_ij = calculate_distance(coordinates[i], coordinates[j], box_length=box_length)
            
            if dist_ij < cutoff:
                interaction_energy = calculate_LJ(dist_ij)
                total_energy += interaction_energy
            
    return total_energy

def accept_or_reject(delta_e, beta):
    """
    Accept or reject based on change in energy and temperature.
    """
    
    if delta_e <= 0:
        accept = True
    else:
        random_number = random.random()
        p_acc = math.exp(-beta*delta_e)
        
        if random_number < p_acc:
            accept = True
        else:
            accept = False
    
    return accept

def calculate_pair_energy(coordinates, i_particle, box_length, cutoff):
    """
    Calculate the interaction energy of a particle with its environment (all other particles in the system)
    
    
    Parameters
    ----------
    coordinates : list
        The coordinates for all the particles in the system.
        
    i_particle : int
        The particle index for which to calculate the energy.
    
    box_length : float
        The length of the simulation box.
    
    cutoff : float
        The simulation cutoff. Beyond this distance, interactions are not calculated
    
    Returns
    -------
    e_total : float
        The pairwise interaction energy of the i-th particle with all other particles in the system.
    
    """
    
    e_total = 0
    
    i_position = coordinates[i_particle]
    
    num_atoms = len(coordinates)
    
    for j_particle in range(num_atoms):
        if i_particle != j_particle:
            j_position = coordinates[j_particle]
            rij = calculate_distance(i_position, j_position, box_length)

            if rij < cutoff:
                e_pair = calculate_LJ(rij)
                e_total += e_pair
    
    return e_total
~~~
{: .language-python}

Finally, we edit our module `monte_carlo.py` which has functions related to Monte Carlo simulation. We put our loop for running MC into a function called `run_mc`

~~~
"""
Functions for running a Monte Carlo Simulation
"""

import math
import random
import os

from .energy import *
from .coordinates import *

def run_simulation(coordinates, box_length, cutoff, reduced_temperature, num_steps, max_displacement, freq=1000):
    """
    Run a Monte Carlo simulation with the specified parameters. 
    """

    # Reporting information
    steps = []
    energies = []
    all_coordinates = []

    # Calculated quantities
    beta = 1/reduced_temperature
    num_particles = len(coordinates)

    # Calculated based on simulation inputs
    total_energy = calculate_total_energy(coordinates, box_length, cutoff)
    total_energy += calculate_tail_correction(num_particles, box_length, cutoff)


    for step in range(num_steps):
        
        # 1. Randomly pick one of num_particles particles
        random_particle = random.randrange(num_particles)
        
        # 2. Calculate the interaction energy of the selected particle with the system. Store this value.
        current_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 3. Generate a random x, y, z displacement range (-max_displacement, max_displacement) - uniform distribution
        x_rand = random.uniform(-max_displacement, max_displacement)
        y_rand = random.uniform(-max_displacement, max_displacement)
        z_rand = random.uniform(-max_displacement, max_displacement)
        
        # 4. Modify the coordinate of selected particle by generated displacements.
        coordinates[random_particle][0] += x_rand
        coordinates[random_particle][1] += y_rand
        coordinates[random_particle][2] += z_rand
        
        # 5. Calculate the new interaction energy of moved particle, store this value.
        proposed_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 6. Calculate energy change and decide if we accept the move.
        delta_energy = proposed_energy - current_energy
        
        accept = accept_or_reject(delta_energy, beta)
        
        # 7. If accept, keep movement. If not revert to old position.
        if accept:
            total_energy += delta_energy
        else:
            # Move is not accepted, roll back coordinates
            coordinates[random_particle][0] -= x_rand
            coordinates[random_particle][1] -= y_rand
            coordinates[random_particle][2] -= z_rand
        
        # 8. Print the energy and store the coordinates at certain intervals
        if step % freq == 0:
            print(step, total_energy/num_particles)
            steps.append(step)
            energies.append(total_energy/num_particles)
            all_coordinates.append(coordinates)
~~~
{: .language-python}

You can now run your simulation in a Jupyter notebook as we did before we separated into modules.
