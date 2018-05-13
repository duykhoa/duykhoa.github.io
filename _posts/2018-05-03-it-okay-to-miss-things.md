---
layout: post
title: It's okay to miss things

---

The internet technology changes the communication completely. It takes milliseconds to deliver your message to someone, the receiver can see your message less that a second.
But there is a problem, we receive a hundred (plus) of notifications everyday from different communication apps, news, social network feeds.
To read and answer them (and reply) isn't an easy job. This is my idea to survive in the notification flood.

9.30 AM, I arrive to the office. I go to my desk and turn on my laptop, checking Slack, Github PRs and notifications, checking emails, writing some updates on Slack (just like post a FB status everyday).

If nothing's special, I am start my day with daily todo list, priotize the important things to be done.
It take a bit of time but the returned value is significant. If you doubt about it, read the book [Eat that frog](https://www.briantracy.com/catalog/eat-that-frog-3rd-edition)

Then I review my colleague Pull Requests. It usually takes a bit longer than 30 mins.

I take a break and start working on my tasks.

> PING!

A beautiful TCP package delivered to my machine, with a natural reaction, I usually acknowledge by saying Hi/Morning - means "Yes I received the package".
I don't want the man to man connection take too long to response, it makes people feel **my** communication system is unreliable.

The handshake protocol is successfully setup, the information exchanging process start happening in a TCP socket.

Some more TCP packages are broadcast in different channels.

Thanks for modern web browsers to show a little banner on my top right screen, I can get notified when someone is talking somewhere.
Just simply clicking on the banner, I can follow them to the place that conversation is happening, which saves me time to figure out.

I also use smart phone, it's even more helpful. All the notifications are there, I can scan through each notification and knowing where are they from.
Joining each conversation, making sure I don't miss anything, provide useful information to my colleagues can't be easier.

What is the time for me to focus on my own stuffs? Seem like not much.

Different with TCP protocol, which is indefinitely waiting until the reply message arrive. A retry mechanism could be triggered if there is no response after a certain time (how long is depended on the patience configuration), another TCP package could arrived using the same system, sometimes can be in different systems, or back to the old slow Dial-up connection. And sometimes it can switch to UDP protocol.

I used a bit of metaphor in talking of TCP UDP, I hope you don't feel annoyed.

> Having Slack open all day is truly death by a thousand paper cuts for your productivity.

-- [I am Devloper](https://twitter.com/iamdevloper/status/977139158731972608)

That hurts.

I don't hate distraction, another way to say is I don't hate people annoying me when I am working. I hate myself when I can't finish my works as promised. I have to kill distraction to boost up my productivity.

I start re-reading the book "The Productive programmer" of Neal Ford. A good book for every developer who wants to improve the productivity, get thing done in limited timeline. It's important to read many books as we can, and not necessary to remember everything from the book. When we face a problem, we know there is a book about it, we can always go back to the book. Now I am taught this lesson well.

Personally, I prefer the concept of the book rather than the specific technique and example. I take those concepts from the book and apply them to my current working flow.

One of those techniques is "Quiet time". It's the time span that we can focus on our work.

For my current job, I can find 2 quiet time spans from 11AM - 12PM (1 hour), and 1.30PM - 3PM (1.5 hour) and sometimes 4.30PM - 6PM (1.5 hours). Between those time spans, I can do different works that are less affects to the key metrics.

This technique works best when people can maximize their performance during the short period. I can drink a soft drink and coffee to maximize my focus. Based on some researches, after drinking coffee, we may feel a bit sleepy during first 30 mins, which exactly the time needed to get into [a flow](https://en.wikipedia.org/wiki/Flow_(psychology))

3 questions I have to answers before start my quiet time

1. What will be done?
1. How important it is?
1. Is it affected to the key metrics?

What can I get done in an hour and a half is a good question. Human attention doesn't last long. If I can't find out what I can get done during a quiet time, I don't start the quiet time, but go back and breakdown the task, clarify the requirement and the scope of the task again. It's better to figure out them before working on the story.

The second question is how important my work is. Sometimes I can spend the whole day to fix a CSS. This is important to me, but not for the team. However, it doesn't apply for all the time. Imagine this CSS is written by you, and it doesn't written well, and your colleagues start copying this CSS to many places. Now it's bad, it bothers you whole day and night. If I were in this situation (yes, I am), I wish I could write it properly from the beginning.

Don't misunderstand the importance of the task with the value of the task to the key metrics. Writting document is valueable, but it's hard to measure in term of key metrics. The key metrics are designed to show the quality of the product, the team that people who don't have technical background can understand. In an Agile company, there are many metrics for the team, like burn down rate, velocity, rejection rate, ... Some people suggest to set customer satisfaction and revenue. The customer satisfaction is count by the error rate, the reliability of the system, how long does it take to release new feature... However, this metric depends on vary costs and it's more about the overall company effectiveness.

I don't disagree with the idea of using those key metrics, and encourage each person thinking them before putting a story to icebox, bottom of backlog and start working on it. Yeah, we have 3 times to think about those business metrics.

And of course, by talking about produce-wide metrics, I don't mean to sacrify the development metrics. It's about the maintenance cost, testing cost, training cost...

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.
-- Martin Fowler, [CodeWisdom](https://twitter.com/codewisdom/status/951529719937323010?lang=en)

It tells us something. Software development is man to man work. The product of it brings values to both team and product.
We may deliver a lot of feature with a ton of technical debts and neglect the maintainance cost.

Unless people tell you to neglect the cost of maintance and you know it isn't a lie, then you can go ahead to create as much of technical debt, hacky stuffs as you want.

Most of the time customer doesn't know what they want, you can't use them to excuse for create a legacy system. Remember the story of [scorpion and a frog](https://en.wikipedia.org/wiki/The_Scorpion_and_the_Frog). Be aware of. Don't neglect our team metrics, our values on building maintainable software. And [learn to say no](https://www.amazon.com/Clean-Coder-Conduct-Professional-Programmers/dp/0137081073) when we have to.

To summarize, productivity is a result of technique, planning, and execution. Quiet time technique create quality time spans to produce the most important  works in limited of time. Planning ahead helps us evaluate what is really important, it allows us to focus on an individual task at a time. The final step is execution, it's the most important step to produce result. There is always enough time to raise the standard of code quality, not cutting the corner if we have discipline of following the first two steps to create the quiet time and planning, prioritize the works.

The opening argument isn't the main focus of this post. I found there is nothing wrong with the internet, nothing is wrong with the communication system. If we know to take values from, it'll help us improve our productivity. It's more important to learn how to use those key metrics to measure our performances, and the distractions from the communication system is no longer a problem.
