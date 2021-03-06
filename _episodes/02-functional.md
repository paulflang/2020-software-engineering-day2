---
title: "Functional Programming"
teaching: 65
exercises: 25
questions:
- "What is functional programming?"
- "What is recursion?"
- "How does functional programming manipulate data?"
objectives:
- "Write recursive functions for to calculate simple sequences"
- "Use the MapReduce model to process data"
keypoints:
- "Pure functions are functions which have no side effects"
- "Recursion allows us to calculate a value in a sequence from previous values"
- "The MapReduce model is a common way of processing large amounts of data"
---

## The Functional Paradigm

> ## Functions in Maths
> > In mathematics, a function is a relation between sets that associates to every element of a first set exactly one element of the second set.
> >
> > -- Wikipedia - Function (mathematics)
{: .callout}

The Functional paradigm is based on mathematical functions.
A program written in a functional style describes a series of operations which are performed on data to produce a desired output, the focus being on **what** rather than **how**.

The most likely place you will encounter functional programming in the future is in data analysis code, using frameworks such as Hadoop, or languages like R.

Though we are teaching Functional Programming after Object Oriented Programming, it does not build on Object Oriented as Object Oriented built on Procedural Programming.
It belongs to a different branch in the history of paradigms, the Declarative branch.

## Side Effect and Pure Functions

There's nothing particularly special about the behaviour of functions in Python, almost any valid code can be put inside a function to be called from elsewhere (even defining classes or other functions).
This means that each function can do anything the Python language can do.

In well designed code, a function should only be responsible for one task (see: Single Responsibility Principle).
As with most guidelines, the reason we do this is to make it easier to reason about the behaviour of our code.
If a function performs only one task, we can be sure about when it is appropriate to use that function and what will happen when we call it.

Any behaviour which is not part of the single task of a block of code is called a **side effect**, while a function which has no side effects is a **pure function**.
In practice these definitions are a little flexible depending on who is using them and in which context.
The strictest definition of **side effect** includes things like printing to the terminal or saving to a file, so in these cases a function without side effects have no other effect than to return a value.
In some functional languages, a **pure function** must not only have no side effects, but also must return the same value when given the same arguments.

~~~
def increment_x(x):
    return x + 1

print(increment_x(3))
~~~
{: .language-python}

~~~
4
~~~
{: .output}

~~~
def increment_x(x):
    print('Incrementing', x)
    return x + 1

print(increment_x(3))
~~~
{: .language-python}

~~~
Incrementing 3
4
~~~
{: .output}


## Recursion

See: Recursion

Recursion is one of the common strategies used in Functional Programming.
Instead of using loops to **iteratively** apply an operation, we can express a result in terms of previous results.
To do this, the function needs to call itself to get the previous result, this is called **recursion**.

<a href="{{ page.root }}/fig/droste-effect.jpg">
  <img src="{{ page.root }}/fig/droste-effect-small.jpg" alt="Droste Effect" />
</a>

To illustrate recursive functions, we'll use factorials as an example.
The factorial of a positive integer `N` (written `N!`) is the product of all of the positive integers equal to or less than `N`.
For example, `5! = 5 x 4 x 3 x 2 x 1 = 120`.

We can calculate a factorial iteratively:

~~~
def factorial(n):
    product = 1
    for i in range(1, n + 1):
        product = product * i

for i in range(5):
    print(factorial(i))
~~~
{: .language-python}

~~~
1
1
2
6
24
~~~
{: .output}

But the factorial function has a property which makes it particularly suitable to be defined recursively.

To define a recursive function we need two things: a **recurrence relation** and a **base case**.
A recurrence relation is a process which can be used to derive the value of a sequence, given the previous value in the sequence.
With just a recurrence relation, the function would run forever, continually trying to get the previous value, so we also need a base case.
The base case is a value in the sequence which is known without having to derive it from previous values.

In the case of the factorial function the recurrence relation is: `N! = N * (N-1)!` or equivalently `f(N) = N * f(N - 1)` - the Nth value in the sequence is N times the previous value.
The base case is `0! = 1` - the factorial of zero is one.

So, if we express the factorial function recursively, we get:

~~~
def factorial(n):
    if n == 0:
        return 1

    return n * factorial(n - 1)
~~~
{: .language-python}

But there's something a bit dangerous about this implementation: if we attempt to get the factorial of a negative number, the code will get stuck in an infinite loop.
In practice, Python has a limit to the number of times a function is allowed to recurse, so we'll actually get an error.

~~~
def factorial(n):
    if n < 0:
        raise ValueError('Factorial is not defined for values less than 0')
    if n == 0:
        return 1

    return n * factorial(n - 1)
~~~
{: .language-python}

> ## Recursive Fibonacci
>
> Another well known sequence is the Fibonacci sequence: `0, 1, 1, 2, 3, 5, 8, 13, ...` where each value is the sum of the previous two values.
>
> One possible iterative implementation of a function to calculate the Nth Fibonacci number is shown below.
> Also note how tuple packing and unpacking are used to effectively swap two values without using a temporary variable.
>
> ~~~
> def fibonacci(n):
>     # Iterative fibonacci
>     a, b = 0, 1
>     
>     for _ in range(n):
>         a, b = b, a + b
>     
>     return a
>         
> for i in range(8):
>     print(fibonacci(i))
> ~~~
> {: .language-python}
>
> ~~~
> 0
> 1
> 1
> 2
> 3
> 5
> 8
> 13
> ~~~
> {: .output}
>
> Write an equivalent function which uses recursion to calculate the Nth Fibonacci number.
>
> Hint: first think about what the recurrence relation and base case are.
>
> > ## Solution
> >
> > First, we need to decide what the recurrence relation is - in this case it's `f(N) = f(N - 1) + f(N - 2)`.
> > And the base cases `f(0) = 0` and `f(1) = 1`.
> >
> > For the function itself, we can use the same approach as for the factorial function: first handle the base cases, then the recurrence relation:
> >
> > ~~~
> > def fibonacci(n):
> >     if n < 0:
> >         raise ValueError('Fibonacci is not defined for N < 0')
> >     if n == 0:
> >         return 0
> >     if n == 1:
> >         return 1
> >
> >     return fibonacci(n - 1) + fibonacci(n - 2)
> > 
> > for i in range(8):
> >     print(fibonacci(i))
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > 0
> > 1
> > 1
> > 2
> > 3
> > 5
> > 8
> > 13
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

## Lambda Functions

If we build our programs in a functional way, we tend to end up with a lot of small, one line functions which perform very simple operations.
For example, we might have a function which adds one to a number:

~~~
def add_one(x):
    return x + 1

print(add_one(1))
~~~
{: .language-python}

~~~
2
~~~
{: .output}

If we have a lot of these smaller functions which only get used once, it makes more sense to define them where they're used.

**Lambda functions** are small, nameless functions which fulfil this need.
In Python Lambda functions are limited to a single expression and are defined using the `lambda` keyword:

~~~
print((lambda x: x + 1)(1))
~~~
{: .language-python}

~~~
2
~~~
{: .output}

Note that the brackets around the lambda are used for clarity and are not a required part of the lambda expression in general.

## Map, Filter, Reduce

One of the main applications of functional programming currently is the Map, Filter, Reduce model of data processing, usually refered to as **MapReduce**.
This model is particularly useful for the processing and analysis of **Big Data** using tools such as Spark or Hadoop.

Note that the `map` and `filter` functions in Python are use **lazy evaluation**.
This means that values in an iterable collection are not actually calculated until you need them.
We'll explain some of the implications of this a little later, but for now, we'll just use `list()` to convert the results to a normal list.

~~~
l = [1, 2, 3]

def add_one(x):
    return x + 1

# Returns a <map object> so need to cast to list
print(list(map(add_one, l))
print(list(map(lambda x: x + 1, l))
~~~
{: .language-python}

~~~
[2, 3, 4]
[2, 3, 4]
~~~
{: .output}

~~~
l = [1, 2, 3]

def is_gt_one(x):
    return x > 1

# Returns a <filter object> so need to cast to list
print(list(filter(is_gt_one, l)))
print(list(filter(lambda x: x > 1, l)))
~~~
{: .language-python}

~~~
[2, 3]
[2, 3]
~~~
{: .output}

~~~
from functools import reduce

l = [1, 2, 3]

def add(a, b):
    return a + b

print(reduce(add, l))
print(reduce((lambda a, b: a + b), l))
~~~
{: .language-python}

~~~
6
6
~~~
{: .output}

> ## Sum of Squares
>
> Using the MapReduce model, write a function that calculates the sum of the squares of the values in a list.
> Your function should behave as below:
>
> ~~~
> def sum_of_squares(l):
>     # Your code here
>
> print(sum_of_squares([0]))
> print(sum_of_squares([1]))
> print(sum_of_squares([1, 2, 3]))
> print(sum_of_squares([-1]))
> print(sum_of_squares([-1, -2, -3]))
> ~~~
> {: .language-python}
>
> ~~~
> 0
> 1
> 14
> 1
> 14
> ~~~
> {: .output}
>
> > ## Solution
> >
> > ~~~
> > from functools import reduce
> >
> > def sum_of_squares(l):
> >     squares = map(lambda x: x * x, l)
> >     return reduce(lambda a, b: a + b, squares)
> > ~~~
> > {: .language-python}
> >
> {: .solution}
>
> Now let's assume we're reading in these numbers from an input file, so they arrive as a list of strings.
> Modify your function so that it passes the following tests:
>
> ~~~
> print(sum_of_squares(['1', '2', '3']))
> print(sum_of_squares(['-1', '-2', '-3']))
> ~~~
> {: .language-python}
>
> ~~~
> 14
> 14
> ~~~
> {: .output}
>
> > ## Solution
> >
> > ~~~
> > from functools import reduce
> >
> > def sum_of_squares(l):
> >     integers = map(int, l)
> >     squares = map(lambda x: x * x, integers)
> >     return reduce(lambda a, b: a + b, squares)
> > ~~~
> > {: .language-python}
> >
> {: .solution}
>
> Finally, like comments in Python, we'd like it to be possible for users to comment out numbers in the input file the give to our program.
> Extend your function so that the following tests pass (don't worry about passing the first set of tests with lists of integers):
>
> ~~~
> print(sum_of_squares(['1', '2', '3']))
> print(sum_of_squares(['-1', '-2', '-3']))
> print(sum_of_squares(['1', '2', '#100', '3']))
> ~~~
> {: .language-python}
>
> ~~~
> 14
> 14
> 14
> ~~~
> {: .output}
>
> > ## Solution
> >
> > ~~~
> > from functools import reduce
> >
> > def sum_of_squares(l):
> >     not_comments = filter(lambda x: x[0] != '#', l)
> >     integers = map(int, not_comments)
> >     squares = map(lambda x: x * x, integers)
> >     return reduce(lambda a, b: a + b, squares)
> > ~~~
> > {: .language-python}
> {: .solution}
>
{: .challenge}

{% include links.md %}

