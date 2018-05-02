---
layout: post
title: Ant Philosophy

---

Today is the "International Workers' Day 2018" (1 May). By this occasion,  I would share some random thougths about an tiny animal with workaholics, the ant.

I have a good relationship with ant, although I don't like them appear in my food or my room.
Ant helped me to finish my thesis project, ant help me discover the power of seriousness and longterm planning.

Wait...What? Most of people immediately react like that when I talk with them about ant.
That's why I write this blog post to explain about it.

Here is the ant philosophy:

- Ant never quit
  Do you see ant stop trying before? How long do they try until they find a way to get over it. No matter how difficult it is, it always try to find a way.
- Ant think about winter all summer
- Ant think about spring all winter
- Ant think "all you possibily can"
- Ant learn to trust the team mate.

They think both positive and negative. In summer, they prepare food for the winter. In winter, they can't go out, but they always be ready for
the first day the winter goes away. They don't lose hope, and they can't wait to get out of their nest on the first warming day.

By studing the ant, I found a lot of things that human can learn from. Human life is like 4 seasons, we have learned to take the most from it.
Instead of trying, prepare for the winter, and take full advantage of the spring, we choose to complain about it.

To see more about the 4 seasons in life lesson, check out this video:

[![4 seasons in life](https://img.youtube.com/vi/IBD03lfmdz4/0.jpg)](https://www.youtube.com/watch?v=IBD03lfmdz4)

That is the first part of this post, I call it as philosophy part. I will share how I apply the philosophy ant to my final thesis.

In reality, a "nondeterministic polynomial time" (NP) has no way to determine the most optimize solution. A local optimization solution is usually acceptable.
To increase the chance get through the local optimization, the heuristic algorithm introduce a random factor. This can be described as the solution is randomly eliminated without a real evaluation.

People start thinking of how to optimize not just the heuristic algorithm, but the random algorithm. That's where the ant colony algorithm is applied.

The ant never quit, unless we terminate it.

```ruby
do
  releaseAntColony
  daemonAction
  pheromoneUpdate
while (user_termination)
```

For example, we need the ant to help finding the shortest path to find food.
At first, the ant colony has no clue where to find the food, so they randomly try every posible solution. After they reach the food, they go back and leave the pheromone in the path they found.

The second ant colony is released to do the same job. This time they don't randomly try, they use the pheromone density to decide which steps they should go. They trust their team mate, but not blindly follow, sometimes they will decide to try different way.

After many times trying, there is one path with the highest phoromenone evaporation, and this path becomes the favorite path for most ants.

However, from time to time, the pheromone evaporates, and it give chance for the ant to try a diferent path.

If you want to know more, checkout [this link of ACO](https://en.wikipedia.org/wiki/Ant_colony_optimization_algorithms)

This short post is finished early here. I hope you will find something interested you in knowing how the ant colony work, and may the ant philosophy make you love your work, your job, your career :cross-finger:.
