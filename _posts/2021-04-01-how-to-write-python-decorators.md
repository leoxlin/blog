---
layout:     post
title:      "How to write python decorators"
date:       2021-04-01
tags:       software python
---

## What are decorators

Decorators are special types of function in python that was introduced in
[PEP-318](https://www.python.org/dev/peps/pep-0318/). It is a function that
extends or change the behavior of a function/class.

## Basic decorator syntax

A decorator can be defined as any other python functions. It needs to accept a
function as in input and return another function as output.

The `@` symbol applies the decorator on the function it annotates.

{% highlight python %}
def scream_when_invoked(func):
  def wrapped():
    print("AHHH")
    return func()
  return wrapped

@scream_when_invoked
def print_name():
  print("My name is Leo")
{% endhighlight %}

When you execute `print_name`, it will execute the wrapped version.

{% highlight python %}
>>> print_name()
AHHH
My name is Leo
{% endhighlight %}

## Experimenting with the decorator '@' syntax

What if you want to accept some arguments to construct the decorator? Let's say
you want to alter `scream_when_invoked` to scream a different value.

The awesome thing about `@` is that it just expects a function that takes a
function and returns a function. And any statement after `@` is evaluated
before the decorator is applied.

{% highlight python %}
def scream_when_invoked(line):
  def scream_when_invoked_decorator(func):
    def wrapped():
      print(line)
      return func()
    return wrapped
  return scream_when_invoked_decorator

@scream_when_invoked(line="YESS")
def print_name():
  print("My name is Leo")
{% endhighlight %}

This will print the expected result with the custom `line` being screamed

{% highlight python %}
>>> print_name()
YESS
My name is Leo
{% endhighlight %}

But what other things will `@` accept?

### It will accept variables

{% highlight python %}
def scream_decorator_builder(line, func):
  def wrapped():
    print(line)
    return func()
  return wrapped

scream_when_invoked = lambda func: scream_decorator_builder("NOOO", func)
@scream_when_invoked
def print_name():
  print("My name is Leo")
{% endhighlight %}

### It will accept callable objects

{% highlight python %}
class scream_when_invoked:
  def __init__(self, line):
    self.line = line
  def __call__(self, func):
    def wrapped():
      print(self.line)
      return func()
    return wrapped

@scream_when_invoked("WOAH")
def print_name():
  print("My name is Leo")
{% endhighlight %}

### In fact it supports any named expressions

The decorator symbol '@' can be followed by
[any named expressions](https://docs.python.org/3/reference/grammar.html?highlight=decorators)
that evaluates to `(func) -> func`.

> decorators: ('@' named_expression NEWLINE )+

Here's an extreme example where even an `next` expression can be used.

{% highlight python %}
def scream_when_invoked(func):
  def wrapped():
    print("NICE")
    return func()
  return wrapped

@next(iter([lambda func: scream_when_invoked(func)]))
def print_name():
  print("My name is Leo")
{% endhighlight %}

## Decorating functions with arguments

We used the example `print_name` as the function being decorated. But what if
you need to decorate a function that accepts arguments?

You can either make it generic or specific to the type of function you decorate.

### Specific params

Let's say you only need to decorate functions that accepts lists and returns
lists. Your decorator can focus on handling functions that accepts lists only

{% highlight python %}
def append_777(func):
  def wrapped(items):
    return func(items) + [777]
  return wrapped

@append_777
def duplicate_list(items):
  return items * 2

@append_777
def reverse_list(items):
  return list(reversed(items))
{% endhighlight %}

`append_777` will append 777 to any list returned by the function it decorates.

{% highlight python %}
>>> duplicate_list([1,2,3])
[1, 2, 3, 1, 2, 3, 777]

>>> reverse_list([1,2,3])
[3, 2, 1, 777]
{% endhighlight %}

### Generic params

You can also accept all functions by using generic parameters.

{% highlight python %}
def scream_before_and_after(func):
  def wrapped(*args, **kwargs):
    print("BEGINN")
    result = func(*args, **kwargs)
    print("DONEEE")
    return result
  return wrapped

@scream_before_and_after
def print_sum(items):
  print(sum(items))
{% endhighlight %}

`scream_before_and_after` will print before and after it executes the function.

{% highlight python %}
>>> print_sum([100, 200, 300])
BEGINN
600
DONEEE
{% endhighlight %}

## Decorating classes

Decorators can decorate classes as well. It will accept a class and must return
a class. The wrapper class can even extend the decorated class.

{% highlight python %}
def add_last_name(Cls):
  class WrappedCls(Cls):
    def __init__(self, *args, **kwargs):
      super().__init__(*args, **kwargs)
      self.name = self.name + " Nye"
  return WrappedCls

@add_last_name
class Person:
  def __init__(self, name, occupation):
    self.name = name
    self.occupation = occupation
  def introduce_yourself(self):
    print(f"Hi I am {self.name} {self.occupation}")
{% endhighlight %}

`add_last_name` adds the last name `Nye` to any classes that contains a name
field. In our case, the `Person` class was modified.

{% highlight python %}
>>> guy = Person("Bill", "The Science Guy")
>>> guy.introduce_yourself()
Hi I am Bill Nye The Science Guy
{% endhighlight %}

## Awesome Decorator Usages

Decorators are commonly used for things like timers and declaring @staticmethod
for a class, I've collected some examples of crazy uses you can push decorator
to do.

- [Patching the function code and implementing goto](https://github.com/snoack/python-goto)

  This is one of the greatest `decorator` usage I've seen. The author wrote a
  decorator that will modify the code of the function passed in and make `goto`
  and `label` work in python

- [Timeout decorator that limits the execution time of function](https://github.com/pnpnpn/timeout-decorator)

  There is so much utility in being able to manage the execution time of a
  function. Having this as a decorator makes implementing timeout behavior with
  functions a lot easier.

- [Rate limiting how many times a function can be called](https://github.com/enricobacis/awesome-python-decorators/tree/master/ratelimit)

  This is another decorator that makes multi-threaded python easier. You can
  wrap any functions and specify how many times it can be called per time
  period.
