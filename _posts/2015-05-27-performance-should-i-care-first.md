---
layout: post
title: Performance, should I care first? 
---

In this post, I want to share my thought about focusing on performance when start developing.

The first step in to produce a software product in general is programming.

However, instead of making something work, we are trying to do it perfectly, make it run fast. I start picking the algorithm to optimize the code.

After many times doing this, I realize it's too early to care about performance. The requirements will change soon. The optimization state will change very soon. Your awesome good code will be removed soon.

At the beginning, all we need is something really works. Several weeks later, the boss will come to you and ask you remove some of them, or rewrite the whole things because the logic doesn't work.

You may concern after finishing a feature, you'll be moved on the next feature, and next feature. It never ends, when we have time to improve the code we wrote.

It's time to talk about automation tests. If we write tests, we can change some modules with confident.

Another concern is our boss, does he give us time to improve the code that already worked well; and if you change, something  can go wrong and the whole system is down.

If your boss know about software development, you can tell him directly. You have to explain why you need to improve some code and ask him for some time. 

In case your boss doesn't understand, you need draw a picture of how better the product will be after changing. It isn't easy for a developer.

Thinking our job like a business, the boss client, the software we're working is business product.
We sell the product to your client because of their demands, it doesn't mean that you must tell them how you do it.
What they need is a software that fulfils all of their requirements.

We understand your product better than client. We know exactly what isn't good, what need to change. The client is unhappy when we tell them nothing is released in next few weeks. However, when he sees a product that works well, all the things that make him annoyed before are gone, and new features are comming every iteration, he'll appreciate.


Back to this post, I always heard something similarly from clients like: we'll have 7 million users, we need a guy must have experience working with performance optimization. The website need to handle at least 1 million requests at the same time. 

Ofcourse there are many websites like that. However, have you asked them back for how long it does take to get 7 million users, are they able to get 7 million users in 1st year. It isn't possible to have this amount of traffics in 1 years, or 2 years. Okay, if unfortunately it happened and I was the business owner, I am happy to pay a huge amount of money to improve the website better. I can either put more instances in AWS, upgrade whatever cloud service and subscription plan. I can hire more Sr. developers and pay more to help scaling the website, improve the code or even rewrite everything in C.

We shouldn't care too much about complex logic, scaling, and performance at the beginning. We focus only on a working software. It's too much to think about all possible cases right at the beginning. Just make it works. I will be back.
