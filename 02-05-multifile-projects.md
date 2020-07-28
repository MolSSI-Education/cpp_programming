---
title: "Multi-file projects"
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

### Create two source files

We will create two source files. One will contain `main` and the other our
`convert_temperature` function.

~~~
// main.cpp

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
~~~
{: .language-cpp}

~~~
// temperature.cpp

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
~~~
{: .language-cpp}

> ## Includes in multi-file projects
> Do we need to include `iostream` in `temperature.cpp`? Why not?
{: .callout}

Now, compile these two source files by specifying them both on the
command line:

~~~
g++ main.cpp temperature.cpp -o temperature
~~~
{: .language-bash}

This should compile just fine. Remember, we still have the forward declaration
of `convert_temperature` at the top of `main.cpp`. The second step is to move that
to a header file, `temperature.hpp`.


~~~
// temperature.hpp

#include <vector>

std::vector<double> convert_temperature(const std::vector<double> & temperatures);
~~~
{: .language-cpp}


And now, we can `#include` our new header file into `main.cpp`.

~~~
// main.cpp

#include <iostream> // for std::cout, std::endl
#include <vector>
#include "temperature.hpp"

int main(void)
...
~~~
{: .language-cpp}

Note the convention in include files: Files that are part of the current
project are specified with quotation marks, and those from other libraries
are specified with angled brackets.  There is some effect in how compilers
will find those headers, but it is also a signal to other developers reading
your code there those headers files might be located.

### Include guards

The header file shown above is missing something very important. While this
particular example won't cause any problems, larger projects can have issues
arise due to circular dependencies. The main problem that occurs is a header
file that can get included twice from a single source file.

There are two ways to prevent this. The simplest way is to include `#pragma
once` at the top of your header file. While not standard, it is supported
by pretty much any compiler you will use in scientific computing.



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
1. Don't forget include guards (in header files only)
1. Good naming of files is important. Pick a convention (including extension) and stick with it.
1. As a general rule of thumb, there is one header file for each source file (except main). This is very general, though, and there are many exceptions.
