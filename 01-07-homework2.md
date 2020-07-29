---
title: "Homework Assignment #2"
teaching: 0
exercises: 120
questions:
- "What is the homework for the second day?"
objectives:
- "Use a second method to calculate pi."
- "Implement the minimum image convention in the calculate distance function."
keypoints:
- "Monte Carlo methods calculate values through random sampling."
---

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## Individual Homework Assignment
1. This should be completed by each group member individually (but you can discuss with your group). Write a python script that estimates the value of $$\pi$$ by evaluating the following integral:

$$ \int_{0}^{1} \frac{1}{1 + x^2} dx $$

This integral will evaluate to $$\frac{\pi}{4} $$

## Group Work - Fixing our LJ calculation

For this portion of the homework, you will work mostly as a group. You have each been added to a collaborative repository with your group members. During this workshop, you should complete homework assignments having to do with the Monte Carlo code on this central repository. You should divide the tasks for the homework, and each group member should make a pull request with their part of the homework. Each pull request should be reviewed by at least one person before merging.

For this homework, you must complete three coding tasks:

1. Exploration of LJ potential and implementation of cutoff in pair potential energy calculation.
1. Tail correction function for cutoff.
1. Minimum image distance in `calculate_distance` function.

Don't forget that one group member must also write a summary/reflection for that day on the group blog.

### Exploring the LJ Potential - cutoffs
Use your `calculate_LJ` function to calculate the Lennard Jones potential energy for a range of distances, $$r$$, from $$r = 0.1$$ to $$r = 5$$ in increments of $$0.1$$.

> ## Code Hint
> We are using just the python standard library at this point, so we don't really have a convenient way to do a `for` loop over decimal numbers. View this hint if you need help on how to generate the specified distances.
>> ## Hint
>> You can use the `range` function to loop over integers and multiply them in the loop by our specified interval of $$0.1$$.
>>
>> ~~~
>> for i in range(1, 51):
>>    r = i * 0.1
>>    ** The rest of your code here **
>>
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

Create a plot of this data (you will need to set the y limit to see anything meaningful. Remember from class that the minimum of this function will occur at $$r=1$$ and will be equal to -1).

Most often when we are running a simulation with pairwise potentials, we will use something called a `cutoff distance`. This is a distance after which the interaction energy between two particles is not calculated and is assumed to be zero. This can lessen computational time significantly, since the energy calculation has to be performed for fewer particles.

A common cutoff distance is $$3 \sigma$$. Do you agree with this choice of cutoff?

**Add a cutoff to your potential energy calculation**. You should modify the script so that the cutoff is a variable. Using the cutoff, the `LJ` potential energy should only be calculated `if` the distance between the particles is less than the cutoff, otherwise, the interaction energy between the two particles is approximated as `0`. This should go in your function `calculate_total_energy` and not in the `calculate_LJ` function.

## Tail Correction
Truncating interactions using a cutoff removes contribution to the potential energy that might be non negligible. The tail correction for our system can be calculated:

$$ U_{tail} = \frac{8 \pi N^2}{3 V} \epsilon \sigma^3
	\left[\frac{1}{3} \left(\frac{\sigma}{r_c} \right)^9 
	- \left(\frac{\sigma}{r_c} \right)^3 \right]
$$

In our reduced units:

$$ U_{tail} = \frac{8 \pi N^2}{3 V}
	\left[\frac{1}{3} \left(\frac{1}{r_c} \right)^9 
	- \left(\frac{1}{r_c} \right)^3 \right]
$$

1. Write a function `calculate_tail_correction` which calculates the tail correction.
    - Write an `assert` statement to check against the reported value from NIST.

### Periodic boundaries

A typical molecular simulation is carried out anywhere from 10 to 10,000 particles. This number of particles is far away of being representative of a bulk liquid. To get around this problem, we employ a trick
called periodic boundary conditions. The primary simulation box is surrounded by copy images of itself. See the diagram below for a 2D cartoon representation:

<figure class="figure">
<img src='../fig/periodic_boundaries.png' height="300">
<figcaption class='figure-caption'>
A schematic representation of periodic boundary conditions. The unit cell of system is in the center cell (pink). The potential energy of the system includes interactions between this center cell and its periodic images. Image Credit Christopher Rowley, obtained from <a href="https://commons.wikimedia.org/wiki/File:Periodic_Boundary_Conditions_in_2D.png">Wikimedia Commons.</a>
</figcaption> 
</figure><br> 

For a cubic simulation, there would be 26 images around the central box. This trick has two implications:

1. If the position of a particle (i.e. Cartesian coordinates) is outside the simulation box after a particle translation, an identical particle should enter the box through the opposite face of the box.
1. When measuring distances used in the evaluation of the LJ potential, we should measure the minimum image.

To get a better idea of periodic boundaries, consider two particles in a periodic box with a length of 10 $$\sigma$$ (remember that when we used reduced units, our units of length are $$\sigma$$). One particle is at `(0, 0, 0)`, and the second is at `(0, 0, 8)`. If we were to measure the distance between these two particles using our `calculate_distance` function, that distance would be 8 $$\sigma$$. However, because we are using periodic boundary conditions, that is not the distance between the particles. There is another copy, or image, of the particle in the adjacent periodic boxes.

Consider the following questions:

1. What is the maximum distance any particle can be from another in each dimension? (this will have to do with the box length)
1. What is the actual distance of our example? (`(0, 0, 0)`, `(0, 0, 8)`)

#### Homework
1. Modify the calculate distance function to take an additional argument, `box_length`. The default value should be `None`. If `box_length` is specified, the `box_length` value should be used to calculate the minimum image distance. When calculating the minimum image distance, follow this procedure:
    - Calculate the distance in each dimension as we did previously.
    - From your previous thinking (where you were asked to consider the maximum distance of any two particles in a particular dimension), you should have arrived at the conclusion that the maximum distance in any direction was $$\frac {l_B}{2}$$ (where $$l_B$$ is the box length). Therefore, if our calculated distance in any direction is greater than $$\frac {l_B}{2}$$, we will want to subtract $$l_B$$ from the value. If there is more than one box length between the two points, you will want to subtract the appropriate number. You can effectively achieve this by dividing the calculated distance in each direction by the box length and rounding to the nearest integer. This result should be multiplied by the box length and subtracted from the distance in each direction.
    
    To get a better understanding of this, consider our example of `(0, 0, 0)` and `(0, 0, 8)`. 

    $$ \vec{r}_i = (0, 0, 0) $$

    $$ \vec{r}_j = (0, 0, 8)$$

    $$\vec{r}_ij = r_j - r_i = (0, 0, 8) $$

    The result above tells us that $$r_{j}$$ is $$8\sigma$$ away from the origin along the z-axis. If we consider that these points follow periodic boundary conditions, we know that the cell is surrounded by copies of itself, meaning there is another copy of particle 1 at (0, 0, 10), or another copy of particle 2 at (0, 0, -2).

    <img src="../fig/minimum_image.svg">

    If we subtract a box length from our vector, we get a separation vector equal to $$(0, 0, -2)$$ which corresponds to the minimum image.
    
    If the particle is greater than one box length away, we will want to subtract more than one box length (whatever is appropriate for the value).

    Add this logic to your `calculate_distance` function. 

~~~
def calculate_distance(coord1, coord2, box_length=None):
    """
    Calculate the distance between two points. When `box_length` is set, the minimum image convention is used to calculate the distance between the points.

    Parameters
    ----------
    coord1, coord2 : list
        The coordinates of the points, [x, y, z]
    
    box_length : float, optional
        The box length. This function assumes box is a cube.

    Returns
    -------
    distance : float
        The distance between the two points.
    """
    # Your function here.

    return distance
~~~
{: .language-python}

1. Recalculate your total potential energy using the new `calculate_distance` function, and check against the value from NIST. This will require you to collaborate with the person implementing the section on cutoffs.
    - Write an `assert` statement to check the NIST value.

