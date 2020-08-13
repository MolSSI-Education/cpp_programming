---
title: "Homework Assignment #4"
teaching: 0
exercises: 120
questions:
- "What is the homework for the fourth day?"
objectives:
- "Use pytest to write tests for your code."
---

## Raising Errors

In Python, you can check behavior of a function or code and halt execution if there is a problem by doing something called raising an exception. You can see the python documentation for Exceptions [here](https://docs.python.org/3.7/library/exceptions.html).

For example, consider the `calculate_LJ` if the user inputs a distance of zero, the calculation will fail with a `ZeroDivisionError`. We can instead choose to check this value and raise our own error. For example, 

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

    """
    
    if r_ij == 0:
      raise ZeroDivisionError("Distance of zero input for Lennard Jones energy calculation - inifinite energy!")
    
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy
~~~
{: .language-python}

You can try this out in an interactive python interpreter in the top level of your project:

~~~
>>> import mcsim.energy
>>> mcsim.energy.calculate_LJ(0)
~~~
{: .language-python}

You will now get a different error message than before. 

~~~
ZeroDivisionError: Distance of zero input for Lennard Jones energy calculation - inifinite energy!
~~~
{: .error}

Alternatively, we could have used a `try` `except` statement here. Here, we attempt the code under the `try` block. If the specified `except` occurs (in this case, if  a `ZeroDivisionError` occurs it will run the code under the `except` clause:

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

    """

    try:
        r6_term = math.pow(1/r_ij, 6)
    except ZeroDivisionError:
        raise ZeroDivisionError("Distance of zero input for Lennard Jones energy calculation - inifinite energy!")
        
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy
~~~
{: .language-python}


## Tasks

## Raising Errors
1. Add a `try` and `except` statement to your `run_mc` function which will raise an error if two particles are overlapping (ie, if a `ZeroDivisionError` is raised by `calculate_LJ`. Print an informative message like - "Infinite energy calculated - particles overlapping! Halting MC simulation" if the error occurs. Make sure you have a test for this behavior. You can test for an expected exception using pytest by inserting the following into your function:

~~~
with pytest.raises(EXPECTED_ERROR)
  ** code which will result in error **
~~~
{: .language-python}

## Testing
1. Write tests for the rest of your code.
    - Write unit tests for your functions. You may need to divide this work between two people. For your tests, you can start by moving `assert` statements we wrote in class. Remember that unit tests for a function should not rely on any other function. Use `pytest.mark.parametrize` for at least two of your tests. You should divide this between group members. Aim for a code coverage of at least 80%.
    - Write a test which verifies that `calculate_total_pair_energy` calculates the correct value for a file from NIST. What kind of test (unit, integration, regression) is this?
