---
layout:     post
title:      "Using python generators"
date:       2022-09-10
tags:       software python
---

## What are generators

Generators are a type of functions or expressions in python that enables you
provide an iterator. A great example of a generator function is the `range`
function used frequently in `for` loops. Iterators generated by generators are
lazy which means that they are not evaluated until you access the next element
which makes it memory efficient. The iterator will return one value at a time
until the next function is called.

## Basic generator syntax

Let's make a generator for the fibonacci sequence. If you are not familiar, the
fibonacci is defined to be F0 = 0, F1 = 1, Fn = Fn-1 + Fn-2.

{% highlight python %}
def fibonacci_sequence():
  fn_s2, fn_s1 = 0, 1
  while True:
    yield fn_s2
    fn_s2, fn_s1 = fn_s1, fn_s2 + fn_s1
{% endhighlight %}

Calling the method will return a generator instead of a result when there is
a yield within the method.

```
>>> fib = fibonacci_sequence()
>>> type(fib)
<class 'generator'>
```

The `yield` keyword will yield next next element of the sequence whenever `next`
is invoked on the generator.

When fetching the first 7 elements of the series, the function successfully
returned: 0, 1, 1, 2, 3, 5, 8

```
>>> [next(fib) for _ in range(7)]
[0, 1, 1, 2, 3, 5, 8]
```

## Execution mechanics

While this syntax makes it easy to write iterators, which part of the function
is actually lazy? I wanted to test this theory out by using the following
version of the fibonacci function.

{% highlight python %}
def fibonacci_sequence():
  num = 0
  fn_s2, fn_s1 = 0, 1
  print("Pre Loop")
  while True:
    num = num + 1
    print(f"Before Yield: {num}")
    yield fn_s2
    print(f"After Yield: {num}")
    fn_s2, fn_s1 = fn_s1, fn_s2 + fn_s1
{% endhighlight %}

The prints shows which part of the function is evaluated when. It seems like
everything after the yield statement is only evaluated during the next call.

```
>>> fib = fibonacci_sequence()
>>> next(fib)
Pre Loop
Before Yield: 1
0
>>> next(fib)
After Yield: 1
Before Yield: 2
1
>>> next(fib)
After Yield: 2
Before Yield: 3
1
>>> next(fib)
After Yield: 3
Before Yield: 4
2
```

It is probably best to use generators as an iterator to run until completion
instead of representing some external states. This is also recommended in
the [python documentation](https://docs.python.org/3/faq/design.html?highlight=generators#why-don-t-generators-support-the-with-statement).

## Underneath the hood

The generator goes through [four
states](https://docs.python.org/3/library/inspect.html?highlight=generators#inspect.getgeneratorstate).
It will start in `GEN_CREATED` and eventually terminate in `GEN_CLOSED`.

The interpreter will suspend the execution after every yield. Calling next on
the generator will resume the execution again. Each RUNNING state will either
terminate in a yield or `StopIteration` if the function completes.

For example in this simple countdown generator:

{% highlight python %}
def countdown(n):
  i = 0
  while n - i > 0:
    print(f"Before yield: {i}")
    yield (n-i)
    print(f"After yield: {i}")
    i = i + 1
{% endhighlight %}

The first 3 `next` calls yielded the countdown, and the final `next` call raised
the `StopIteration` exception indicating the generator is now in a `GEN_CLOSED`
state.

```
>>> c = countdown(3)
>>> next(c)
Before yield: 0
3
>>> next(c)
After yield: 0
Before yield: 1
2
>>> next(c)
After yield: 1
Before yield: 2
1
>>> next(c)
After yield: 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```