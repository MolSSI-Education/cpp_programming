---
title: "Homework Assignment #4"
teaching: 0
exercises: 120
questions:
- "What is the homework for the fourth day?"
objectives:
- "Use pytest to write tests for your code."
---

1. Now that you can run your simulation without being in a notebook, you might want a way to save the coordinates.
    - Modify your function `run_mc` so that coordinates are written out to an xyz file at the end of the simulation run.
    - **Bonus** - Modify your `run_mc` function so that coordinates are written every `freq` steps to an xyz file. Use this xyz file to get an averaged RDF.
1. Write tests for the rest of your code.
    - Write unit tests for each function. Remember that unit tests for a function should not rely on any other function. Use `pytest.mark.parametrize` for at least two of your tests.
    - Write a test which verifies that `calculate_total_pair_energy` calculates the correct value for a file from NIST. What kind of test (unit, integration, regression) is this?