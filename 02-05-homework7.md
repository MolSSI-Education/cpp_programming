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
being assigned**. The due date for this assignment is **August 24, 2020**.

Your individual assignment is to rewrite the first homework in C++.
In that assignment, you wrote a python script to estimate the value
of the integral

$$ \int_{0}^{1} \frac{1}{1 + x^2} dx $$

which evaluates to $$\frac{\pi}{4} $$.

### Some considerations

You will be given the function for generating random doubles in C++, as it
is a bit trickier to do in C++ than python.

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
