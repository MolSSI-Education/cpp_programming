---
title: "Python Testing using pytest"
teaching: 60
exercises: 10
questions:
- "How is a Python module tested?"
objectives:
- "Explain the overall structure of testing."
- "Explain the reasons why testing is important."
- "Understand how to write tests using the pytest framework."
keypoints:
- "A good set of tests covers individual functions/features __and__ behavior of the software as a whole."
- "It's better to write tests during development so you can check your progress along the way. The longer you wait to start the harder it is."
- "Try to test as much of your package as you can, but don't go overboard, most packages don't have 100% test coverage."
---

Until now, we have been writing functions and checking their behavior using assert statements. While this seems to work, it can be tedious and prone to error. In this lesson, we'll discuss how to write tests and run them using the `pytest` testing framework.

Using a testing framework will allow us to easily define tests for all of our functions and modules, and to test these each time we make a change. This will ensure that our code is behaving the way we expect, and that we do not break any features existing in the code by making new changes.

This episode explains the importance of code testing and demonstrates the possible capabilities.

## Why testing

Software should be tested regularly throughout the development cycle to ensure correct operation. Thorough testing is typically an afterthought, but for larger projects, it is essential for ensuring changes in some parts of the code do not negatively affect other parts.

**Software testing is checking the behavior of part of the code (such as a method, class, or a module) by comparing its expected output or behavior with the observed one.** We will explain this in more detail shortly.

## Unit vs Regression vs Integration testing

There are three main levels of testing:

- **Unit tests**: the purpose is to verify that each part of the code is functioning as expected.
Unit testing is done on smaller units (such as single functions or classes) as you work on
your code.
This is helpful for catching errors in uncommonly-used parts of the code. Unit tests should
be added as new features are added, resulting in better code coverage.
In unit tests, you are testing a part of your code independent of any other factors;
therefore, you should avoid using the file system, databases, network, or any other
resources unless you are testing a function directly related to that resource.

- **Integration tests**: this is a more holistic approach where you test the interface
between modules, and how they combine and integrate together.

- **System tests**: where you test your system as a whole to check if it meets all the
requirements.

Another important type of testing is **Regression tests**. In Regression tests,
given a known input, does the software correctly and consistently return the correct
values? This kind of testing can catch problems in previously working code that may has been broken by new changes or new features.

It is highly encouraged to have Unit tests that *cover* most of your code. It is
also helpful to have some Integration and System tests.

In this lesson, we are focusing on unit testing.
Same concepts here can be applied to perform Integration tests across modules.

## The pytest testing framework

MolSSI recommends using the [pytest](https://pytest.org) testing framework.
Other testing frameworks are available (such as unittest and nose tests);
however, the combination of easy implementation, [parametrization of tests](https://docs.pytest.org/en/latest/parametrize.html),
[fixtures](https://docs.pytest.org/en/latest/fixture.html), and [test marking](https://docs.pytest.org/en/latest/example/markers.html)
make `pytest` an ideal testing framework.

If you don't have `pytest` installed or it's not updated to version 3, install it using:
~~~
$ pip install -U pytest-cov
~~~
{: .bash}

### Running our first test

When we run `pytest`, it will look for directories and files which start with `test` or `test_`. It then looks inside of those files and executes any functions that begin with the word `test_`. This syntax lets pytest know that these functions are tests. If these functions do not result in an error, `pytest` counts the function as passing. If an error occurs, the test fails.

Create a directory called `tests` in the top level of your project (the same directory as your `mcsim` folder). In your `tests` directory, create an empty file called `test_coord.py`.  It is a good idea to separate your tests into different modules based on which python file you will be testing.

Let's write a test for our `calculate_distance` function.

Add the following contents to the `test_coord.py` file.

~~~
"""
Tests for the coord module
"""
import sys

# Add our folder to the system path so python can find our code.
current_location = os.path.dirname(__file__)
sys.path.append(os.path.join(current_location, '..'))

from mcsim.coord import calculate_distance
~~~
{: .language-python}

In this first part of the file, we are getting ready to write our test. The first line imports the `sys` python package. Then, we add our directory to the system path. The system path is where Python looks for packages when you do an import. By using this code, we will be able to access the modules in our `mcsim` folder.

Next, we write our test. When you write a test to be run with `pytest`, it must be inside a function that begins with the word test. We set up our calculation, then we use the function we are testing and compare the output with our expected output. This is very similar to what we did before with our `assert` statements, except that it is now inside of a function:

~~~
def test_calculate_distance():
    point_1 = [0, 0, 0]
    point_2 = [1, 0, 0]

    expected_distance = 1
    dist1 = calculate_distance(point_1, point_2)
    
    assert dist1 == expected_distance
~~~
{: .language-python}


We can run this test using `pytest` in our terminal. In the folder containing both `mcsim` and `tests`, type

~~~
$ pytest
~~~
{: .language-bash}

You should see an output similar to the following.

~~~
==================== test session starts ====================
platform darwin -- Python 3.7.6, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
rootdir: /Users/jessica/lesson-follow/monte-carlo-lj
plugins: cov-2.10.0
collected 1 item                                            

tests/test_coord.py .
~~~
{: .output}

Here, `pytest` has looked through our directory and its subdirectories for anything matching `test*`. It found the `tests` folder, and within that folder, it found the file `test_coord.py`. It then executed the function `test_calculate_distance` within that module. Since our `assertion` was `True`, our test did not result in an error and the test passed.

We can see the names of the tests `pytest` ran by adding a `-v` tag to the pytest command.

~~~
$ pytest -v
~~~
{: .language-bash}

Using the command argument `-v` will result in pytest listing which tests are executed and whether they pass or not. There are a number of
additional command line arguments to [explore](https://docs.pytest.org/en/latest/usage.html).

~~~
==================== test session starts ====================
platform darwin -- Python 3.7.6, pytest-5.4.2, py-1.8.1, pluggy-0.13.1 -- /Users/jessica/miniconda3/envs/molssi_best_practices/bin/python3.7
cachedir: .pytest_cache
rootdir: /Users/jessica/lesson-follow/monte-carlo-lj
plugins: cov-2.10.0
collected 1 item                                            

tests/test_coord.py::test_calculate_distance PASSED   [100%]
~~~
{: .output}

Now we see that `pytest` displays the test name for us, as well as `PASSED` next to the test name.

### Failing tests
Let's see what happens when a test fails.

In case of test failure, Pytest will show detailed output from doing its own analysis to discover the error by inspecting your objects at runtime. Change the value of the `expected` variable in your test function to `2` and rerun the test.

~~~
$ pytest -v
~~~
{: .language-bash}

~~~
==================== test session starts ====================
platform darwin -- Python 3.7.6, pytest-5.4.2, py-1.8.1, pluggy-0.13.1 -- /Users/jessica/miniconda3/envs/molssi_best_practices/bin/python3.7
cachedir: .pytest_cache
rootdir: /Users/jessica/lesson-follow/monte-carlo-lj
plugins: cov-2.10.0
collected 1 item                                            

tests/test_coord.py::test_calculate_distance FAILED   [100%]

========================= FAILURES ==========================
__________________ test_calculate_distance __________________

    def test_calculate_distance():
        point_1 = [0, 0, 0]
        point_2 = [1, 0, 0]
    
        expected_distance = 2
        dist1 = calculate_distance(point_1, point_2)
    
>       assert dist1 == expected_distance
E       assert 1.0 == 2
E         +1.0
E         -2

tests/test_coord.py:23: AssertionError
~~~
{: .output}

Pytest shows a detailed failure report, including the source code around the failing line. The line that failed is marked with `>`.
Next, it shows the values used in the assert comparison at runtime, that is ` 1.0 == 2`. This runtime analysis is one of the advantages of pytest that help you debug your code.

> ## Check Your Understanding
> What happens if you leave your `expected_value` equal to 2, but remove the assertion? Change your assertion line to the following
> ~~~
> expected_distance == calculated_distance
> ~~~
> {: .language-python}
>  
>  -
>> ## Answer
>> If you remove the word `assert`, you should notice that your test still passes. This is because the expression evaluated to `False`, but since there was no Assertion, there was no error. Since there was no error, pytest counted it as a passing test. The `assert` statement causes an error when it evaluates to False.
>>
>> It's very important to remember that `pytest` counts a test as failing when some type of exception occurs.
> {: .solution}
{: .challenge}

Change the expected value back to 1 so that your tests pass and make sure you have the `assert` statement.

> ## Exercise
> Create a second test for the `calculate_distance ` function. Use the following points and box length:
> ~~~
> r1 = [0, 0, 0]
> r2 = [0, 0, 8]
> box_length = 10
> ~~~
> {: .language-python}
>
> With periodic boundaries, these points correspond to a distance of 2.
> 
> Verify that your test is working by running pytest. You should now see two passing tests.
>> ## Solution
>> ~~~
>> def test_calculate_distance2():
>>     point_1 = [0, 0, 0]
>>     point_2 = [0, 0, 8]
>>     box_length = 2
>>
>>     expected_distance = 2
>>     dist1 = calculate_distance(point_1, point_2, box_length=box_length)
>>     assert dist1 == expected_distance
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

## Advanced features of pytest

> ## Python Decorators
> Some of pytest's advanced features make use of decorators. Decorators are a very powerful tool in programming, which we will not
> explore in depth here. You can think of them as functions that act on other functions. To decorate a particular function, you write 
> the name of the decorator, preceded by `@`, in the line above the `def` statement:
>
> ~~~
> @decorator
> def foo():
>     pass
> ~~~
> {: .language-python}
{: .callout}

### Pytest Marks
Pytest marks allow you to mark your functions. There are built in marks for pytest and you can also define your own marks. Marks are implemented using decorators. One of the built-in marks in pytest is `@pytest.mark.skip`. Modify your `test_calculate_distance` function to use this mark.

~~~
@pytest.mark.skip
def test_calculate_distance():
    point_1 = [0, 0, 0]
    point_2 = [1, 0, 0]

    expected_distance = 1
    dist1 = calculate_distance(point_1, point_2)
    
    assert dist1 == expected_distance
~~~
{: .language-python}

When you run your tests, you will see that this test is now skipped:

~~~
tests/test_coord.py::test_calculate_distance SKIPPED  [ 50%]
tests/test_coord.py::test_calculate_distance2 PASSED  [100%]
~~~
{: .output}

You might also use the `pytest.mark.xfail` if you expect a test to fail.


### Pytest Parametrize

For our `calculate_distance` function we have only tested two conditions so far. This is not very complete, and we may be missing testing edge cases. You may think of writing another test where you change the values which you input into the calculation, or do multiple calculations with periodic boundaries. This is definitely something you can do, but `pytest` has a feature which makes it easy to run a test with multiple inputs/values - the `parametrize` mark.

> ## Edge and Corner Cases
> 
> ### Edge cases
> The situation where the test examines either the beginning or the end of a range, but not the middle, is called an edge case.
> In a simple, one-dimensional problem, the two edge cases should always be tested along with at least one internal point.
> This ensures that you have good coverage over the range of values.
> 
> Anecdotally, it is important to test edges cases because this is where errors tend to arise. Qualitatively different behavior happens at boundaries. As such, they tend to have special code dedicated to them in the implementation.
>
> 
> ### Corner cases
> When two or more edge cases are combined, it is called a corner case. If a function is parametrized by two linear and independent variables, a test that is at the extreme of both variables is in a corner.
{: .callout}


The syntax for the `pytest.mark.parametrize` decorator is:

~~~
@pytest.mark.parametrize("variable_name1, variable_name2, ...variable_nameN", [
    (variable_value1, variable_value2, ...variable_valueN),
    (variable_value1, variable_value2, ...variable_valueN), ...
])
def test_name(variable_name1, variable_name2, ... variable_nameN):
~~~
{: .language-python}

Where each line in the middle (in parenthesis) gives a set of values for the test. Then, these variables are passed to the test written under the decorator.

For example, for testing our `calculate_distance` function, we might test several coordinates at one time.

~~~
@pytest.mark.parametrize("point1, point2, expected_distance, box_length",
    [
        ([0, 0, 0], [1, 0 , 0], 1, None),
        ([0, 0, 0], [8, 0 , 0], 8, None),
        ([0, 0, 0], [8, 0 , 0], 2, 10)
    ],
)
def test_calculate_distance_many(point1, point2, expected_distance, box_length):

    calculated_distance = calculate_distance(point1, point2, box_length=box_length)

    assert calculated_distance == expected_distance
~~~
{: .language-python}

Run these tests, but this time add another special option to pytest `-k` which allows you to specify the name of the test you want to run.

~~~
$ pytest -v -k "test_calculate_distance_many"
~~~
{: .language-bash}

~~~
==================== test session starts ====================
platform darwin -- Python 3.7.6, pytest-5.4.2, py-1.8.1, pluggy-0.13.1 -- /Users/jessica/miniconda3/envs/molssi_best_practices/bin/python3.7
cachedir: .pytest_cache
rootdir: /Users/jessica/lesson-follow/monte-carlo-lj
plugins: cov-2.10.0
collected 6 items / 3 deselected / 3 selected               

tests/test_coord.py::test_calculate_distance_many[point10-point20-1-None] PASSED [ 33%]
tests/test_coord.py::test_calculate_distance_many[point11-point21-8-None] PASSED [ 66%]
tests/test_coord.py::test_calculate_distance_many[point12-point22-2-10] PASSED [100%]

============== 3 passed, 3 deselected in 0.03s ==============
~~~
{: .output}

Running this test resulted in three different tests with three different values.

### Code Coverage 

Now that we have a set of modules and associated tests, we want to see how much of our package is "covered" by our tests.
We'll measure this by counting the lines of our packages that are touched, i.e. used, during our tests.

We already have everything we need for this since we installed `pytest-cov` earlier which includes the coverage tools on top of the `pytest` package.

We can assess our code coverage as follows:

~~~
pytest --cov=mcsim
~~~
{: .language-bash}

~~~
==================== test session starts ====================
platform darwin -- Python 3.7.6, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
rootdir: /Users/jessica/lesson-follow/monte-carlo-lj
plugins: cov-2.10.0
collected 6 items                                           

tests/test_coord.py ......                            [100%]

---------- coverage: platform darwin, python 3.6.8-final-0 -----------
Name                   Stmts   Miss  Cover
------------------------------------------
mcsim/coord.py            33      8    76%
mcsim/energy.py           34     34     0%
mcsim/monte_carlo.py      51     51     0%
------------------------------------------
TOTAL                    118     93    21%

===================== 6 passed in 0.07s =====================
~~~
{: .output}

The output shows how many statements (i.e. not comments) are in a file, how many weren't executed during testing, and the percentage of statements that were.

To improve our coverage, we also want to see exactly which lines we missed and we can determine this using the `.coverage` file produced by `pytest`. You can see an html version by running the command

~~~
pytest --cov=mcsim --cov-report=html
~~~
{: .language-bash}

You will now have an additional folder called `htmlcov` in the directory. You can open the `index.html` file in this folder in your browser to view your code coverage report.

> ## Do we need to get 100% coverage?
>
> Short answer: __no__.
> Code coverage is a useful tool to assess how comprehensive our set of tests are and in general the higher our code coverage the better.
> __However__, trying to achieve 100% coverage on packages any larger than this sample package is a bit unrealistic and would require more time than that last bit of coverage is worth.
>
{: .callout}
