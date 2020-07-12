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

## Monte Carlo Integration

1. **Individual** - This should be completed by each group member individually (but you can discuss with your group). Write a python script that estimates the value of $$\pi$$ by evaluating the following integral:

$$ \int_{0}^{1} \frac{1}{1 + x^2} dx $$


## Fixing our LJ calculation - periodic boundaries

**Group Work** - A typical molecular simulation is carried out anywhere from 10 to 10,000 particles. This number of particles is far away of being representative of a bulk liquid. To get around this problem, we employ a trick
called periodic boundary conditions. The primary simulation box is surrounded by copy images of itself. For a cubic simulation, there would be 26 images around the central box. This trick has two implications:

1. If the position of a particle (i.e.Cartesian coordinates) is outside the simulation box after a particle translation, an identical particle should enter the box through the opposite face of the box.
1. When measuring distances used in the evaluation of the LJ potential, we should measure the minimum image.

To get a better idea of periodic boundaries, consider two particles in a periodic box with a length of 10 $$\sigma$$ (remember that when we used reduced units, our units of length are $$\sigma$$). One particle is at `(0, 0, 0)`, and the second is at `(0, 0, 8)`. If we were to measure the distance between these two particles using our `calculate_distance` function, that distance would be 8 $$\sigma$$. However, because we are using periodic boundary conditions, that is not the distance between the particles. There is another copy, or image, of the particle in the adjacent periodic boxes.

Consider the following questions:

1. What is the maximum distance any particle can be from another in each dimension? (this will have to do with the box length)
1. What is the actual distance of our example? (`(0, 0, 0)`, `(0, 0, 8)`)

## Homework
1. Modify the calculate distance function to take an additional argument, `box_length`. The default value should be `None`. If `box_length` is specified, the `box_length` value should be used to calculate the minimum image distance. You can use this formula to easily calculate the minimum image distance:

$$ Formula here $$

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

1. Recalculate your total potential energy using the new `calculate_distance` function, and check against the value from NIST.
    - Write an `assert` statement to check the NIST value.
1. Write a function `calculate_tail_correction` which calculates the tail correction.
    - Write an `assert` statement to check against the reported value from NIST.