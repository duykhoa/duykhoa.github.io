---
layout: post
title: Improve productivity by a simple trick
excerpt_separator: <!--more-->
---

Let me start in this way. I think laziness is an important character of a good developer.

In [ITviec blog](https://itviec.com/blog/luoi-to-ra-ngu-ngoc-la-ban-nang-cua-developer-gioi-2/), I shared that I feel
much better to spend a week writing a tool to automate someworks like copy-paste every minute.

The reason I think I should do it isn't for myself, but for the entire team. For a rough calculation, a team with 5
people, and this repetive task has to be done twice a day, it takes 5 minutes to finished manually, it would take 1400
minutes per month to just do this task. Compare to that amount of time, I spend a week (160 hours) to develop a tool
and save the cost of 1400 - 160 = 1240 hours for the team, plus the mental affection, that definitely is a good
invesment.

Laziness is different with procrastination. For example, you are given a task to eat an elephant.
And people tends to avoiding it by eating some thing else instead. Despite how much they have eaten other stuff, the
elephant is still there and occupies the significant space in the dining table. Laziness isn't only about avoiding to
eat the elephant, but also findout a good way to enjoy it (like adding herbs to make it more tasty or cut it to 5 parts
and cook each part in different ways).

There are many cures to procastinaion. [How to overcome procrastination](https://www.mindtools.com/pages/article/newHTE_96.htm)
article lists down the step to fix the procrastination habit as 3 steps:

1.  Recognize That You're Procrastinating
1.  Work Out WHY You're Procrastinating
1.  Adopt Anti-Procrastination Strategies

If you think you are procrastinate, or you aren't sure whether you are in procrastination mode, I highly recommend to
spend your 7 mins to read this article..

To break the procrastination habit, you have to work on it. It isn't possible to change a habbit overnight.
One book that I have read about stoping procrastination is ["Eat That Frog"](https://g.co/kgs/iAJzTS) by Brian Tracy.
I think I have mentioned about this book a few times in different posts. So check it out if you are struggling to stop
this habbit yourself.

Once a time, I am very disappointed with my ability of procrastination. Yet, it is easy to excuse that I have to spend
time in some strategic thinking, I will work on that when I find a perfect solution. I will go for a run in a day there
is no rain, I can finish work at 6 and not feeling hungry. That's the time that I go to bed with zero thing I have done
for myself, and I want to wake up to work a bit. The time I have "work" - sit in front of the computer at 2AM to read
and there is nothing come to my head.

That time, I hate the fact that I'm so smart to find out all the excuses for myself.
Until oneday, I must say that it's enough, I don't like the way I am anymore, I have to become more discipline about
myself. I start reading what people think and do, how can they are so enthusiastic at work. And I found JimRohn (if he
is still alive, I will call him to ask to become my daddy), his speech teaches me a very simple approach to fix my bad
habbit. It is an extremely simple approach with 2 steps: break it down and write it down.

I don't need to break the task down before writing it. I write everything down.
Just a simple list will do. Before acting on something, I look at my list first. If it is in the list, I start working
on it. If it isn't in the list, I spend about 2-3 mins to decide whether I should put it on the list. If I put it to my
list, should I work on it now, or should I work on something else first?

As a developer, I usually work with computer, in a dark window aka terminal. Tracking my todo list in the terminal is
kind of joy. I thought of finding a package online, but I also want to build one for myself. You know, as developer, we
like to use our own stuff. I was thinking about how to build it.

So I sit down on a Sunday morning, write down the task "Design a todo list" to my todo list. Sound like this is one of
the task that I should get done by today. It may block other works since I need a todo list before doing anything.

My todo list should be able to list down all the works I have done, doing and not started. Plus I can add new item, mark
it as finished. I also want to separate the todo list by date, which mean when I want to open my todo list, I can see
the current date list instead of everything. Or the current date todo list must be on top.

Then I think back a bit, all of those features are all available in any text editor (Vim in this case).

All I need is a script that allows me to create a file with some format similar to `2019-05-12-todolist`,
or even simplier, a script with todo list that open the TODO file.

To install a header that include a date, Vim has a super simple solution for that, by typing `:r! date "+\%Y-\%m-\%d`
I will write a Vim command to make it even nicer. Again, not a high priority currently, put in my Todo list first.

This approach works well for me. I may spend a week to implement a tool to build this todo list, by putting the work
down and spend an hour thinking about it, how can I value from the work I am going to do, I can see it clearer.
That is what I mean "laziness is an important character of a good developer". Laziness is good.

I am now happy to use my old, legacy way to write thing down. I don't need to build another todo tool to be cool, and I
guess it works very well til now.

In fact, if I spend time working on the todo app, it is a sign of procrastination. I really want to build an app, I feel
like I want it. However, it isn't a priority task. It doesn't make me more productive. It is purely a way to avoid doing
what I should do.

I would like to hear your story or your thoughts on procrastination, drop me a line at duykhoa12t[at]gmail.com.

# Other resources

- https://www.njlifehacks.com/how-to-stop-procrastinating/
- https://succeedfeed.com/jim-rohns-2-simple-techniques-to-stop-procrastinating/
