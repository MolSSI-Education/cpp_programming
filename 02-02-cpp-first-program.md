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

## Functions

~~~
// hello_world.cpp

#include <iostream>

void say_hello(std::string name)
{
    std::cout << "Hello, " << name << "!" << std::endl;
}

int main(void)
{
    say_hello("Ben");
    say_hello("Hans");
    return 0;
}
~~~
{: .language-cpp}

## Conditional statements

~~~
// hello_world.cpp

#include <iostream>

void say_hello(std::string name)
{
    if(name == "Ben")
    {
        std::cout << "Hello, " << name << " (from Berkeley)" << std::endl;
    }
    else if(name == "Hans")
    {
        std::cout << "Hello, " << name << " (from MolSSI)" << std::endl;
    }
    else
    {
        std::cout << "Hello, " << name << " (from parts unknown)" << std::endl;
    }
}

int main(void)
{
    say_hello("Ben");
    say_hello("Hans");
    return 0;
}
~~~
{: .language-cpp}

## For loops

~~~
// hello_world.cpp

#include <iostream>

void say_hello(int i, std::string name)
{
    std::cout << i << ".) Hello, " << name << "!" << std::endl;
}

int main(void)
{
    for(int i = 0; i < 10; i++)
    {
        say_hello("Ben");
    }
    return 0;
}
~~~
{: .language-cpp}


## Pitfall for Python programmers

In C++, conditional statements and `for` loops introduce a new scope. Variables
declared within these scopes cannot be accessed outside of the conditional
or loop. This is allowed in Python, and is a common mistake in programmers moving
from Python to C++.

~~~
int main(void)
{
    for(int i = 0; i < 10; i++)
    {
        std::string name = "Ben";
        say_hello("Ben");
    }

    // Error: name is undeclared here!
    std::cout << "Bye, " << name << std::endl;
    return 0;
}
~~~
{: .language-cpp}


## Compiler Errors

If there is a problem with your code, the compiler will print an elaborate
error message. Reading can take experience and intuition, as the message is
not always the most helpful (although things are improving).

As a rule of thumb, I usually follow this workflow when dealing with errors:

1. Read the output from top to bottom (may require scrolling up in your terminal)
1. Fix the first error found
1. Build again
1. If more errors, go to step 1.

Often, a single error will cascade, causing more errors further down in the
code. It is possible that what looks like many errors are caused by a single
small error. Therefore, fixing errors one-at-a-time and then rebuilding is
usually a good approach, especially as you begin building experience. With
time, you will be able to spot batches of errors that can be fixed at once.
 
> ## C++ Compiler Error Contests
> C++ compilers are known having very verbose, incomprehensible error messages. There
> are contests to see how you can get the longest error messages with the shortest
> amount of code.
{: .callout}

Take your original `hello_world.cpp` and introduce a few errors that you may
inadvertantly run across (if you haven't done so already). This will help you
recognize what the compiler is telling you in the future. Some good things to
mess up are:

1. Remove a semicolon
1. Remove the `#include` line
1. Misspell `iostream`, `std::cout`, or `main`
1. Forget the second `<<` (so that it reads `std::cout << "Hello, world!" std::endl`)

