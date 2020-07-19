---
title: "Intro to C++"
teaching: 30
exercises: 5
questions:
- "What is the difference between compiled and interpreted languages?"
- "What are the main advantages of C++ vs python?"
- "What are some similarities between python and C++?"
objectives:
- "Explain static and dynamic typing"
- "Explain basic C++ structure and syntax, comparing/contrasting with python"
- "Introduce compiling from the command line"
keypoints:
- "C++ is a statically-type language that involves compiling code"
- "C++ has some superficial similarity to python"
---

> ## Prerequisites
>
> - Installed a C++ compiler
> - Familiarity with command line and text editor
{: .prereq}

## Introduction to C++

C++ is a general-purpose, statically-typed, compiled programming language
designed by Bjarne Stroustrup in the 1980s. It is generally used where
performance is a concern, while still allowing for flexibility. These
constraints can come at a cost, as C++ is widely considered to be a very
deep and complex language.

C++ is generally thought of as a superset of C. That is, most C code is able
to be compiled with a C++ compiler; this is not strictly true, however in
practice most C code is portable to C++.

## Versions of C++

Like Python, C++ has several different versions (called 'standards'). These standards
are designed by a [committee](https://isocpp.org/std/the-committee), with input from
the community.

By now, C++11 (released around 2011) is very common and a recommended base standard
to use. C++14 adds some relatively minor updates and is also generally acceptable.
C++17 is the most recent published standard, and C++20 is currently in development.

C++ standards are designed to be backwards compatible, so that, for example,
C++11 code is acceptable C++17 code. However, new features can create a
different 'feel' to the code, particularly when used side-by-side with older code.

> ## "Modern C++"
>
> C++11 is considered a turning point in the C++ language, with the previous release
> being C++98. Code written in C++11 or later style can look very different compared to
> older code due to the addition of very helpful features and paradigms. This is sometimes
> called "modern C++" although there is no precise definition.
{: .callout}


## Compiled languages

C++ is a compiled language. This means that the code you write is compiled
into binary format by means of a compiler. This binary code is more
directly interpretable by your computer's processor without further need for
translation. Compiling also allows for optimization not generally possible
in interpreted languages such as python.


The figure below shows a small C function for computing the volume of
a sphere.  On the right is the corresponding binary code (in green) that
has been disassembled into a more human-readable format (purple and white).

<center><img src='../fig/cpp/disassembly.png'></center>

In interpreted/dynamic languages such as python, the translation from source code to
binary happens implicitly every time the function is used, whereas in compiled languages
this only happens at compile time, allowing for much more performant programs.

There is a cost to compilation, though. In python, for example, you may edit
your code and then immediately run it to see what the effect of your change
was. Compiled languages require the intermediate compilation step, resulting
in a longer feedback loop between code modification and running or testing.

### Compilation and linking

There are two steps to creating a program or library from source code: compiling and linking.
It is common to use 'compilation' to mean both steps, although it may be technically incorrect; the
word 'building' is also used to refer to a combination of compiling and linking.

Compilation refers to the transformation of source code files to machine code,
as shown in the figure above.  Then, the `linker` combines all the compiled
data (including external libraries) into a single library or executable.

> ## Compilers and linkers
>
> The above is even more confusing as compilers will often call the linkers for you
> on the command line, often by default.
{: .callout}

<center><img src='../fig/cpp/compilation.png'></center>

There are several different compilers that you may run across. These include the [GNU Compiler Collection (GCC)](https://gcc.gnu.org), [Clang](http://clang.org/), and [Intel compilers](https://software.intel.com/content/www/us/en/develop/tools/compilers/c-compilers.html).

Complex projects are often compiled/linked with a build system (such as
[CMake](https://cmake.org)). These build systems can simplify building software
with lots of interconnected pieces, even written in multiple programming
languages. A discussion of build systems is beyond the scope of these lessons;
our code can be build with a few commands saved inside a bash script.


## Static typing

One major difference between Python and C++ is that C++ is
statically-typed. This means that variables are given a type and that type
cannot change.

In C/C++, the types of variable is given before the variable name

~~~
int i = 10;
std::string s = "Hello";
~~~
{: .language-cpp}

>## Types in python
>
>Python does have types, although they are not declared the same way as in C++,
>and are often determined implicitly.
>
>Try running the following in a jupyter notebook or in a python script
>
>~~~
>a = "This is a string"
>print(type(a))
>b = 10
>print(type(b))
>~~~
>{: .language-python}
{: .callout}


Once set, a variable's type cannot be changed. For example, the following python code is valid:

~~~
def hello_world():
    to_print = "Hello, world!" # to_print is a str
    print(to_print)

    to_print = 12345; # to_print is now an int
    print(to_print)
~~~
{: .language-python}

But an equivalent C++ code is not valid

~~~
void hello_world()
{
    std::string to_print = "Hello, world!" // OK
    to_print = 10; // Error: Assigning an integer to a string type
    int to_print = 10; // Error: to_print is already delcared to be a string!
}
~~~
{: .language-cpp}

Some types can be implicitly converted (such as between `int` and `double`). This can be
the source of bugs, and compilers can be made to warn about such conversions.



## Basic C++ Syntax

The syntax of C++ can look similar to that of python, with a few differences.

1. Single-line comments start with `//`. Multi-line comments start with `/*` and end with `*/`
1. Whitespace is not significant
1. Statements end with a semicolon (;)
1. Rather than whitespace, statements are grouped together with curly braces
1. A type is specified before the first use of a variable (including in a function argument)
1. The return type of a function is specified before the function name; the function must return
   an object of that type and only that type.

Here is the function for calculating the volume of a sphere in both Python and C++. Don't worry if
you don't understand all the differences - we will explain more as the course goes on.


~~~
#include <cmath>

double sphere_volume(double radius)
{
    const double pi = acos(-1);
    return (4.0/3.0) * pi * pow(radius, 3); 
}
~~~
{: .language-cpp}


~~~
import math

def sphere_volume(radius)
{
    pi = math.acos(-1);
    return (4.0/3.0) * pi * radius**3; 
}
~~~
{: .language-python}



Some common C++ types and their similar types in python are given in the table below.
Please note, however, that these are only rough equivalences and there are many
nuances not captured here.

| C++ Type           | Python Type | Description                          |
| ------------------ | ----------- | ------------------------------------ |
| int                | int         | Decimal number (not floating point)  |
| double             | float       | Floating point number (64-bit)       |
| std::string        | str         | String of characters                 |

