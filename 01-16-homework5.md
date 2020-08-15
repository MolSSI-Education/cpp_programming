---
title: "Homework Assignment #5"
teaching: 0
exercises: 120
questions:
- "What is the homework for the fifth day?"
objectives:
- "Rewrite simulation to use NumPy"
- "Compare the timing of the two versions of code."
keypoints:

---

## Individual Assignment #2

For the second individual assignment, you will be comparing results from Monte Carlo simulations to tabulatd experimental values reported by NIST. Start your assignment by clicking [this link](https://classroom.github.com/a/EyklTlS7).

## NumPy Monte Carlo

Take an hour or so to discuss with your teammates what changes you can make to your code to take advantage of the features of NumPy arrays. For this portion, take the first 30 minutes to think about this by yourself, then discuss for half an hour with your group. We will tell you one option for rewriting your code at the end of this discussion period.

Consider each function in our code - which ones could benefit from using numpy arrays? Consider each function and include thoughts for each one in your blog post for today.

> ## NumPy Monte Carlo
> Expand this section for an explanation of functions which should be rewritten.
>> ## Answer 
>> The two functions you should rewrite are :
>> 1. `calculate_total_energy`
>> 1. `calculate_pair_energy`
>> 
>> You will also need to modify some other functions like `calculate_LJ` slightly so we can use numpy arrays. Out of the two listed functions, `calculate_pair_energy` will result in the greatest performance increase. This is because we call it twice per simulation step. The other function `calculate_total_energy` is only called once at the beginning of the simulation.
> {: .solution}
{: .challenge}


