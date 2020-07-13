---
title: "First C++ Program"
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
>
> - Installed a C++ compiler
> - Familiarity with command line and text editor
{: .prereq}

## Hello world in C++

Now, lets begin by writing our first C++ program, and the compiling and running it.

First, we will begin with a program that does absolutely nothing. Start by
creating a new directory for your C++ project, and creating a plain text file
with the name `hello_world.cpp`.

> ## File extensions
> C++ source files commonly end in `.cpp`, which is the convention we will use. However,
> other people have different conventions: `.cxx` is other common extension. Compilers will
> generally understand either extension to mean C++ source
{: .callout}

~~~
// hello_world.cpp

int main(void)
{
    return 0;
}
~~~
{: .language-cpp}

This is basically the bare minimum of a C++ program. Lets take a look at this in detail

1. This defines a function called `main`. It returns an `int` (integer) and takes no arguments (`void`)
1. The *function body* is enclosed in curly braces.
1. It simply returns the integer 0 to outside the function

the `main` function is very important. It defines the first function that is called by the operating
system. That is, by running the executable we will create, the `main` function is the starting point of our code.

### Building our executable

Save the `hello_world.cpp` file. We will now build our executable. In general,
it is easier to have two windows open - one with your code editor, and one
for compiling.

In whatever terminal you are going to use to build your executable, we are going to run
the following command:

~~~
g++ hello_world.cpp -o hello_world
~~~
{: .language-bash}

The command is `g++`, which will both compile and link your code. `-o hello_world` specifies the
final output name for our executable.

If successful, there will be no output, and there will be a new file in the directory
called `hello_world`.

If you try running `hello_world`, nothing will happen! That is because we don't have
any code that does anything.

Now lets make our code actually do something

### Printing to the standard output

Unlike python, C++ does not have a built-in way of printing to the terminal! To do so, you must
use an external library. Fortunately, the C++ standard specifies the *C++ Standard Library*, containing
many useful functions, including printing.

In order to use features of the standard library, you must `#include` the appropriate files. This is
conceptually similar to the Python `import` statement - it allows you to use code defined somewhere
else in the current file.

Printing to standard output is done via the `std::cout` object (pronounced
*see-out*) and by using the `<<` (stream insertion) operator. These are
found in the `iostream` file that is part of the standard library.

> ## Namespaces
> You will notice that when we use pieces of the standard library, we will prefix them
> with `std::`. C++ allows you to group functions, etc, into *namespaces*. These are conceptually
> similar to python modules (with many differences of course). You tell the compiler which namespace
> a function or object belongs to with the `::` operator (called the *scope resolution operator*).
> Details about namespaces are beyond the scope of this course.
{: .callout}

~~~
// hello_world.cpp

#include <iostream>

int main(void)
{
    std::cout << "Hello, world!";
    return 0;
}
~~~
{: .language-cpp}

Now save and build this file the same way you did before. Running it will
now print `Hello, world!` to the terminal, but you will notice that it does
not put a newline afterwards, causing it to be on the same line as your prompt.

The solution to this is to place and endline character after the `Hello,
world!`. There are several different ways to do this, but we will stick with
using `std::endl` from the standard library.

~~~
// hello_world.cpp

#include <iostream>

int main(void)
{
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
~~~
{: .language-cpp}

Notice that we can keep inserting into `std::cout` by chaining together `<<` operators.

If you build and run this executable again, the output should go where you expect.
