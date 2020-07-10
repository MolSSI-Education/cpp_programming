---
title: "Monte Carlo for a Lennard Jones Fluid - Part 1"
teaching: 80
exercises: 20
questions:
- "How can Monte Carlo be applied to a thermodynamic problem?"
- "How do we model the interaction of nonbonded atoms?"
objectives:
- "Understand the Lennard Jones potential."
- "Understand periodic boundary conditions."
keypoints:
- "Thermodynamic properties of a system can be thought of as a very high dimensional integral."
- "We can use MC methods for this, but the calculation is more complicated due to the high dimensionality of the problem."
- "We model pairwise atomic interactions using the Lennard Jones potential."
- "When simulating molecular systems, we often use periodic boundary conditions to simulate an infinite system."
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

This module is based on work by MolSSI Software Scientist Eliseo Marin-Rimoldi and Prof. John D. Chodera.

## Motivation - Using Monte Carlo on molecular systems

We are going to use Monte Carlo simulation to solve a chemical problem. MC simulation is used in a lot of different fields, and one of those is molecular simulation. MC methods for molecules can be quite advanced these days (link to something), but for this bootcamp, we will be using MC to simulate noble gases.

In statistical mechanics, we are interested in computing averages of thermodynamic properties as a function of atom positions an momenta. A thermodynamic average depending only on configurational properties can be computed by evaluating a multivariate integral.

$$ \left<Q\right> = \int_V Q\left(\textbf{r}^N\right)
	\rho\left(\textbf{r}^N\right) d\textbf{r}^N
	\label{eq.statMechAverage} $$ 

where $$Q\left(\textbf{r}^N\right)$$ is the thermodynamic quantity of interest that depends on only on the configuration, $$\rho\left(\textbf{r}^N\right) $$ is the probability density, and $$V$$ defines the volume of configuration space over which $$\rho$$ has support.

This integral is very hard to compute, even for small atomic systems. For instance, a monoatomic system of 10 atoms leads to a 30 dimensional integral. 

Yesterday, we learned about using Monte Carlo to evaluate integrals. We can apply this same approach to solve this integral. There are a few extra things we have to consider, however, because of the high dimensionality of the problem. We'll worry more about exactly how to implement this MC integration tomorrow. Today's lesson will focus on the model we are going to use for our Monte Carlo simulation.

## Modeling the system

When modeling atomic and molecular systems, the interaction energy of nonbonded interactions are often approximated using the **Lennard Jones Potential**.

$$ U(r) = 4 \epsilon \left[\left(\frac{\sigma}{r}\right)^{12} -\left(\frac{\sigma}{r}\right)^{6} \right] $$

This is a pairwise potential which is a function of distance between two atoms. It has two parameters, $$\sigma$$ and $$\epsilon$$, which represent the size of the particle, and the depth of the potential well (you can think of this as how strongly the particles are attracted to one another), respectively.

This potential is commonly used in molecular simulation, not just for those of noble gases. For more complicated systems, you will have additional energy terms in addition to the LJ potential, but for our system of noble gases, the is the only contribution to the potential energy. 

The first thing we will do is write a function which evaluates the LJ energy based on an input of distance.

Commonly used parameters for simulating Argon are $$\sigma = 3.4 \unicode{xC5} $$ and $$\epsilon/k_B = 120~K$$

### Dealing with units
Throughout this project, we will be working with reduced units. As stated above, common values are $$\sigma = 3.4 \unicode{xC5} $$ and $$\epsilon/k_B = 120~K$$. When converted to SI units, these quickly become inconvenient to work with. Often, in simulation, we will use something called *reduced units* in order to make calculations more convenient. This approach essentially scales quantities by characteristic values to get them closer to unity.

Quantity    | Expression
------------|------------
Length      | $$L^*=L / \sigma$$
Density     | $$\rho^* = N \sigma^3 / V$$
Energy      | $$U^* = U / \epsilon$$
Pressure    | $$P^* = P \sigma^3 / \epsilon$$
Volume      | $$V^* = V / \sigma^3 $$
Temperature | $$T^* = k_{B} T / \epsilon $$
Time        | $$t^* = t \sqrt{\frac{\epsilon}{ m \sigma^2}}$$

Conveniently for us, Lennard Jones fluids have the surprisingly pleasant behavior of possessing universal behavior when expressed in terms of reduced units as:

$$ 	U^*\left(r_{ij} \right) = 4 \left[\left(\frac{1}{r^*_{ij}}\right)^{12} -\left(\frac{1}{r^*_{ij}}\right)^{6} \right] $$

Since we know that we will need this as a function for our MC calculation, we will write a function for it to use later.

~~~
def calculate_LJ(r_ij):
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy
~~~
{: .language-python}

### Sanity checks

It is always a good idea to test the expected behavior of your function, or do some "sanity checks". We'll think about a few qualities of the LJ potential to check if our function is reasonable.

The LJ potential has the following qualities - 

1. This function should equal zero at r = 1.
1. A minimum occurs at r = 2^(1/6) sigma. In our case, we are using reduced units and sigma is equal to one. This minimum will have a value equal to -epsilon, in our case -1. 

We'll check these two test statements using something called an `assert` statement in Python. In an `assert` statement, you assert that something is True. If the statement following the word `assert` is True, nothing happens. However, if the statement is False, you will get an assertion error.

For example,

~~~
assert 1 == 1
~~~
{: .language-python}

results in no visible output. But,

~~~
assert 2 == 1
~~~
{: .language-python}

results in an `AssertionError`.

~~~
AssertionError                            Traceback (most recent call last)
<ipython-input-26-39a8159f4693> in <module>
----> 1 assert 2 == 1

AssertionError:
~~~
{: .error}

Let's translate our two criteria into `asserts` to check our function:

~~~
assert calculate_LJ(1) == 0
assert calculate_LJ(math.pow(2, (1/6))) == -1
~~~
{: .language-python}

### Lennard Jones Energy of Atomic System

When applying the LJ potential to a set of atomic coordinates, the total potential energy of the system is equal to the sum of the LJ energy over all pairwise interactions:

$$ U \left( \textbf{r}^N \right) = \sum_{i < j} U \left( r_{ij} \right) $$

We can write some steps out for calculating the total Lennard Jones potential energy for a system.

1. Calculate the distance between each particle and every other particle in the system.
1. Evaluate the Lennard Jones potential for each distance
1. Sum the energies to get the total potential energy.

From this procedure, we can see that we will need a few more functions. We will need a function that can calculate a distance between two particles based on their coordinates. We will need to loop over particle pairs.

So that we have a system where we know the answer, we will use some sample systems and benchmarks from the National Institutes of Standards and Technology (NIST). Some sample configurations have been included with your lesson materials.

> ## Exercise
> Write a function called `calculate_distance` which takes in two coordinates as lists ([x, y, z]), and returns the distance between the two points.
>
>> ## Solution
>> One possible solution is given below:
>> ~~~
>>  def calculate_distance(coord1, coord2):
>>     """
>>     Calculate the distance between two 3D coordinates.
>>    
>>     Parameters
>>     ----------
>>     coord1, coord2: list
>>         The atomic coordinates
>>     
>>     Returns
>>     -------
>>     distance: float
>>         The distance between the two points.
>>     """
>>     
>>     distance = 0
>>     for i in range(3):
>>         dim_dist = (coord1[i] - coord2[i]) ** 2
>>         distance += dim_dist
>>     
>>     distance = math.sqrt(distance)
>>     return distance
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

##### Sanity Checks for the Calculate Distance Function

~~~
point_1 = [0, 0, 0]
point_2 = [1, 0, 0]

dist1 = calculate_distance(point_1, point_2)
assert dist1 == 1

point_1 = [0, 0, 0]
point_2 = [0, 1, 0]

dist1 = calculate_distance(point_1, point_2)
assert dist1 == 1

point_1 = [0, 0, 0]
point_2 = [0, 0, 1]

dist1 = calculate_distance(point_1, point_2)
assert dist1 == 1

point_1 = [0, 0, 0]
point_2 = [0, 1, 1]

dist1 = calculate_distance(point_1, point_2)
assert dist1 == math.sqrt(2)
~~~
{: .language-python}

#### Reading coordinates from NIST file

~~~
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
~~~
{: .language-python}


##### Sanity Checks

1. Check that function has expected number of coordinates.
2. Check that first line, last line is as expected.

~~~
# We expect this file to have 800 particles
assert len(sample_coords) == 800

# We expect the first line to be:
first_line = [-0.11263625932560001, 1.385093082507, -0.8842035145736]

for i in range(3):
    assert first_line[i] == sample_coords[0][i]
    
# We expect the last line to be:
last_line = [3.497455843197, 0.3754925406415, 4.393398690912]

for i in range(3):
    assert last_line[i] == sample_coords[-1][i]
~~~
{: .language-python}

### Calculating the total pairwise LJ energy

~~~
total_energy = 0
num_atoms = len(sample_coords)
cutoff = 3.0

count = 0
for i in range(num_atoms):
    count +=1
    for j in range(i+1, num_atoms):
        # Calculate the distance between the particles - exercise.
        dist_ij = calculate_distance(sample_coords[i], sample_coords[j])
        
        if dist_ij < cutoff:
            # Calculate the pairwise LJ energy
            LJ_ij = calculate_LJ(dist_ij)

            # Add to total energy.
            total_energy += LJ_ij

print(f'The total system energy is {total_energy}')
~~~
{: .language-python}

~~~
assert total_energy == -4351.5
~~~
{: .language-python}

~~~
AssertionError                            Traceback (most recent call last)
<ipython-input-11-b7c5eba717e6> in <module>
----> 1 assert total_energy == -4351.5

AssertionError: 
~~~
{: .error}

### Fixing our calculation - periodic boundaries

A typical molecular simulation is carried out anywhere from 10 to 10,000 particles. This number of particles is far away of being
representative of a bulk liquid. To get around this problem, we employ a trick
called periodic boundary conditions. The primary simulation box is surrounded by copy images of itself. For a cubic simulation, there would be 26 images around the central box. This trick has two implications

1. If the position of a particle (i.e.Cartesian coordinates) isoutside the simulation box after a particle translation, an identical particle should enter the box through opposite face of the box.
1. When measuring distances used in the evaluation of the LJn potential, we should measure the minimum image.

Homework
1. Modify the calculate distance function to take an additional argument, `box_length`. The default value should be none. If `box_length` is specified, the `box_length` value should be used to calculate the minimum image distance.
1. Recalculate your total potential energy and check against NIST using periodic boundary conditions.
1. Write a function `calculate_tail_correction` which calculates the tail correction.