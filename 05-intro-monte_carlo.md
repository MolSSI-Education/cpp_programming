---
title: "Intro to Monte Carlo Methods"
teaching: 60
exercises: 20
questions:
- "What are Monte Carlo methods?"
- "How can I use Monte Carlo to estimate pi?"
objectives:
- "Explain Monte Carlo methods."
- "Apply Monte Carlo method to calculate pi."
- "Apply Monte Carlo to calculate integrals"
keypoints:
- "Monte Carlo methods calculate values through random sampling."
---

# Calculation of pi using MC Integration

This module will cover calculating pi using MC integration. Functions from the python standard library are used (matplotlib used for visualization). This is just the code for now. I will expand the lesson in the next few days.

# Imports
~~~
import random
import math
~~~
{: .language-python}

# Reference Value
~~~
# Calculate a reference value.

# arctan(1) is equal to pi/4
pi = 4 * math.atan(1)
~~~

# Initial Calculation of pi using 100 samples.
~~~
# Start by using 100 samples.
n_samples = 100

num_inside = 0
for i in range(n_samples):
    # Generate a random point between 0 and 1 for x.
    x = random.random()
    
    # Generate a random point between 0 and 1 for y.
    y = random.random()
    
    ## Calculate the radius of this point.
    r = math.sqrt(x ** 2 + y ** 2)
    
    # if this value is less than 1, the point is inside the circle.
    if r < 1:
        num_inside += 1
~~~
{: .language-python}

# Pi calculation
~~~
# Calculate pi
4 * num_inside / n_samples
~~~
{: .language-python}

# Visualization

~~~
# Let's plot our points.

import matplotlib.pyplot as plt

%matplotlib notebook

# Create a figure - just empty for now.
fig = plt.figure(figsize=(4,4))
ax = fig.add_subplot(111)
fig.show()
~~~
{: .language-python}

Calculate again, adding points to our figure as they are calculated:

~~~
# This time add points to plot as they're calculated.

# Start by using 100 samples.
n_samples = 100

num_inside = 0
for i in range(n_samples):
    # Generate a random point between 0 and 1 for x.
    x = random.random()
    
    # Generate a random point between 0 and 1 for y.
    y = random.random()
    
    ## Calculate the radius of this point.
    r = math.sqrt(x ** 2 + y ** 2)
    
    # if this value is less than 1, the point is inside the circle.
    if r < 1:
        num_inside += 1
        # if inside add to plot
        ax.plot(x, y, 'ob')
    else:
        ax.plot(x, y, 'r^')

ax.set_xlim(0, 1)
ax.set_ylim(0, 1)
~~~
{: .language-python}

## Improving the visualization

~~~
# Use patch in matplotlib to make a circle for better visualization

from matplotlib.patches import Circle

circle = Circle((0, 0), 1, color='k', alpha=0.2)
ax.add_patch(circle)
fig.show()
~~~
{: .language-python}

# Pi for increasing number of samples
~~~
n_samples = [10, 100, 1000, 10000]

# Empty lists for appending.
calculated_pi = []

for n in n_samples:
    num_inside = 0
    for i in range(n):
        # Generate a random point between 0 and 1 for x.
        x = random.random()

        # Generate a random point between 0 and 1 for y.
        y = random.random()

        ## Calculate the radius of this point.
        r = math.sqrt(x ** 2 + y ** 2)

        # if this value is less than 1, the point is inside the circle.
        if r < 1:
            num_inside += 1
    print(n, 4*num_inside/n)
    calculated_pi.append(4*num_inside/n)
~~~
{: .language-python}

# Examining error
Exercise - calculate the % error for each n.

~~~
# Examine the error with increasing number of samples.

n_samples = [10, 100, 1000, 10000, 100000]

# Empty lists for appending.
calculated_pi = []
error = []

for n in n_samples:
    num_inside = 0
    for i in range(n):
        # Generate a random point between 0 and 1 for x.
        x = random.random()

        # Generate a random point between 0 and 1 for y.
        y = random.random()

        ## Calculate the radius of this point.
        r = math.sqrt(x ** 2 + y ** 2)

        # if this value is less than 1, the point is inside the circle.
        if r < 1:
            num_inside += 1
        
    print(n, 4*num_inside/n)
    calculated_pi.append(4*num_inside/n)
    error.append(math.fabs(100 * (calculated_pi[-1] - pi)/ pi))
~~~
{: .language-python}

Plot the error
~~~
fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(n_samples, error)
~~~
{: .language-python}

Plot the error - semilog plot
~~~
fig = plt.figure()
ax = fig.add_subplot(111)
ax.semilogx(n_samples, error)
~~~
{: .language-python}






