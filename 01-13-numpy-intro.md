---
title: "Working with Numpy Arrays"
teaching: 45
exercises: 20
start: true
questions:
- "What are the differences between numpy arrays and lists?"
- "How can I use NumPy to do calculations?"
objectives:
- "Be able to name the differences between Python lists and numpy arrays."
- "Understand the idea of broadcasting."
keypoints:
- "NumPy arrays which are the same size use element-wise operations when added or subtracted."
- "NumPy uses something called *broadcasting* for arrays which are not the same size to allow arrays to be added or multiplied."
- "NumPy has extensive documentation online - you should check this out if you need to do a computation."
---

[Numpy](https://numpy.org/) is a widely used Python library for scientific computing. It has a number of useful features, including the a data structure called an array. Compared to the built-in data types `lists` which we used previously, `numpy` arrays have many features which can help you in your data analysis. Properly using the features of numpy arrays will make your code much faster and more efficient.

## NumPy Arrays vs. Python Lists

Previously, you have worked with the built-in types of `lists`. NumPy arrays seem similar, but offer some distinct advantages. 

NumPy arrays take up less space, are faster, and have more mathematical operations associated with them. However, unlike lists, they elements all have to be the same type.

There are also differences in how lists and numpy arrays behave. Let's look at some of these.

First open a Jupyter notebook to record your work. 

To use the numpy library, we have to import it. When `numpy` is imported, it is often shortened to `np`. 

We will start with reading in some data from an `xyz` file. Read in the coordinates from `sample_config1.xyz` using the module with your Monte Carlo functions.

~~~
import os
import mc_functions as mc

file_path = os.path.join('sample_configs', 'sample_config1.xyz')
coordinates, box_length = mc.read_xyz(file_path)
~~~
{: .language-python}

Isolate the first coordinate:

~~~
first_atom = coordinates[0]
~~~
{: .language-python}

Now that we have our first coordinate, let's imagine we wanted to do something to it. Let's imagine that we wanted to translate the position of the atom (much like we do in our Monte Carlo simulation!). We want to translate it 0.1 units in the x direction and -0.1 units in the y direction.

In our previously written code, we implemented this in the following way:.

~~~
new_coordinate = deepcopy(first_atom)

translation_vector = [0.1, -0.1, 0]

new_coordinates[0] += translation_vector[0]
new_coordinates[1] += translation_vector[1]
new_coordinates[2] += translation_vector[2]
~~~
{: .language-python}

~~~
[-0.012636259325600002, 1.285093082507, -0.8842035145736]
~~~
{: .output}

However, if we made our coordinates into NumPy arrays, we could have done this differently. One way numpy arrays and lists are different is that you can easily perform element-wise operations on numpy arrays without loops. You can make your code much faster if you use numpy element-by-element operations instead of loops. 

If an operation is performed with two arrays (or a list and an array), NumPy will guess that you want to do element-wise addition. In the code we just wrote, we actually wanted an answer that looked like

`[x1 + x2, y1+ y2, z1+z2]`

where `first_atom = [x1, y1, z1]`, and `translation_vector = [x2, y2, z2]`.

Let's see how we would do the same operation as before using NumPy arrays. To cast a list as a numpy array, you use `np.array(list_name)`.

~~~
import numpy as np

first_atom_np = np.array(first_atom)
translation_vector_np = np.array([0.1, -0.1, 0])
~~~
{: .language-python}

Using the features of numpy arrays, we could have instead written

~~~
new_coordinates_np = first_atom_np + translation_vector_np
print(new_coordinates)
~~~
{: .language-python}

~~~
[-0.012636259325600002, 1.285093082507, -0.8842035145736]
~~~
{: .output}

Numpy was smart - it looked at the shape of both of these variables, saw they were the same shape, and assumed we wanted to do element-wise operation. You could have also subtracted, multiplied, or divided these, and it would have performed element-wise operations.

Note, that this only worked because at least one of these variables was a NumPy array.

~~~
type(first_atom_np)
~~~
{: .language-python}

~~~
numpy.ndarray
~~~

What happens if we try this with a list?

~~~
type(first_atom)
~~~
{: .language-python}

~~~
list
~~~
{: .output}

~~~
first_atom + translation_vector
~~~
{: .language-python}

~~~
[-0.11263625932560001, 1.385093082507, -0.8842035145736, 0.1, -0.1, 0]
~~~
{: .output}

When you use the `+` with two lists, it results in list concatenation, or the second list being appended to the end of the first.

> ## Concatenating NumPy arrays
> If you wanted to concatenate the two where `oxygen_coordinate` was a numpy array, you could have done so with the `np.concatenate` function.
{: .callout}

You can add two arrays together, multiply arrays by scalars, or do element-wise multiplcation of arrays.

For example, you can multiply two numpy arrays to get their element-wise product. This means that given two vectors `a = np.array([a0, a1, a2])` and `b = np.array([b0, b1, b2])`, `a * b = [a0*b0, a1*a1, a2*b2]`.

In contrast, if `a` and `b` were lists, you would get an error. 

> ## Check your understanding
> Consider the variable definitions for `a1` and `a2`. What does each print statement result in?
>
> ~~~
> a1 = np.array([2, 1, 0])
> a2 = np.array([1, 3, 5])
> 
> print(a1 * a2)
> print(a1 + a2)
> ~~~
> {: .language-python}
> 
>
>> ## Answer
>> 
>> The code block results in 
>> ~~~
>> [2 3 0]
>> [3 4 5]
>> ~~~
>> {: .output}
>> For the first line, the first element is `a1[0]*a2[0]`, the second element is `a1[1]*a2[1]`, and the third element is `a1[2]*a2[2]`.
>> For the second line, the first element is `a1[0]+a2[0]`, second is `a1[1]+a2[1]`, third is `a1[2]+a2[2]`.
>>
> {: .solution}
{: .challenge}

> ## Check your understanding
> What happens if `a1` and `a2` are lists? Consider each `print` statement separately.
>
> ~~~
> a1 = [2, 1, 0]
> a2 = [1, 3, 5]
> 
> print(a1 + a2)
> print(a1 * a2)
> ~~~
> {: .language-python}
> 
>> ## Answer
>> 
>> The first print statement results in concatenation of the lists.
>> ~~~
>> [2, 1, 0, 1, 3, 5]
>> ~~~
>> {: .output}
>> 
>> Second print statement results in a TypeError. Two lists cannot be multiplied. 
>> ~~~
>> TypeError: can't multiply sequence by non-int of type 'list
>> ~~~
>> {: .error}
>> This is because two lists cannot be multiplied. If you wanted to do element-wise multiplication, you would have to use `numpy` arrays (like in the previous exercise.)
>>
> {: .solution}
{: .challenge}

## Broadcasting
Another special thing about numpy is something called **broadcasting**. Broadcasting occurs when you attempt mathematical operations on arrays that have different shapes. If possible, the smaller array is "broadcast" across the larger array.

Let's think about what would happen if we wanted to move every atom in our coordinate set by our translation vector.

If you were working with Python lists, or you didn't know about the features of numpy arrays, you might try to do this with a `for` loop. 

~~~
new_coordinates = []

for atom in coordinates:
    new_x = atom[0] + translation_vector[0]
    new_y = atom[1] + translation_vector[1]
    new_z = atom[2] + translation_vector[2]
    
    new_coordinates.append([new_x, new_y, new_z])
~~~
{: .language-python}

Broadcasting in `numpy` allows us to achieve that with one command, rather than a `for` loop. It is not even necessary for both things you are adding to be numpy arrays, only one.

~~~
translated_coordinates = coordinates + translation_vector_np

print(new_coordinates)
~~~
{: .language-python}

Note that both variables do not have to be arrays for this to work, only one. However, the two array-like variables do have to have a matching dimension. You can see the shape of an array using the function `np.shape`.

~~~
np.shape(coordinates)
~~~
{: .language-python}

~~~
(800, 3)
~~~
{: .output}

~~~
np.shape(translation_vector_np)
~~~
{: .language-python}

~~~
(3,)
~~~
{: .output}

When you typed, `coordinates + translation_vector_np`, numpy looked at the shapes of both arrays to figure out if they were compatible. 

It starts with the dimensions to the right, so when it saw to matching 3's it assumed you wanted to do element-wise operation this way, and stretched or 'broadcast' the smaller array to match the larger one.

## Logical comparisons

We can also do logical comparisons on whole arrays. For example, to find out if values in the array are greater than 0, we can write

~~~
coordinates = np.array(coordinates)
print(coordinates > 0)
~~~
{: .language-python}

This will print either `True` or `False` for each array element depending on whether the value of that element is greater than 0 or not.

~~~
array([[False,  True, False],
       [False, False,  True],
       [False,  True,  True],
       ...,
       [False,  True,  True],
       [ True,  True,  True],
       [ True,  True,  True]])
~~~
{: .output}

To get every value in the array that is greater than 0, we can use this as a list of indices we want, or a slice.

~~~
greater_than_0_values = coordinates[coordinates>0]
print(greater_than_0_values)
~~~
{: .language-python}

~~~
[1.38509308 0.83913362 1.65106295 ... 3.49745584 0.37549254 4.39339869]
~~~
{: .output}

## Array Axes

Imagine we wanted to calculate the geometric center of our atoms To do this, we would need to get the average x coordinate, the average y coordinate, and the average z coordinate.

Previously, we would have done this with a `for` loop. However, the `numpy.mean` function will let us do that without a `for` loop.

To calculate the mean, or average of a set of data, 

~~~
np.mean(coordinates)
~~~
{: .language-python}

~~~
-0.029640541574553616
~~~
{: .output}

When we use the mean function on an array without any other arguments, it will give us the average of all of the values. However, sometimes, we prefer to have the average of all the rows or columns instead. `NumPy` functions usually allow us to specify this using the `axis` command. For our example of the geometric mean, we would want the average of the `x` column, the average of the `y` column, and the average of the `z` column.

Check the shape of our coordinates again:

~~~
np.shape(coordinates)
~~~
{: .language-python}

~~~
(800, 3)
~~~
{: .output}

If we want the column average (or the average of all the rows for a column), we would specify `axis=0` in our `mean` calculation.

~~~
center = np.mean(data, axis=0)
print(center)
~~~
{: .language-python}