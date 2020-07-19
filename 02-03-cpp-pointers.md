---
title: "Arrays, Pointers, Memory, and Vectors"
teaching: 30
exercises: 5
questions:
- TODO
objectives:
- TODO
keypoints:
- TODO
---

> ## Prerequisites
> - TODO
{: .prereq}

## Arrays

Note that arrays in C++, like python, are zero-indexed.

**When compiling/building, you will need to change the source file on the command line**.

~~~
// arrays.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int arr[10] = { 3, 5, 7, 9, 11, 13, 15, 17, 19, 21 };

    for(int i = 0; i < 10; i++)
    {
        std::cout << "Element " << i << ": " << arr[i] << std::endl;
    }
    
    return 0;
}
~~~
{: .language-cpp}

~~~
Element 0: 3
Element 1: 5
Element 2: 7
Element 3: 9
Element 4: 11
Element 5: 13
Element 6: 15
Element 7: 17
Element 8: 19
Element 9: 21
~~~
{: .output}


~~~
// arrays.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int arr[10];

    for(int i = 0; i < 10; i++)
    {
        // Set the i-th value to 2*i
        arr[i] = 2*i;
    }

    for(int i = 0; i < 10; i++)
    {
        std::cout << "Element " << i << ": " << arr[i] << std::endl;
    }
    
    return 0;
}
~~~
{: .language-cpp}

~~~
Element 0: 0
Element 1: 2
Element 2: 4
Element 3: 6
Element 4: 8
Element 5: 10
Element 6: 12
Element 7: 14
Element 8: 16
Element 9: 18
~~~
{: .output}

> ## Overflow
> What happens when you try to write beyond the bounds of the array? That is called a
> *buffer overflow* and depending on many factors can result in a crash or random behavior.
> Attempting to read out of bounds may also result in a crash, or you may just read garbage.
{: .callout}




## Addresses

> ## A word of Warning
> Pointer mismanagement is probably the most common source of bugs in C and often in C++.
> These bugs often lead to crashing, if you are lucky, but can also lead to silent,
> hard-to-debug issues or security vulnerabilites. Later we will talk about
> good practices for pointer management.
{: .callout}

In C/C++, variables you declare have a defined amount of memory and exist at a particular
location in memory. When accessing this variable, the compiler generates (binary) code to lookup
the data in that location.

We can get the location of a variable using the `&` operator. It is rarely needed to print
this information, but can sometimes be used in debugging.

Create a new file called `pointers.cpp` and try out the following code.

~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int j = 1234;
    std::cout << "Address of j: " << &j << std::endl;
    return 0;
}
~~~
{: .language-cpp}

When running, I get the following output. The address will likely be different
on your computer, and will change when re-running.

~~~
Address of j: 0x7ffd0046f874
~~~
{: .output}

The address of the pointer is given in hexadecimal, but its exact value is
rarely important.

## Pointers

Now that we know how to get the address of a variable, we can then store that address in another
variable. Doing so gives us a *pointer* to the same memory address as the original variable.

To do so, we must define the type of the new variable to be a pointer type. This is denoted with
a *star* (\*) that comes after the pointed-to type.


~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int j = 1234;
    std::cout << "Address of j: " << &j << std::endl;
    std::cout << "Value of j: " << j << std::endl;

    int * pj = &j; // pi points to address of j
    std::cout << "Value of pj: " << pj << std::endl;
    return 0;
}
~~~
{: .language-cpp}

~~~
Address of j: 0x7fff9eebe4ec
Value of j: 1234
Value of pj: 0x7fff9eebe4ec
~~~
{: .output}

You will notice that the value of the pointer is the address that we assigned to it.
So how can we retrieve the actual value stored in the pointed-to memory? By again
using *star* (in this case, called the *pointer dereferencing operator*)

~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int j = 1234;
    std::cout << "Address of j: " << &j << std::endl;
    std::cout << "Value of j: " << j << std::endl;

    int * pj = &j; // pi points to address of j
    std::cout << "Value of pj: " << pj << std::endl;
    std::cout << "Value of *pj: " << *pj << std::endl;
    return 0;
}
~~~
{: .language-cpp}

~~~
Address of j: 0x7ffd8e66f70c 
Value of j: 1234
Value of pj: 0x7ffd8e66f70c
Value of *pj: 1234
~~~
{: .output}

So now we've shown that we can access the same memory location, but can we change the value? Of course we can

> ## Manual addressing of pointers
> Can you manually set the address of a pointer via something like `int * j = 0x13579bdf;`? Yes.
> However, in scientific computing, this would be extremely rarely done, if ever.
> It does have its place in embedded platforms, microcontrollers, etc. You will likely not
> ever need to do this in your entire scientific career.
{: .callout}


~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int j = 1234;
    std::cout << "Value of j: " << j << std::endl;

    int * pj = &j; // pj points to address of j
    std::cout << "Value of *pj: " << *pj << std::endl;

    // Change j via pj
    *pj = 5678;

    std::cout << "Value changed!" << std::endl;
    std::cout << "New Value of j: " << j << std::endl;
    std::cout << "New Value of *pj: " << *pj << std::endl;
    
    return 0;
}
~~~
{: .language-cpp}


~~~
Value of j: 1234
Value of *pj: 1234
Value changed!
New Value of j: 5678
New Value of *pj: 5678
~~~
{: .output}


## Dynamic memory allocation

Think about the array examples. We must know how many elements exist at compile time. But what
if we don't?

For example, lets say we want to read in the coordinates of a molecule. How big should that
array be? One solution is to make a very large array and then only use part of it. This not only results in
inefficient use of memory, but also can cause many headaches down the road when people try to run using
larger molecules.

The solution is to use *dynamic memory allocation*. In C++, this is done with the `new` keyword,
which returns (you guessed it) a pointer!

It is important to remember that if you allocate memory with `new`, you must also *deallocate* it
with `delete`. Doing this tells the operating system that you are done using it and allows
it to be used by other programs.

> ## Memory Leaks
> If you fail to deallocate/free the memory, you will have a memory leak. When your
> program ends, the operating system will still be able to free any memory that you
> did not. However, if your program is long-lived (ie, a very long simulation), memory
> usage can steadily increase and maybe even trigger an OOM (out-of-memory) error in the
> operating system.
{: .callout}

~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int memsize = 16;

    // Store the result in a pointer
    double * data = new double[memsize];

    for(int i = 0; i < memsize; i++)
    {
        data[i] = 3.1415 * i;
    }
    
    for(int i = 0; i < memsize; i++)
    {
        std::cout << "Element " << i << ": " << data[i] << std::endl;
    }

    // When done, free it
    delete [] data; 
    
    return 0;
}
~~~
{: .language-cpp}


## A better way - std::vector

If you need a dynamically-sized array of data that behaves like a `list` in Python,
then you can use `std::vector` in C++. A `std::vector` is an array that can be added to and resized
just like a `list`, while the memory management shown above is all handled
automatically.

> ## Homogeneous vs. Heterogeneous
> The big difference between C++ `vector` and Python `list` is that a C++ `vector` is *homogeneous* - that is,
> it can only hold data of a single specified type. For example, you cannot add a string to a
> `vector` of integers. That example is possible in python, where `list`s are *heterogeneous*.
{: .callout}

To use `std::vector`, we must add `#include <vector>` at the top of our file.

To declare a `std::vector`, we need to put the type in angled brackets. For example, a `std::vector`
of `double` would be declared as `std::vector<double>`.

Then, functions can be used to modify the vector, similar to lists in python

| `std::vector` function | Python `list` function | Description                                          |
| ---------------------- | ---------------------- | ---------------------------------------------------- |
| `[]` operator          | (none)                 | Access an element at an index                        |
| `.at()`                | `[]` operator          | Access an element at an index (with bounds checking) |
| `.push_back(x)`        | `.append(x)`           | Add an element to the end                            |
| `.pop_back()`          | `.pop()`               | Remove & Return the last element                     |
| `.size()`              | `len(list)`            | Get the number of elements in the list/vector        |


Note, however, that C++ does not support the slicing syntax that Python has (with `:`).


Consider the examples in the previous section. We can rewrite that to use a `std::vector` with
the following change

~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl
#include <vector>   // for std::vector


int main(void)
{
    int memsize = 16;

    // A dynamic array of double
    std::vector<double> data;

    for(int i = 0; i < memsize; i++)
    {
        data.push_back(3.1415 * i);
    }

    for(int i = 0; i < data.size(); i++)
    {
        std::cout << "Element " << i << ": " << data[i] << std::endl;
    }

    // Memory is deleted automatically! No need to delete/deallocate
    
    return 0;
}
~~~
{: .language-cpp}

> ## Safely accessing elements of a vector
> An `std::vector` has an `.at()` function which takes an index. This is interchangeable with
> using square brackets `[]`, except the `.at()` function will cause an error if the index
> is beyond the bounds of the vector. Try it out and see!
{: .callout}


## References

While pointers are inherited from C, C++ introduces a new but similar concept - a *reference* type.
References are similar to pointers with a few key differences

1. References must be set to reference an existing object
1. References cannot be NULL/nullptr
1. Once set, references cannot be *re-seated* (made to reference something else)
1. De-referencing is not required

References are created by using `&` with the type. For example,

~~~
std::string my_string = "Hello world!"; // Regular string
std::string & ref_string = my_string; // Reference to my_string
~~~
{: .language-cpp}

However, given the differences above

~~~
std::string & empty_ref; // not valid - reference must point to something

std::string my_string = "Hello world!"; // Regular string
std::string & ref_string = my_string; // Ok, reference to my_string

std::string my_string_2 = "Hello again, world!"; // Regular string
ref_string = my_string_2; // Error - cannot re-seat reference
~~~
{: .language-cpp}

If we go back to our original pointer example, we can see that taking the address
of the reference results int the same address as the pointed-to object.
And, like pointers, the original object can be modified via the reference.

~~~
// pointers.cpp

#include <iostream> // for std::cout, std::endl

int main(void)
{
    int j = 1234;
    std::cout << "Value of j: " << j << std::endl;

    int & rj = j; // rj references j
    std::cout << "Value of rj: " << rj << std::endl;

    std::cout << "Address of j: " << &rj << std::endl;
    std::cout << "Address of rj: " << &rj << std::endl;

    // Change j via rj
    rj = 5678;

    std::cout << "Value changed!" << std::endl;
    std::cout << "New Value of j: " << j << std::endl;
    std::cout << "New Value of rj: " << rj << std::endl;

    return 0;
}
~~~
{: .language-cpp}


~~~
Value of j: 1234
Value of rj: 1234
Address of j: 0x7fff8727380c
Address of rj: 0x7fff8727380c
Value changed!
New Value of j: 5678
New Value of rj: 5678
~~~
{: .output}

References are not often used by themselves, but are very common when passing arguments to
functions (which we will see in the next lesson).
