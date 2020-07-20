---
title: "Function Calling and Arguments"
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

## Function overview

Like in python, functions are a fundamental building block in C++.

Let's refresh our memories of some of the terminology around functions. Note that
this terminology is the same as in Python.

<center><img src='../fig/cpp/function_terms.png'></center>

## The `void` Keyword

`void` can be used in the signature to signify a function takes zero arguments,
and can also be used as the return type to signify the function does not
return anything. See, for example, our `main` function.

## The `const` Keyword

In C/C++, it is possible to mark a variable as constant, such that it
cannot be changed accidentially. Marking a variable as constant can also
enable some performance improvements, although these are typically very small.

To do so, the keyword `const` is placed before the type.

~~~
const std::string my_string = "Hello world!"; // Constant string
my_string = "Another string"; // Error - my_string is const!
~~~
{: .language-cpp}

Constant variables like this can be useful in some cases. For example,
for storing physical constants or common strings. As we will see, they are
most often used for declaring objects passed to functions as `const`.

## Argument passing by copy

By default, arguments are passed into functions by copying the contents.
The two main things to remember about passing via copying is that

1. This can be expensive for large data types (think `vector` with many elements)
1. Changes made to the data within the function are not reflected outside

This last point can be a benefit, however, and can protect you from
making accidental mistakes.

~~~
// temperature

#include <iostream> // for std::cout, std::endl
#include <vector>

std::vector<double> convert_temperature(std::vector<double> temperatures)
{
    std::vector<double> new_temperatures;
    for(size_t i = 0; i < temperatures.size(); i++)
    {
        new_temperatures.push_back(temperatures[i]*1.8+32);
    }

    return new_temperatures;
}

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    std::vector<double> new_temperatures = convert_temperature(temperatures);

    for(size_t i = 0; i < new_temperatures.size(); i++)
    {
        std::cout << "Temperature " << i << ": " << temperatures[i] << " C = " << new_temperatures[i] << " F" << std::endl;
    }
    
    return 0;
}
~~~
{: .language-cpp}



## Argument passing by reference

Passing by reference is very common in C++. In general, this should be
the default way to pass in data the will be changed in the function.
In addition, `const` references are used when passing in large
objects to prevent copying of large amounts of data (think matrices).

~~~
#include <iostream> // for std::cout, std::endl
#include <vector>

void convert_temperature(std::vector<double> & temperatures)
{
    for(size_t i = 0; i < temperatures.size(); i++)
    {
        temperatures[i] = temperatures[i]*1.8+32;
    }   
}

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    convert_temperature(temperatures);

    for(size_t i = 0; i < temperatures.size(); i++)
    {   
        std::cout << "New Temperature " << i << ": " << temperatures[i] << " F" << std::endl;
    }   
        
    return 0;
}
~~~
{: .language-cpp}

### Passing by constant reference

Passing by constant reference allows for larger amounts of data to be passed
without incurring a performance penalty due to copying. This is a very
common idiom that you will internalize very quickly.

Making argument a `const` reference also ensures that the object cannot
be changed within the function.


~~~
// temperature

#include <iostream> // for std::cout, std::endl
#include <vector>

std::vector<double> convert_temperature(const std::vector<double> & temperatures)
{
    std::vector<double> new_temperatures;
    for(size_t i = 0; i < temperatures.size(); i++)
    {
        new_temperatures.push_back(temperatures[i]*1.8+32);
    }

    return new_temperatures;
}

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    std::vector<double> new_temperatures = convert_temperature(temperatures);

    for(size_t i = 0; i < new_temperatures.size(); i++)
    {
        std::cout << "Temperature " << i << ": " << temperatures[i] << " C = " << new_temperatures[i] << " F" << std::endl;
    }
    
    return 0;
}
~~~
{: .language-cpp}

> ## Comparison to Fortran
> A `const` reference in a Function signature is similar to
> an argument being declared as `intent(in)` in Fortran. A plain non-`const`
> reference would be similar to `intent(inout)`.
{: .callout}


## Argument passing by pointer

Not recommended in most C++ code now. You can pass in the address of an object
and then directly access (via `*`) the contents. This is very
awkward for many types. Mainly reserved for passing in memory
allocated with `new`.

~~~
#include <iostream> // for std::cout, std::endl
#include <vector>

void convert_temperature(std::vector<double> * temperatures)
{
    for(size_t i = 0; i < (*temperatures).size(); i++)
    {
        (*temperatures)[i] = (*temperatures)[i]*1.8+32;
    }   
}

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    convert_temperature(&temperatures);

    for(size_t i = 0; i < temperatures.size(); i++)
    {   
        std::cout << "New Temperature " << i << ": " << temperatures[i] << " F" << std::endl;
    }   
        
    return 0;
}
~~~
{: .language-cpp}


## Const correctness

As we have seen, a function that takes a `const` reference can accept both
`const` and non-`const` objects. However, a function taking a non-`const`
reference is restricted to only taking non-`const` objects. This leads to
a general rule of writing function arguments: **make your reference (and
pointer) arguments const unless you do intend to modify them**. This way,
your function is usable in all cases, since the calling function may have
`const` data it needs to pass in.

This thinking is called *const correctness*. If you do not do this, you will
eventually write yourself into a corner and will find yourself refactoring
your entire project. So it is much easier to start thinking about `const` from
the very beginning. After some experience, it will be second nature.


## Making a choice

So now we have several ways to pass arguments to a function. Which should we use?

* Dynamically allocated memory? Pointer is basically your only option
    * Not *exactly* true in C++11
* The function does not modify the argument
    * **Plain data type (int, double)** - pass by copy
    * **Small data structure (some strings)** - pass by copy
        * Can make code more safe with only a small performance hit
    * **Larger data structure (vector, string)** - constant reference
* The function modifies the argument
    * Generally, use a (non-const) reference

## Function overloading

Now we come to a feature of C++ that is not available in C or in Python.
In C++, we can have multiple functions with the same name, as long
as the function signature is different. The compiler will determine
which function will be used based on the arguments passed to the function.

For example, we can have to `convert_temperature` functions - one that takes
just a `double`, and the other that takes a `std::vector<double>`.

~~~
// temperature

#include <iostream> // for std::cout, std::endl
#include <vector>

std::vector<double> convert_temperature(const std::vector<double> & temperatures)
{
    std::vector<double> new_temperatures;
    for(size_t i = 0; i < temperatures.size(); i++)
    {
        new_temperatures.push_back(temperatures[i]*1.8+32);
    }

    return new_temperatures;
}

double convert_temperature(double temperature)
{
    return temperature * 1.8 + 32;
}

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    // Calls convert_temperature with a vector
    std::vector<double> new_temperatures = convert_temperature(temperatures);

    // Calls convert_temperature with a double
    double new_temperature_d = convert_temperature(1.234);

    return 0;
}
~~~
{: .language-cpp}


> ## Argument-dependent lookup
> The process by which C++ determines the proper function to call based
> on arguments is extremely complex. It has to take into account
> not just argument types, but (implicit) conversions, custom conversions,
> and more advanced C++ features such as templates. In general, the compiler
> will do the correct thing, and if there is ambiguity, will require further
> clarification on your part.
{: .callout}


## Forward declarations

Up until now, we have we have placed all of our functions in a single file.
In real projects, this is very rarely the case.

We now have to introduce a little more terminology around functions.

1. A *function declaration* declares the existance of a function with
   a particular name and signature.
2. A *function definition* contains the actual code of the function.

C++ has a rule called the *one definition rule* - every function can only
be defined once. However, you can *declare* a function as many times as you like.

<center><img src='../fig/cpp/function_terms_2.png'></center>

Note that the declaration has a semicolon at the end. This is likely something
you will forget at some point (I certainly still do).

Take for example our temperature conversion function that takes a constant
reference. If we place the `convert_temperature` function below the main function,
it will not compile.

~~~
// temperature

#include <iostream> // for std::cout, std::endl
#include <vector>


int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    std::vector<double> new_temperatures = convert_temperature(temperatures);

    for(size_t i = 0; i < new_temperatures.size(); i++)
    {
        std::cout << "Temperature " << i << ": " << temperatures[i] << " C = " << new_temperatures[i] << " F" << std::endl;
    }
    
    return 0;
}


std::vector<double> convert_temperature(const std::vector<double> & temperatures)
{
    std::vector<double> new_temperatures;
    for(size_t i = 0; i < temperatures.size(); i++)
    {
        new_temperatures.push_back(temperatures[i]*1.8+32);
    }

    return new_temperatures;
}
~~~
{: .language-cpp}

~~~
test.cpp: In function ‘int main()’:
test.cpp:14:44: error: ‘convert_temperature’ was not declared in this scope; did you mean ‘new_temperatures’?
   14 |     std::vector<double> new_temperatures = convert_temperature(temperatures);
      |                                            ^~~~~~~~~~~~~~~~~~~
      |                                            new_temperatures
~~~
{: .output}

(Your error message may vary depending on compiler and version). The reason for this error is that the
compiler is compiling your source file from the top down. When compiling the `main` function,
it runs into a call to `convert_temperature`. In order to properly compile `main`, it needs
to know the signature and return type of `convert_temperature`, however it does not have that information yet;
the definition is at the bottom of the file.

To fix this, we can *forward declare* the function above the `main` function.

~~~
// temperature

#include <iostream> // for std::cout, std::endl
#include <vector>

// Forward declaration of convert_temperature
std::vector<double> convert_temperature(const std::vector<double> & temperatures);

int main(void)
{
    std::vector<double> temperatures;
    temperatures.push_back(0.0);
    temperatures.push_back(-40.0);
    temperatures.push_back(123.4);

    std::vector<double> new_temperatures = convert_temperature(temperatures);

    for(size_t i = 0; i < new_temperatures.size(); i++)
    {   
        std::cout << "Temperature " << i << ": " << temperatures[i] << " C = " << new_temperatures[i] << " F" << std::endl;
    }   
     
    return 0;
}


std::vector<double> convert_temperature(const std::vector<double> & temperatures)
{
    std::vector<double> new_temperatures;
    for(size_t i = 0; i < temperatures.size(); i++)
    {   
        new_temperatures.push_back(temperatures[i]*1.8+32);
    }   

    return new_temperatures;
}
~~~
{: .language-cpp}


## Header files

Forward declaration in your source file is sometimes necessary to avoid
circular dependencies.  However, what if you have multiple source files that
require a function declaration? You could manually place the declaration in
each source file, however this is noisy and error-prone.

To solve this, *header files* are used. One purpose of header files is to
declare a set of functions (and more) for source files to use. This serves
a few purposes:

1. Prevents clutter in your source files
1. Small changes to function signatures only need to be made in two places (header and definition).
1. Exposes the minimum amount of information to users of functions (think libraries)
1. Good place for documentation!

Source files will the `#include` the header file and get the function
definitions. `#include` basically takes the contents of the header file and
inserts it into your source before compiling.  There are compiler options
to even show you the result of this, which can be helpful for debugging
esoteric compiler errors.

> ## Compiling vs. linking
> Remember the discussion on the first day about compiling vs. linking? This is where the
> difference should become more clear. When compiling, you only need to have a function *declaration*.
> Then, when linking, the actual connection between the calling code and the function *definition*
> is made. That is also why there can only be one definition - if there were more, which one should
> the linker choose?
{: .callout}


So lets split our project into three files - one containing `main`, one
containing our `convert_temperature` function, and a header file for our
`convert_temperature` functions.


### A note about file extensions

As mentioned before, file extensions in C++ are not completely standardized. This is also true for
header files. I use the convention of `.cpp` for source, `.hpp` for C++ header. You will often find
`.h` for C++ header, although I typically interpret that as a C header.

Up until now, we have benn using `#include <vector>` and `#include <iostream>`. `vector` and `iostream`
are actually header files with no extension! For whatever reason, the C++ standard library chose
the convention of no extention. This is also done with some libraries, too (you may use
the [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) matrix/vector library at some point,
which adopts this convention).


## Common mistakes in multi-file projects

I have run across several common mistakes when learning about multi-file projects.
This include mistakes I made way back when first dealing with header files. So here
are some rules to keep in mind:

1. NEVER `#include` a source file (ending with `.cpp` or `.c`).
    * Source files can include header files
    * Header files can include header files.
1. You do not compile or link header files. You only compile source files (which pull header files in).
1. Good naming of files is important. Pick a convention (including extension) and stick with it.
1. In general, there is one header file for each source file (except main). This is very general, though.
