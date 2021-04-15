---
layout:     post
title:      "Why bash and essential bash tricks"
date:       2021-04-15 06:00:00
tags:       software bash
---

## Why are we still using bash

I think as a person who graduated college in 2015, bash is probably one of the
last language I'd like to script with. There are a bajillion sexy, expressive,
easy languages like python to script with.

### It exists *almost* everywhere

For the most part, everything I work with have bash on it. Unless you are
working on some microcontrollers, the chances are you will have access to a
bash shell on most modern linux distros.

Furthermore, you most likely will also have access to the [GNU
CoreUtils](https://www.gnu.org/software/coreutils/) which can be incorporated
into your scripts to maximize your productivity.

### It can be used interactive & script

This is pretty huge. For some languages like `C++` and `golang`, there isn't
really an easy way to interact with it like a system shell.

For languages that do offer a REPL, there have always been cool projects that
attempts to make it a system shell replacement.

- Python: <https://xon.sh>
- Scala: <https://ammonite.io>
- Scheme: <https://scsh.net>

While these are great for you own local development, it becomes a challenge to
maintain if you work with people who aren't familiar with it.

### It provides simplicity

I think the beauty of bash really comes into play when you want to glue a bunch
of small programs together.

Let's say you want to count the number of files ending in *.log in /var/log,
you'd need to do this in python:

{% highlight python %}
pipe = subprocess.PIPE
proc1 = subprocess.Popen(['ls', '/var/log'], stdout=pipe)
proc2 = subprocess.Popen(['grep', '.log$'], stdin=pipe, stdout=pipe)
proc3 = subprocess.Popen(['wc', '-l'], stdin=pipe, stdout=pipe)
output = proc3.communicate(
  proc2.communicate(
    proc1.communicate()[0]
  )[0]
)[0]
print(output.decode('utf-8'))
{% endhighlight %}

Of course there are native ways to be able to accomplish this in python but the
commands you want to chain together might not always be implemented in python or
some other language you are using.

For the same calls, you can easily do this in bash:

{% highlight bash %}
ls /var/log | grep .log$ | wc -l
{% endhighlight %}

### It is stable and have been around a long time

Bash is not perfect, but it is the devil we all know. First release in 1989,
most developers who works on linux in this generation have been exposed to Bash
in some form or another.

## Bash "Tricks"

Well, in some regards, there are no bash tricks :D In the spirit of not making a
click bait, I will share some stuff here about the most useful tricks I learned.

The more you spend time learning about bash and the various Linux and GNU tools,
the more effective you will be at it. Check out the next section to learn how to
[Bash Properly](#bash-properly).

#### Use the Bash Hackers wiki

Fortunately people smarter and more altruistic than me have compiled an awesome
wiki of useful knowledge regarding bash.

The [Bash Hackers wiki](https://wiki.bash-hackers.org/doku.php) provides useful
beginner guides and documentations about Bash. It is a little bit easier to get
into than the [Bash Manual](https://www.gnu.org/software/bash/manual/bash.html)
for beginners.

#### Read all the FAQs

We've definitely all googled how to do some specific thing in Bash. The list of
questions tagged on Stack Overflow with *'bash'* is actually a treasure trove.

You can filter the questions by the [most
votes](https://stackoverflow.com/questions/tagged/bash?tab=Votes) or the [most
frequently seen](https://stackoverflow.com/questions/tagged/bash?tab=Frequent).

It is really useful to look at some of the top questions because they might
teach you about things people commonly struggle with.

Some of these questions are also available on the [Bash FAQ wiki
page](http://mywiki.wooledge.org/BashFAQ).

#### Run in strict mode

Debugging is probably one of the most frustrating thing about bash because it is
not always clear what went wrong. Also without setting some flags your script
might not fail fast which can cause interesting behaviors.

You can turn on the [unofficial strict
mode](http://redsymbol.net/articles/unofficial-bash-strict-mode) in bash to have
an easier time.

#### Collect snippets

I have this giant file in my notebook app containing all kinds of bash snippets
and wicked one liners. It will probably be useful across multiple positions you
hold as a developer and it your personal life.

Instead of having to google things over and over, your snippet notes can become
a powerful toolbox overtime.

Here's few examples:
{% highlight bash %}
arr=($(...some_cmd...))
for (( n=0; n<${#arr[@]}; n++ )); do
  echo ${arr[$c]}
done

...some_cmd... | while read line; do
  echo "do work with $line"
done

[ -z "$SOME_VAR" ] && echo "SOME_VAR IS EMPTY"
[ -n "$SOME_VAR" ] && echo "SOME_VAR IS NOT EMPTY"
{% endhighlight %}

I also include snippets for common commands like `sed`, `grep`, `cut`,
`...`, `etc`.

There is a lot more on [Github Gists](https://gist.github.com/) and [Bash
Oneliners](http://www.bashoneliners.com/oneliners/popular).

## Bash Properly

#### 1. Learn about Operating Systems

I really wish I paid more attention in my university course about Operating
Systems. It has become one of the most relevant classes to my career and allowed
me to reason with software at a much deeper level.

Like
[CLRS](https://www.amazon.com/Introduction-Algorithms-3rd-MIT-Press/dp/0262033844)
for Algorithms, you can get the [Dinosaur
Book](https://www.amazon.com/Operating-System-Concepts-Abraham-Silberschatz/dp/0470128720)
to learn about Operating Systems.

#### 2. Use the [man pages](https://man7.org/linux/man-pages/man1/man.1.html)
```
"If you don't know what `man` does run `man man`"
  - Professor Ganesan (My OS Prof)
```

#### 3. Learn about Linux

If you run any programs in Linux, it is crucial that you learn about Linux at
least at the high level.

There are a lot resources and youtube videos that I won't link here but my
personal favorite is <https://linuxjourney.com>

#### 4. Learn about all the tools

There is already a ton of incredibly powerful tools that ships with Unix like
systems. Expanding your knowledge on `sed` for example can easily extend your
ability to script complex things ten fold.

You can usually read the `man` page for any tools and get a quick overview of
the functionality. Another approach is to check out this book.

<https://www.amazon.com/Power-Tools-Third-Shelley-Powers/dp/0596003307>


#### 5. Use it & automate

Ultimately any theory you learn need to be battle tested. The best way to learn
to script in bash is just try it out for tasks that takes up your time.

For example, at my current job we use JIRA ticket names as branch names. i.e.
PROJECT-1234. I need to look up the ticket associated with the branch every time
I need to check one out.

Instead of that I just wrote a little script that I hook into my `.gitconfig`
which allows me to list branches and [auto query the JIRA ticket
summary](https://gist.github.com/leoxlin/ac0bc7a7b4427365b9eb6334c1c43f73).
