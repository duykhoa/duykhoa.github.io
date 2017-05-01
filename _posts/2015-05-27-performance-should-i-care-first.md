---
layout: post
title: I am not the best developer
---

In this post, I will tell you my thought about performance when I start to developing.

The first step in development process, assume we pass all product plans, database designs, reporting... is coding.
But actually not, many developer instead of thinking of make something work, they are try to be sophisticated at the first time. And so do I. I get the requirements, and think how to do it perfectly, and think how to make it run fast, which algorithm I should use to optimize my code performance.

But I tell you, it's too early to care about the performance. The requirements will change soon for sure. So why you need to write a good performance app now, and you remove them all very soon. It's no point to write a too good code to remove.
At the beginning, we just need something really works, no matter of the code isn't good, well design, because several weeks later, the boss will come to you and ask you remove some of them, or rewrite the whole things because the logic doesn't work.

You may ask me, "Hey Kevin, so if I write my code, and my code works. So later on, I'll move on the next features, and how can I come back and improve the code I write today?"
It's a tough question. But you know what, you always can comeback and improve your code later. You have the tests, so you can change something and run the test again. If it passes, we just move on to next thing. You also may concern about the boss, does he give you time to improve the code that have worked already, if you change, may be.. something wrong and the website is down.

If your boss can understand tech, it's good. You can tell him directly, explain why you need to improve the code base and ask him to expand the time. 
If your boss can't understand tech, ok, you may choose: tell or don't tell. If you choose to tell him, draw a picture of what you make and what he get after changing. I don't mean that you draw a pink one, but at least show him why you need to change the code, and how better the product will be. It may not easy for a developer, I know. OK, so you can't just do thing you need to do. Think about your job like a business, your boss is your client, and the website you're doing is your business product. You sell the product to your client because of their demands, but it doesn't mean that you must tell them how you do it. You understand your product better than your client, and understand better how you create it than your client, right? May be he won't be happy at the first time, but after a while, he sees something that works perfectly, he'll appreciated more than complain you.

Another thing. Did you hear something like: we'll have 7 million users, and you must experience working with performance optimization, so the website can handle 1 million requests at the same time blah blah. Make sense? Yup, but how far to get 7 million users, can we get these users in the 1st day or even the 1st year. No way, right? And even if you get this point, if I was your boss, I am happy to pay a lot of money to improve the website better, either put 10 more instances in AWS, upgrade any cloud services to help my site better, or hire more developers and pay you guys more to help me make the website better.

So, what should you care in the beginning state. No complex logic, no scaleable thought, and no performance need! You should only care about how to make thing works, that's all. I found myself thinking too much about how to make thing scaleable, easily to add a feature/override method. But it's too much, I can think about a very sophisticated solution to solve the problem, but I don't think about how much time and effort to do. So better way, just make it works. Do you agree with me? Drop me  a line below
