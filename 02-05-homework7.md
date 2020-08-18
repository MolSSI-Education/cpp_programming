---
title: "Homework Assignment #7"
teaching: 0
exercises: 60
questions:
- "What's the homework for day 7?"
objectives:
- "Use Monte-Carlo integration to compute an integral in C++"
keypoints:
- "TODO"
---

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## Individual Homework Assignment

This should be completed by each group member individually (but you can
discuss with your group). **Individual assignments are due one week after
being assigned**. The due date for this assignment is **August 25, 2020**.

Your individual assignment is to rewrite the first homework in C++.
In that assignment, you wrote a python script to estimate the value
of the integral

$$ \int_{0}^{1} \frac{1}{1 + x^2} dx $$

which evaluates to $$\frac{\pi}{4} $$.

In addition, we would like you to time three different versions of
the code.

1. The python version from Homework 1.  This will necessitate moving the
python version out of the Jupyter notebook and into a standalone script,
which can be run on the command line.

1. The C++ version with disabled optimizations `-O0` (see below)

1. The C++ version with aggressive optimizations `-O3` (see below)

### Timing your code

We would like you to time your code for 10,000,000 iterations. To do
this, there is a simple `time` utility installed on your operating
system (or under WSL). To time your command, add `time` before
the command:

~~~
[ben@mobilefort less]$ time ./my_executable 
Some program output here

real	0m0.141s
user	0m0.134s
sys	0m0.007s
~~~
{: .bash}

To time a python script, you may just add time before the `python` or `python3`
command.

~~~
(base) [ben@mobilefort less]$ time python time_me.py 

real	0m4.124s
user	0m0.032s
sys	0m0.015s
~~~
{: .bash}

The output includes three numbers; these are reported as minutes, and then
seconds (with fractions). You should report the 'real' time as the time
it took to run your code. Report the average of three runs of each version
of your code.

### C++ Optimization Levels

Until now, we have been compiling with the default optimizations of
the compiler. However, for this exercise, we should enable aggressive
optimizations. To do so, add `-O3` (that is a capital letter O) to the
compilation command line. Optimizations can be disabled with `-O0`.

~~~
g++ -O3 montecarlo.cpp -O montecarlo
~~~
{: .bash}


### Random numbers in C++

You will be given the function for generating random doubles in C++, as it
is a bit trickier to do in C++ than python.

### Integer Division in C++

In python, dividing to integer types with `/` results in a float. This is
not true in C++, where dividing two integers results in an integer. You will
need to *cast* integers to doubles.

~~~
int some_int = 1.0;
double as_double = static_cast<double>(some_int);
~~~
{: .language-cpp}

This is called a *static cast*, and is one of the several different kinds
of casting done in C++.  The desired type is placed in the angled brackets,
and the variable you are casting from is placed in parentheses.
