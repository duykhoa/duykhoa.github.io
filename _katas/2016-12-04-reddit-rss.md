---
layout: post
title: Reddit RSS (part 1)
---

Welcome back to TDD Kata.

Well, we already go through a few examples of TDD Kata. I and you already know how to write code to solve a problem using TDD
technique, I hope. Also I have experienced with writing code and writing an explanation next, which I haven't done
before. It's great. I'm kind of lazy developer who never document my code. Sometimes I leave some comments for the method
I wrote, like a contract to another method that I will implement next.

**TLDR;**

One of my TDD addictor, he asked my why don't do a real kata, for example interact with an api or connect to database.
I have considered about it many times, there are my 2 concerns:

- The real problem one is usually aren't like any of katas. The problem is bigger. And with bigger problem, we need more code.
Last time I tried with the `game of life`, it is a problem related to math and computer science only, but still quite long
already. I don't know if we do a real problem, I wonder if my readers - **you** can diggest it?
- For a real problem, I would love to use OOP (object oriented programming), which is class and object. Then we have some more
things to discuss, like dependency inversion, single responsibility and design pattern. There aren't just 3 things, but more
than that. And with my limited knowledge, I am not really sure that I can send the message to you. Also when talk about these
techniques too much, feel like I am trying to overwhelm every one here, haha.

That's my concerns when introducing a real problem for TDD kata. But I decided to do it anyway. So I hope you guys will help me
by give me some comments if I am wrong or describe something not clear enough. Thanks very much for your feedback. I promise I
will improve the content better.

---

Enough for talking. The problem today is about building a feed system. It is a collection of feeds, and store it in our db.
So Reddit will be the first feed source that we want to collect. I will split the big problem to a few small problems, so
the level of annoying will be reduced a bit for you :grin:.

This is the first TDD Kata I do with a real problem, and as I said, I will use OOP for this kata. Because OOP til now is the
best way to represent a real problem.

As the title of the kata, I and you should only care about collecting Reddit RSS only. If we don't do it, we will keep in
mind somethings like FeedSource, DatabaseGateway, RedditFeedSource inhirited from FeedSource. Too headache with the
class hirachy right? Why don't make it easier by just limit the problem in to `Get the Reddit RSS`.
