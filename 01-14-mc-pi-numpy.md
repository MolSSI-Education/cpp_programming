---
title: "Using NumPy Arrays to Calculate Pi"
teaching: 30
exercises: 10
questions:
- "What are the differences between numpy arrays and lists?"
objectives:
- "Be able to name the differences between Python lists and numpy arrays."
- "Understand the idea of broadcasting."
keypoints:
- "NumPy arrays which are the same size use element-wise operations when added or subtracted."
- "NumPy uses something called *broadcasting* for arrays which are not the same size to allow arrays to be added or multiplied."
- "NumPy has extensive documentation online - you should check this out if you need to do a computation."
---

# Rewriting our calculation of pi using NumPy

This will be expanded with explanations. For now, just the final code.

Final code:
~~~
# Start by using 100 samples.
n_samples = 100

random_numbers = np.random.random(size=(n_samples,2))

vals = np.sum(random_numbers**2, axis=1)
num_inside = np.sum(vals < 1)

pi = 4 * num_inside / n_samples
~~~
{: .language-python}