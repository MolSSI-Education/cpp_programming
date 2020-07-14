---
title: "Monte Carlo for a Lennard Jones Fluid - Part 1"
teaching: 100
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

When modeling atomic and molecular systems, the energy of nonbonded interactions are often approximated using the **Lennard Jones Potential**.

$$ U(r) = 4 \epsilon \left[\left(\frac{\sigma}{r}\right)^{12} -\left(\frac{\sigma}{r}\right)^{6} \right] $$

This is a pairwise potential which is a function of distance between two atoms. It has two parameters, $$\sigma$$ and $$\epsilon$$, which represent the size of the particle, and the depth of the potential well (you can think of this as how strongly the particles are attracted to one another), respectively.

This potential is commonly used in molecular simulation to model nonbonded interactions, not just for those of noble gases. For more complicated systems, you will have additional energy terms in addition to the LJ potential, but for our system of noble gases, the is the only contribution to the potential energy. 

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

We'll check these two test statements using something called an `assert` statement in Python. In an `assert` statement, you assert that something is `True`. If the statement following the word `assert` is `True`, nothing happens. However, if the statement is `False`, you will get an assertion error.

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

### Python Style - Docstrings

Part of this class is cultivating good coding habits. A "best practice" when writing your code is to make sure that your code is adequately documented. This could be through code comments, but a very good way to document your code is to use something called `docstrings` in Python. In this course, we may not always write `docstrings` right away, but you should always write them.

A `docstring`, or `documentation string` is a string which follows the function definition and describes a function's inputs and behavior. If you write `docstrings`, you will have a much easier time returning to your code, sharing it with others, and creating online documentation. For Python, there is a tool called `Sphinx` which can parse your code for `docstrings` and build web documentation for you (we will not discuss this in this course).

We will write `Numpy` style `docstrings`. Consider our `calculate_LJ` function with a docstring:

~~~
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

    Examples
    --------
    >>> calculate_LJ(1)
    0

    """
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy
~~~
{: .language-python}

We've now added a multi-line comment to the beginning of our function. Docstrings **are the first statement after a function or module definition** and are opened and closed with three quotes.

The `docstring` should explain what the function or module does (and not how it is done).

> ## The `__doc__` attribute
>
> When you add a docstring to a function or module, python automatically adds this to the `__doc__` attribute of the object.
>
> You can also see an object's docstring by typing `object.__doc__` into the Python interpreter. For example, to see the docstring associated with the calculate_LJ function, use `calculate_LJ.__doc__` )
{: .callout}

### Sections of a Docstring
There are many ways you could format this docstring (different styles/conventions). We recommend using [numpy style docstrings], and this is what the example above and `calculate_LJ` function are written in.

Each docstring has a number of sections which are separated by headings. Headings should be underlined with hyphens (`-----`). There are many options for sections, we will only cover the most relevant here. If you would like to see a full list, check out the documentation for [numpy style docstrings].

#### 1. Short summary
A one-line summary that does not use the variable name or the function name. In our `calculate_LJ` function, this corresponds to the following.

~~~
"""
The LJ interaction energy between two particles.
"""
~~~
{: .language-python}

#### 2. Extended summary
A few sentences giving a detailed description of the function or module. This section should be used to clarify *functionality*, not to discuss implementation.

~~~
"""
Computes the pairwise Lennard Jones interaction energy based on the separation distance in reduced units.
"""
~~~
{: .language-python}

#### 3. Parameters
This section contains a description of the function arguments - keywords and expected types.

The parameters for our `calculate_LJ` function is shown below:

~~~
"""
    Parameters
    ----------
    r_ij : float
        The distance between the particles in reduced units.
"""
~~~
{: .language-python}

Here, you can see that the parameter section begins with the section title ("Parameters"), followed by a line of hypens ("----"). On the next line, we have the argument name (r_ij), then a colon followed by the input type of the argument. This line says that the argument should be of type `float`. The next line gives a more detailed description of the variable. When the there is more than one input parameter,they should be written on new line.

#### 4. Returns
This section is very similar to the `Parameters` section above. In contrast to the `Parameters` section, each returned value does not have to be named, but the type of the return value is required.

For our `calculate_LJ` function, our `Returns` section looks like the following.

~~~
"""
Returns
    -------
    pairwise_energy : float
        The pairwise Lennard Jones interaction energy in reduced units.
"""
~~~
{: .language-python}

#### 5. Examples
This is an optional section to show examples of functionality. This section is meant to illustrate usage. Though this section is optional, its use is strongly encouraged.

Consider the example we have in our docstring

~~~
"""
Examples
--------
>>> calculate_LJ(1)
    0
"""
~~~
{: .language-python}

It is important that your Examples in docstrings be working Python. We will see in the `testing` lesson how we can run automatic tests on our docstrings.

We have one line of code for our example. In examples, lines of code begin with `>>>`. On the last line, you give the output (with no `>>>` in front.)

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
> Use the docstring to write your function:
> ~~~
>  def calculate_distance(coord1, coord2):
>     """
>     Calculate the distance between two 3D coordinates.
>    
>     Parameters
>     ----------
>     coord1, coord2: list
>         The atomic coordinates
>     
>     Returns
>     -------
>     distance: float
>         The distance between the two points.
>     """
> ~~~
> {: .language-python}
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

Let's try out our function on some values.

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

Information about cut-offs.

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

Your homework (explained on the next page), will partially be to implement a solution to this problem.

[numpy style docstrings]: https://docs.scipy.org/doc/numpy/docs/howto_document.html#numpydoc-docstring-guide


