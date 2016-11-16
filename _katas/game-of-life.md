---
layout: post
title: Game of Life
---

Hi there, Kevin here. Welcome to another TDD Kata.

In this TDD Kata, I will show you how to solve the [Game of life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)
I choose it for my second TDD kata because the problem is very classic. Basically the `game` has only 4 rules,
depend on the input, the `game` can generate different kind of output. But when we let it runs again, using the
current output as the next input for the next one, you can get output like that:

![https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif](https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif)

or

![https://upload.wikimedia.org/wikipedia/commons/0/07/Game_of_life_pulsar.gif](https://upload.wikimedia.org/wikipedia/commons/0/07/Game_of_life_pulsar.gif)

So during this TDD Kata, I will help you go through these game rules, and how we implement it using TDD technique.


