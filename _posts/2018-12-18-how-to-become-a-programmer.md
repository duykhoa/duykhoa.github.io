---
layout: post
title: How to become a programmer
excerpt_separator: <!--more-->

---

# WIP

> Programmer ˈ/prəʊɡramə/' is a person who writes computer programs. (Google Translate)

<!--more-->

This post is another guide to contribute a massive list of tutorial: how to become a developer/programmer/web developer.

Most of blog post about learning to become a programmer are about: pick up a programming language (javascript, ruby,
python), practicing by writing some code, doing kata, building a website.

This one is just another follows this path. I'm apologyze for disappointing you.

You can go now because it has no difference with other posts you've read. May be there are some typos because English
isn't my mother language.

I work with people who graduated from "learning to become a programmer" 3 month program, who truly love software
development. This post includes my observation when working with them, what they have and other people don't. And I
believe what they have identified them as programmers.

This post is just about become a programmer, as the definition "who writes computer programs". To become different kind
of people who also write computer programs, but not a programmer only, or other skills to work with people and other
stuffs that are not related to computer, I include some resources I have read in the last part of this post "You are a
programmer already, what is next".

# Why not programmer

Simple and rude, who hears like programmer is fun and can get a job with high pay, and developer is the hottest job, you
should research a bit deeper before invest you effort in, talk with a developer in a local meetup, watch some youtube
video about developer life. It isn't that cool like the hacker scene in movies or advertisement.

# Why Programmer

Also simple, who like to express ideas into a concrete, precise language, that both understanding by computer and human.

A programmer should be the nicest person who always take care of all potential problems and try to minimize the effort
of his/her team when maintaining the program.

To take care of the team, he/she must keep her discipline to write clean code, naming stuffs with descriptive meaning.
Naming thing always be an civil war (sometimes), it can't be neither too generic or too specific, it can't describe the
data type because some miserable day this datatype isn't suiteable and the team has to name this stuff again (not apply
for all programming languages ya).

To prove the program he/she wrote works, he/she must write full cover tests. The unit test are the low level documentation.
His/her teammates can look at the unit test to understand how a method work, it is easier because the unit test is
simple with one or a few assertions with a descriptive description.

As the same time, he/she has to balance a number of objectives while remain his care for the team. The deadline is near, and
he/she knows he/she can make it without writing any test and just put the entire chunk of logic together, just to get it
done. He/she can choose to discuss earlier to extend the deadline, and using his/her knowledge about estimation in
software development to explain the situation to the product owner, he/she can still remain his cares for the team.
If it isn't possible, he/she may decide to add some technical debt to the project, but he also makes a strong promise
to himself and the team and have a clear communication with the product team to fix his technical debt. He has to make
sure that people are aware of the cost to fix his debt now or later, and make sure his team doesn't mutiply his debt to
many places.

Not every programmer/developer have to deal with this problem. However, when it happens, the programmer has to perform
the communication well to defense the team objective and the product he/she is maintaining. Programming is 90% human to
human work: review people code, writting code that people understand and follow people's convention, understand how
user uses the product, get the right meaning of people's requirement, etc.

If you are confident about your ability to take care of people in your team, your project, ability to explain
what you believe in to other people, and able to understand people problem and come out with a right solution that is
good for both side, the programmer door is open for you.

# First step to become a programmer: learn a programming language

To become a programmer, as the current moment, you have to learn a programming language. Programming language is the
only way so far to express programmer idea to computer and to other programmers.

Here is an awesome book: "Seven Languages in Seven Weeks: A Pragmatic Guide to Learning Programming".

What is the best language to learn: C, C++, Java, Ruby, Python, NodeJS, Javascript, Elixir, Erlang, Scala, Swift, Go,
PHP, Prolog, etc.

The answer for it is bias by the most familar language of the answer's person. I prefer Ruby because it is my most used
language. [Ruby][1] is a OOP with dynamic typing, meta programming support and tend to be similar with human language
(English).

[C][2] is 46 years old language (as the time this blog post is published, Dec 2018; C is first appeared in 1972).
C is a general language and is used to write the Ruby language. C is used in embeded system, operation system, software 
application. It is structural language, doesn't support OOP.

[Java][3] is an general language with class-based, OOP, intended to "write once, run everywhere" (need to install java
machine first). 

[Elixir][4] and [Scala][5] is functional programming language, which is another style of programming.

[Prolog][6] is a logic programming language, it is used most in Artificial intelligence.

These short introductions give us a sense that each language may solve a specific problem better than other. However,
almost human problems can be solved by any language, just the approach is different.

The first sentence is not true actually. Computer doesn't understand Ruby, Python, Node js, or even C. Computer only
understand 0 and 1. It may understand a bit more, but not a human friendly sentence `5.times { |i| puts '*' * i.next }`
or `1.upto(5) { |i| puts '*' * i  }`. It means the purpose of expressing programmer ideas is just for human, not for a
computer.

I hope you agree with me that learning a programming language is for you to communicate with other people, and
communicating with a computer is just secondary. To talk with more people, we have to learn more than 1 language because
different people prefer different language.

Every programming language has some differences with other languages. Java has Interface, C allows to allocate memory
and manually trigger garbage collector, while the rest don't allow programmer to do that. C++ allows multiple
inheritance, while Java, Ruby only support single inheritance. Javascript uses prototype and Go doesn't have class. In
C, Java, Scala, we have to specify the datatype when define a variable, and we don't have to do that in Javascript,
Ruby, Python. Ruby program can't be run in multiple processor because the [MRI run on one thread][7], C++ has
overloading and overriding, but the rest only support overriding.

Beside than that, every programming language shares the same list of basic functionalities, involved variable
declaration, condition branching and iteration.

Here is an example of defining a variable in different language:

- Ruby: `sum = 0`, `name = "Kevin"`, `checked = false`
- Javascript: `let sum = 0`, `var checked = 0`, `let name = "Changi Airport"`, `coffeeType = "Arabica"`
- C: `int count = 1`, `char aString[] = "Terminal 4"`, `typedef enum { false, true } bool; bool checked;`

As seeing, they are pretty much similar. For the condition branching and integration syntax are also similar between
each language. There are some syntax sugar like `1.upto(5)`, `5.times`, `[1,2,3].each`, but their implementation are
back to the basic iteration syntax, which is `for i = 0; i < 5; i += 1` or `while i < 5 do ....; i += 1; end`.

There are just [a few paradigms][8]: Imperative, Functional, Object Oriented and mixed. Hence learning a paradigm is
equal with learning a few programming languages. It may be the best secret that open the door of learning as many
programming languages as you want, as fast as you can.

If you are reading my post, and you feel what I'm saying is true, I suggest you to do this exercise today or tomorrow:

- Spend 2 days to learn basic syntax of Ruby, includes
  - Branching condition: if else, short hand if else, negative if, guarding condition, case..when 
  - Iteration syntax and basic Enumerator functions
  - Class definition
  - Class inheritance, module extending and including
- Spend 1 day to learn basic syntax of Elixir
- Spend 1 day to learn basic syntax of Golang
- Spend 1 day to learn basic syntax of Javascript
- Spend 1/2 day to learn basic syntax of Typescript and ES6
- Spend 1/2 day to learn basic syntax of Swift, Kotlin

If you are able to do these tasks above, you acquired the skill which allows you to learn any thing, doesn't limit to
programming language as you want. The time I suggest is very short, and it gets shorter later on. You will probably not
able to remember the specific syntax, or don't even remember how to run a Go program in few weeks. If isn't the main
point of learning programming language, we can only remember a programming language well when using it regularly.
Knowing what can we do with it is enough.

# Second step to become a programmer: learn basic data structure and algorithm

As you follow me til now, and you did the exercise I suggested, you are able to pick up any programming
language without difficulty. Here is another secret, as programming language evolves, the new born programming language
should (must) be easy to learn, and not too different with existing language. If it is harder or so different, people
won't learn it.

After being able to learn a programming language, programmer needs to learn how to express an idea to programming
language. Programmer needs a strategy to find a solution based on the input and the problem statement.

The word programmer uses to call the strategy of finding a solution for a given is ALGORITHM.

> /ˈalɡərɪð(ə)m/  a process or set of rules to be followed in calculations or other problem-solving operations, especially by a computer.

Algorithm is often misconceptual with some magical (as I heard from business people sometimes), but the truth is

> Word used by programmers when they do not want to explain what they did.

We will focus on discussing about algorithm, and temporary skip the term data structure for a while.

To prove that algorithm can be simple, I use the example of sorting a list of numbers.

**TL;DR**

Assume the program is just applied for 2 number, we can name the 2 items as `a` and `b`,
the code for this can be written like below

```go

func sort(a int, b int) []int {
  if a > b {
    return []int{a, b}
  } else {
    return []int{b, a}
  }
}
```

However, if there are 3 input numbers, the logic becomes a bit more complicated

```go
func sort(a, b, c int) []int {
  if a <= b {
    if b <= c {
      return []int{a, b, c}
    } else if a <= c {
      return []int{a, c, b}
    } else {
      return []int{c, a, b}
    }
  } else {
    if a <= c {
      return []int{b, a, c}
    } else if b <= c {
      return []int{b, c, a}
    } else {
      return []int{c, b, a}
    }
  }
}
```

A few things to discuss from the solution above. First, the solution itself is an algorithm.
The algorithm states that to sort 2 numbers, it compare the first number `a` to the second number `b`, if `a` is less
than or equal to `b`, then the algorithm returns an array of `a` and `b` with no change in the order of `a` and `b`,
otherwise it returns `b` before `a` if `a` is greater than `b`.

For 3 numbers, after comparing the `a` and `b`, it takes `c` in account, there are 3 possible position for c in the
result.

Same strategy can apply for 4 input numbers. The problem isn't about the algorithm itself, but the implementation.
The implementation see each input number as a variable, hence it is only applied for a certain number of inputs.
If the implementation treats input numbers as a collection, hence it can reused for any number of inputs.

To make the program reusable, I change the the `sort` function's definition to

```go
func sort(arr []int) { //TODO }
```

Instead of return a new array with correct position of each input number, I try to make the change on the original array
if necessary.

```go
func sort(arr []int) []int {
  if arr[0] > arr[1] {
    arr[0], arr[1] = arr[1], arr[0]
  }

  if arr[1] > arr[2] {
    arr[1], arr[2] = arr[2], arr[1]
  }

  if arr[0] > arr[1] {
    arr[0], arr[1] = arr[1], arr[0]
  }
}
```

This version only applies for 3 input numbers, but it creates room for extending by using the iteration.

```go
func sort(arr []int) []int {
  for i := 0; i < 2; i++ {
    if arr[i] > arr[i + 1] {
      arr[i], arr[i + 1] = arr[i + 1], arr[i]
    }
  }

  if arr[0] > arr[1] {
    arr[0], arr[1] = arr[1], arr[0]
  }

  return arr
}
```

It can be optimized one more time

```go
func sort(arr []int) []int {
  for j := 0; j < 2; j++ {
    for i := 0; i < 2 - j; i++ {
      if arr[i] > arr[i + 1] {
        arr[i], arr[i + 1] = arr[i + 1], arr[i]
      }
    }
  }

  return arr
}

// last thing is change `2` to `len(arr)`
```

This piece of code is based on the previous version, adding some abstraction (using interation) to make the code
reusable. Don't worry if you don't fully understand it yet. Bob Martin had explained the transformation in [his blog post
about TPP][9]. It will be another discussion about the practical usage of this method. I just want to say that Algorithm
can be something easy and understandable by everyone.

I am still wondering whether the example about building a sort function is a good example. Most of programming language
has a built-in sort function, and it uses a better sort algorithm than our solution.

The point is, Algorithm can be anything. It is just a 

> a process or set of rules to be followed

It can be written by anyone, it doesn't stop at the classic one like  sorting algorithm, find shortes path algorithm,
dynamic programming, etc.

<!-- brain storming -->

Learning algorithm is another thing. Yes, you can solve the problem, but not in the best way. RIght, many people say it
doesn't need to be in the best way from the beginning. Optimizing the code will take a lot of time and you may end up
wasting a lot of time to improve the code, while if you already know the right strategy, just use it and it will save
you a lot of time of figuring out.


Here is the list of algorithms you can start to learn

- Sorting
- Find the shortest path
- loang
- dynamic programming
- Searching
- bruce force
- back track

MOre advance topic, and more algorithm, you can check out the book "Introduction about Algorithm"o

<!-- brain storming -->

# Third step to become a programmer: do a real project
# Forth step to become a programmer: reading people code

# Fifth step to become a programmer: write reusuable code
# Sixth step to become a programmer: sharing is caring
# Seventh step to become a programmer: you are a programmer, what is next

  [1]: https://www.ruby-lang.org/en/about/
  [2]: https://en.wikipedia.org/wiki/C_(programming_language)
  [3]: https://en.wikipedia.org/wiki/Java_(programming_language)
  [4]: https://elixir-lang.org/
  [5]: https://www.scala-lang.org/
  [6]: https://en.wikipedia.org/wiki/Prolog
  [7]: https://stackoverflow.com/questions/56087/does-ruby-have-real-multithreading/57802#57802
  [8]: https://www.cs.bham.ac.uk/research/projects/poplog/paradigms_lectures/lecture1.html
  [9]: http://blog.cleancoder.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html
