---
title: "Monte Carlo for a Lennard Jones Fluid - Part 2"
teaching: 100
exercises: 20
questions:
- "How do I write a Monte Carlo simulation?"
objectives:
- "Use the Metropolis algorithm to sample configurations of LJ particles"
keypoints:
- "Thermodynamic properties of a system can be thought of as a very high dimensional integral."
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

# Preparing for our Monte Carlo Simulation

Move functions from yesterday into module. Import into today's notebook.

# Writing the Monte Carlo Loop

Just like with our first Monte Carlo examples, we will be relying on random numbers to sample our configuration space. This translates into generating possible coordinates for our system. However, due to because our high dimensional integral is dominated by a relatively small region of configurational space, it would be very inefficient just pick random numbers. To overcome this problem, we will use the ideas of **importance sampling** and **detailed balance**.

## Importance Sampling

A solution to this problem is to sample positions from the desired equilibrium probability density. This will generate relevant configurations more frequently than configurations that have low probability. This idea is known as **importance sampling**.

## Detailed Balance

Basically, the condition of detailed balance tells us that the "flux" of transitions from state `m` to state `n` equals the flux from state `n` to state `m`.

Metropolis criteria for accepting movements.

## Flow of Calculations

The following workflow can be used to implement the Metropolis algorithm to sample the canonical ensemble of configurations of LJ particles:

1. Generate an initial system state $$m$$.
1. Choose an atom with uniform probability from $$\{1, \ldots, N\}$$ from old state $$m$$.
1. Propose a new state $$n$$ by translating a LJ particle by a uniform random displacement $$\Delta r \sim U(-\Delta x, +\Delta x)$$ in each dimension.
	- The displacement scale $$\Delta x$$ should not be too large as this would
	likely result in particle overlaps, but should not be too small
	as this would result in a slow sampling of configurational space.
1. The difference in energy between the new and old states is computed.
1. The new state is accepted or rejected using the Metropolis criterion.Practically, this can be implemented as follows. 
	- If a move from $$m$$ to $$n$$ is "downhill", $$\beta \Delta U \leq 0$$,
	the move is always accepted. For "uphill" moves, a random number $$\zeta$$ is generated uniformly on (0,1).  
	- If $$\zeta < \exp[-\beta {\Delta U}]$$, the move is accepted.  Otherwise, the move is rejected.

~~~
# Acceptance criteria - importance sampling and detailed balance

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
~~~
{: .language-python}

### Sanity check

1. `True` should be returned for a negative `delta_e`
2. `True` should be returned for `delta_e == 0`
3. For `delta_e`> 0 the function relies on a random number generator. In order to get a predictable value from the random number generator, you should set the seed using `random.seed(value)` where value corresponds to a random number seed. This will make the call to `random.random()` return the same number every time. 
    - Find a random seed which will result in a `True` being returned from the function, and one that will result in a `False` being returned from the funciton. Use these for tests

~~~
delta_energy = -1
beta = 1/0.9
assert accept_or_reject(delta_energy, beta)

delta_energy = 0
beta = 1/0.9
assert accept_or_reject(delta_energy, beta)
~~~
{: .language-python}

#### Finding appropriate random seeds

Consider what types of numbers we need for this sanity check. From the mathematical equation for acceptance, what should we be looking for?

~~~
import random

random.seed(1)
random.random()
~~~
{: .language-python}

~~~
0.13436424411240122
~~~
{: .output}

~~~
random.seed(0)
random.random()
~~~
{: .language-python}

~~~
0.8444218515250481
~~~
{: .output}

~~~
random.seed(0)
delta_energy = 1
beta = 1/0.9
assert accept_or_reject(delta_energy, beta) is False
~~~
{: .language-python}

~~~
random.seed(1)
delta_energy = 1
beta = 1/0.9
assert accept_or_reject(delta_energy, beta)
~~~
{: .language-python}

## Energy Calculation

In our Monte Carlo loop, we will be calculating the change in energy from a particle movement to decide whether to accept or reject that movement. Previously, we calculated the total pairwise energy for particles. However, when we move a particle, we only need to calculate the energy change for that one particle. We will write a new function for calculating the pairwise interaction energy of a single particle.

~~~
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
            rij = calculate_distance(i_position, j_position, box_length)

            if rij < cutoff:
                e_pair = calculate_LJ(rij)
                e_total += e_pair

    return e_total
~~~
{: .language-python}

### Sanity Checks
~~~
coordinates = [[0, 0, 0], [0, 0, 2**(1/6)], [0, 0, 2*(2**(1/6))]]

assert calculate_pair_energy(coordinates, 1, 10, 3) == -2

assert calculate_pair_energy(coordinates, 0, 10, 3) == calculate_pair_energy(coordinates, 2, 10, 3)

assert calculate_pair_energy(coordinates, 0, 10, 2) == -1
~~~
{: .language-python}

## Simulation
~~~
import time

start = time.time()
# Parameters
reduced_temperature = 0.9
num_steps = 5000
max_displacement = 0.1
cutoff = 3.0

# Calculated quantities
beta = 1 / reduced_temperature

# Read initial coordinates
coordinates, box_length = read_xyz('sample_config1.xyz')
num_particles = len(coordinates)

# Make a copy of the coordinates so we keep the original
initial_coordinates = deepcopy(coordinates)
delta_energy = 0

total_energy = calculate_total_pair_energy(coordinates, box_length, cutoff)
total_energy += calculate_tail_correction(num_particles, box_length, cutoff)

freq = 1000

for step in range(num_steps):
    
    # Randomly pick one of N particles
    random_particle = random.randrange(num_particles)
    
    # Generate random x, y, z displacement
    x_rand = random.uniform(-max_displacement, max_displacement)
    y_rand = random.uniform(-max_displacement, max_displacement)
    z_rand = random.uniform(-max_displacement, max_displacement)
    
    
    # Modify coordinate of Nth particle by generated displacements
    new_coordinates = deepcopy(coordinates)
    
    new_coordinates[random_particle][0] += x_rand
    new_coordinates[random_particle][1] += y_rand
    new_coordinates[random_particle][2] += z_rand
    
    old_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    new_energy = calculate_pair_energy(new_coordinates, random_particle, box_length, cutoff)

        
    # Calculate change in potential energy for particle move.
    delta_energy = new_energy - old_energy

    accept = accept_or_reject(delta_energy, beta)
    
    if accept:
        coordinates = new_coordinates
        total_energy += delta_energy

    else:
        # Move is not accepted, do not update coordinates or energy.
        pass
    
    if step % freq == 0:
        
        print(step, total_energy/num_particles, max_displacement)

end = time.time()
~~~
{: .language-python}

## Visualization
Add before loop.
~~~
import matplotlib.pyplot as plt

%matplotlib notebook

fig = plt.figure()
ax = fig.add_subplot(111)
~~~
{: .language-python}

We can plot the calculated energies during the MC loop.

~~~
import time

start = time.time()
# Parameters
reduced_temperature = 0.9
num_steps = 5000
max_displacement = 0.1
cutoff = 3.0

# Calculated quantities
beta = 1 / reduced_temperature

# Read initial coordinates
coordinates, box_length = read_xyz('sample_config1.xyz')
num_particles = len(coordinates)

# Make a copy of the coordinates so we keep the original
initial_coordinates = deepcopy(coordinates)
delta_energy = 0

total_energy = calculate_total_pair_energy(coordinates, box_length, cutoff)
total_energy += calculate_tail_correction(num_particles, box_length, cutoff)

freq = 1000

for step in range(num_steps):
    
    # Randomly pick one of N particles
    random_particle = random.randrange(num_particles)
    
    # Generate random x, y, z displacement
    x_rand = random.uniform(-max_displacement, max_displacement)
    y_rand = random.uniform(-max_displacement, max_displacement)
    z_rand = random.uniform(-max_displacement, max_displacement)
    
    
    # Modify coordinate of Nth particle by generated displacements
    new_coordinates = deepcopy(coordinates)
    
    new_coordinates[random_particle][0] += x_rand
    new_coordinates[random_particle][1] += y_rand
    new_coordinates[random_particle][2] += z_rand
    
    old_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    new_energy = calculate_pair_energy(new_coordinates, random_particle, box_length, cutoff)

        
    # Calculate change in potential energy for particle move.
    delta_energy = new_energy - old_energy

    accept = accept_or_reject(delta_energy, beta)
    
    if accept:
        coordinates = new_coordinates
        total_energy += delta_energy

    else:
        # Move is not accepted, do not update coordinates or energy.
        pass
    
    if step % freq == 0:
        
        print(step, total_energy/num_particles, max_displacement)
        ax.plot(step, total_energy/num_particles, 'og')
        fig.canvas.draw()

end = time.time()
~~~
{: .language-python}

## Tuning Displacement
Potential homework


