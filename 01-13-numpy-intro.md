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

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

[Numpy](https://numpy.org/) is a widely used Python library for scientific computing. It has a number of useful features, including the a data structure called an array. Compared to the built-in data types `lists` which we used previously, `numpy` arrays have many features which can help you in your data analysis. Properly using the features of numpy arrays will make your code much faster and more efficient.

## NumPy Arrays vs. Python Lists

Previously, you have worked with the built-in types of `lists`. NumPy arrays seem similar, but offer some distinct advantages. 

First open a Jupyter notebook to record your work. 

To use the numpy library, we have to import it. When `numpy` is imported, it is often shortened to `np`. Define a system of three atoms as we have been using in many of our test cases:

~~~
import numpy as np
import math 

# Construct some coordinates
coordinates = [[0, 0, 0], [0, 0, math.pow(2, 1/6)], [0, 0, 2 * math.pow(2, 1/6)], [math.pow(2, 1/6), 0, 0]]

second_coordinate = coordinates[0]
~~~
{: .language-python}

Now that we have our second coordinate, let's imagine we wanted to do something to it. Let's imagine that we wanted to translate the position of the atom (much like we do in our Monte Carlo simulation!). We want to translate it 0.1 units in the x direction, -0.1 units in the y direction, and 0 units in the z direction.

Using the Python Standard Library as we have learned so far, we could write code for this translation:

~~~
translation_vector = [0.1, -0.1, 0]

new_coordinate = []
for i in range(3):
    translated_coordinate = second_coordinate[i] + translation_vector[i]
    new_coordinate.append(translated_coordinate)
~~~
{: .language-python}

~~~
[0.1, -0.1, 1.122462048309373]
~~~
{: .output}

In our code so far, we have written a lot of `for` loops. Use of NumPy, however, will allow us to avoid loops in many cases. Let's consider how we could perform our translation with NumPy instead. One way numpy arrays and lists are different is that you can easily perform element-wise operations on numpy arrays without using `for` loops. You can make your code much faster if you use numpy element-by-element operations instead of loops. 

If an operation is performed with two arrays (or a list and an array), NumPy will guess that you want to do element-wise addition. In the code we just wrote, we actually wanted an answer that looked like

$$(x_1 + x_2, y_1+ y_2, z_1+z_2)$$

where `second_atom = [x1, y1, z1]`, and `translation_vector = [x2, y2, z2]`. In other words, we added elememts with the same index together (or did the operation "element-wise"). You will also notice that in the `for` loop we wrote, we were using the same index `i` for both lists we were indexing into. Because of this, we can `numpy` arrays to more easily perform this calculation.

Let's see how we would do the same operation as before using NumPy arrays. To cast a list as a numpy array, you use `np.array(list_name)`.

~~~
import numpy as np

# Create numpy array from list
second_coordinate_np = np.array(second_coordinate)

# Create a numpy array from list
translation_vector_np = np.array(translation_vector)
~~~
{: .language-python}

Using the features of numpy arrays, we could have instead written the following instead of the original `for` loop.

~~~
new_coordinate_np = second_coordinate_np + translation_vector_np
print(new_coordinate_np)
~~~
{: .language-python}

~~~
[ 0.1        -0.1         1.12246205]
~~~
{: .output}

NumPy performed the addition operation on these two arrays in an element-wise fashion. The shape of both lists was compared, and because they were the same shape, element-wise operation was used. You could have also subtracted, multiplied, or divided these, and it would have performed element-wise operations.

Note, that this only worked because at least one of these variables was a NumPy array.

~~~
type(second_coordinate_np)
~~~
{: .language-python}

~~~
numpy.ndarray
~~~

What happens if we try this were both variables are a list?

~~~
type(second_coordinate)
~~~
{: .language-python}

~~~
list
~~~
{: .output}

~~~
# Adding two lists results in list concatenation
second_coordinate + translation_vector
~~~
{: .language-python}

~~~
[0, 0, 1.122462048309373, 0.1, -0.1, 0]
~~~
{: .output}


When you use the `+` with two lists, it results in list concatenation, or the second list being appended to the end of the first. Element-wise operation is not possible on standard Python lists. You must use `for` loops to iterature through them.

> ## Concatenating NumPy arrays
> If you wanted to concatenate the two numpy arrays, you could have done so with the `np.concatenate` function.
{: .callout}


~~~
# Addition of two lists results in concatenation of the lists
# Addition of to numpy arrays results in elements wise addition if possible
~~~

Element wise operations work with multiplication and division as well.

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

## Array Shape
The two arrays have to have compatible dimensions in order to be added. For example, if we try to add an array with two elements to an array of three elements, we will get an error.

~~~
my_array = np.array([0.2, 0.3])
second_coordinate + my_array
~~~
{: .language-python}

~~~
ValueError: operands could not be broadcast together with shapes (3,) (2,) 
~~~
{: .error}

This is because the two arrays have incompatible sizes. You can see the size of the array by using the command:

~~~
array_name.shape
~~~

For example, we can check the size of `second_coordinate_np`:

~~~
second_coordinate_np.shape
~~~
{: .language-python}

> ## Check your understanding
> Convert the variable `coordinates` to a NumPy array and save it with the variable name `coordinates_np`. Print the array shape.
> 
>> ## Answer
>> ~~~
>> coordinates_np = np.array(coordinates)
>> print(coordinates_np.shape)
>> ~~~
>> {: .language-python}
>>
>> ~~~
>> (4, 3)
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

Though hard to see from our example, when the shape of a NumPy array is given, the first number is the number of rows and the second number is the number of columns. When we use `coordinats_np.shape` it tells us `(4,3)`. This means we have 4 rows and 3 columns (which is what we would expect for four atoms in three dimensions).

### Accessing Information in a Multidimensional Array
In the Python standard library, we have been representing coordinate information in a nested list. This required us to use two sets of brackets for indexing. For example, to get the x value of atom index 3 from our nested list (Python Standard library), we could do the following:

~~~
atom_3 = coordinates[3]
x_coord_atom_3 = atom_3[0]
print(x_coord_atom_3)
~~~
{: .language-python}

~~~
1.122462048309373
~~~
{: .output}

this could have been done in one line as well, note we have to use two sets of brackets:

~~~
print(coordinates[3][0])
~~~
{: .language-python}

~~~
1.122462048309373
~~~
{: .language-python}

When accessing information in a NumPy array, we can put the indices in one set of brackets. You put the row you would like first, followed by the column:

~~~
coordinates[3, 0]
~~~
{: .language-python}

## Broadcasting
However, in order for this type of operation to work, the arrays do not have to be *exactly* the same size they just have to have compatible shapes! Another special thing about numpy is something called **broadcasting**. Broadcasting occurs when you attempt mathematical operations on arrays that have different shapes. If possible, the smaller array is "broadcast" across the larger array.

Let's think about what would happen if we wanted to move every atom in our coordinate set by our translation vector.

If you were working with Python lists, or you didn't know about the features of numpy arrays, you might try to do this with a `for` loop. 

~~~
num_atoms = len(coordinates)
new_coordinates = []

for n in range(num_atoms):
    
    atom_coord = coordinates[n]
    updated_coord = []
    for i in range(3):
        translated_coordinate = atom_coord[i] + translation_vector[i]
        updated_coord.append(translated_coordinate)
    
    new_coordinates.append(updated_coord)
    
print(new_coordinates)
~~~
{: .language-python}

~~~
[[0.1, -0.1, 0], [0.1, -0.1, 1.122462048309373], [0.1, -0.1, 2.244924096618746]]
~~~
{: .output}

If we think about the indices we were adding the x indices together, the y indices together, and the z indices together for the two arrays. You might write something that looks like this to express the addition:

$$
c_{translated} = 
  \left[\begin{array}{rrr}
    c_{11} + t_1 & c_{12} + t_2 & c_{13} + t_3 \\
    c_{21} + t_1 & c_{22} + t_2 & c_{23} + t_3 \\
    c_{31} + t_1 & c_{32} + t_2 & c_{33} + t_3 \\
  \end{array}\right]
$$

NumPy is able to compare the shape of two arrays to see if they are compatible. When we examine the shape, we see that they both have `3` present. The shapes are compatible.Broadcasting in `numpy` allows us to achieve that with one command, rather than in multiple `for` loop. 

~~~
new_coordinates_np = coordinates_np + translation_vector_np

print(new_coordinates_np)
~~~
{: .language-python}

Note that both variables do not have to be arrays for this to work, only one. However, the two array-like variables do have to have a matching dimension. You can see the shape of an array using the function `np.shape`.

~~~
np.shape(coordinates)
~~~
{: .language-python}

~~~
(3, 3)
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

If we are using a NumPy array for operations, this check will always be performed. You can also multiply arrays by scalars:

~~~
10 * coordinates_np
~~~
{: .language-python}


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
coordinates_np.mean()
~~~
{: .language-python}

~~~
0.3741540161031243
~~~
{: .output}

When we use the mean function on an array without any other arguments, it will give us the average of all of the values. However, sometimes, we prefer to have the average of all the rows or columns instead. `NumPy` functions usually allow us to specify this using the `axis` command. When we specify `axis` we give an array axis over which to give an average. In our two dimensional array, we choose between axis=0 and axis=1.

~~~
print(coordinates_np.mean(axis=0))
print(coordinates_np.mean(axis=1))
~~~
{: .language-python}

Notice that when we calculate the mean of axis=0, we get an output array with 4 values, while when we calculate the mean of axis=1, we get 3 values.

~~~
[0.28061551 0.         0.84184654]
[0.         0.37415402 0.74830803 0.37415402]
~~~
{: .output}

Check the shape of our coordinates again:

~~~
coordinates_np.shape
~~~
{: .language-python}

~~~
(4, 3)
~~~
{: .output}

If we want the column average (or the average of all the rows for a column), we would want the average value of our columns. When we examine the shape, we see that the have `4` values for each column (and this is indicated in the shape index = 1) We expect that we will have three values - the average x, the average y, and the average z. One way you might think about it is to give the axis index for the number of values you expect to average. For example, if I want to calculate the geometric center of my set of atoms, I expect that I will be averaging 4 x values, 4 y values, and 4 z values. The size 4 is in shape index `0`. So, specify `axis=0` in my `mean` calculation.

~~~
center = np.mean(data, axis=0)
print(center)
~~~
{: .language-python}

There are a lot of ways to think about array axes, and it will be beneficial to do a google search to find another explanation. 