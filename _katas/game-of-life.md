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

---

For you guys if you don't know about the Game of Life yet, I will write some short description first.
It's a zero player game, the game will generate the next state by the input (initialze state) base on these 4 rules
The input is a 2-demension array, each element (cell) has 2 status: `live` or `dead`. Each cell has 8 neighbors except
the cell near the edge of the array.

To make it easy, we will assign a cell to be `0` if it's a `dead` cell, and `1` for `live` cell.

For example, this array

```ruby
  arr = [
    [0,0,0]
    [0,1,0]
    [0,0,0]
  ]
```

It has only 1 live cell at `arr[1][1]`, and this cell has 8 neighbors, all of them are `dead` cells.

Next thing is the game's rules. We talk about it for a moment, here are they:

```
- Any live cell with fewer than two live neighbours dies, as if caused by under-population.
- Any live cell with two or three live neighbours lives on to the next generation.
- Any live cell with more than three live neighbours dies, as if by over-population.
- Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.

from wikipedia https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
```

These rules are quite straigth forward, for instance the first one mean if a cell with value `1`(live cell)
has fewer 2 (<2) live neighbor will be die in the next iteration.

With this array

```
  arr = [
    [1, 0]
    [0, 1]
  ]
```

This cell at arr[0][0] will die because it only has 1 live neighbor at arr[1][1]

For other rules, I will explain while I implement the `game`. Don't be worry if you still understand them.

---

At the begin, as a practice, I add a `test_nothing` method to make sure I setup everything correctly

```ruby

require 'minitest/autorun'

class GameOfLifeTest < Minitest::Test
  def test_nothing
    assert_equal(true, true)
  end
end
```

Cool, what is next? So far what I get from the requirements is I need to count how many `live` neighbors
for each cell. So if input with an array with 1 element only, the live neighbors will be 0, then the
element will die because the rule #1: the cell will die if fewer than 2 live neighbors.

We have the test

```
  def test_iterate
    arr = [[1]]
    result = iterate(arr)
    assert_equal(0, arr[0][0])
  end
```

It failed because we don't have the iterate method.  Also we know there is 0 live neighbor with this input,
we can change the only value of this array to `0`

```
  def iterate(arr)
    arr[0][0] = 0
  end
```

Passed!. But we may miss the live neighbors count, let's try with another test, and in that input
the arr[0][0] has exactly 2 live neighbors, so the value is remained, it's still live.

```
    arr = [
      [1, 1],
      [1, 0],
    ]

    result = iterate(arr)
    assert_equal(1, arr[0][0])
```

So it will be fail. We need to count the number of live neighbors and check if the number is fewer than 2
Basically we just need to check for the cell at [0][1], [1,0] and [1,1].

The code will look like

```
  def iterate(arr)
    live_count = 0
    i = 0
    while i < arr.size
      j = 0

      while j < arr.size
        if arr[i][j] == 1
          live_count += 1
        end

        j += 1
      end

      i += 1
    end

    if arr[0][0] == 1 && live_count < 2
      arr[0][0] = 0
    end
  end

```

and the test is

```
  def test_iterate
    arr = [[1]]
    result = iterate(arr)
    assert_equal(0, arr[0][0])

    arr = [
      [1, 1],
      [1, 0],
    ]

    result = iterate(arr)
    assert_equal(1, arr[0][0])
  end
```

Passed

```sh
  Run options: --seed 26161

  # Running:

  .

  Finished in 0.001290s, 775.1938 runs/s, 1550.3876 assertions/s.

  1 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

Notice here we only check for the value of arr[0][0] at this moment. So we need to make the
code work for every cell. First, we add the test

```
  arr = [
    [0, 0],
    [1, 1],
  ]

  result = iterate(arr)
  assert_equal(0, arr[1][1])
```

It failed. How do we fix?
Look at the `i`, `j` at the current code. I set it to 0,0. We can simply put i, j to a loop
i, j will sequentially increase from 0 to the arr.size - 1

```
  x = 0

  while x < arr.size
    y = 0

    while y < arr.size
      i = x
      live_count = 0

      while i < arr.size
        j = y

        while j < arr.size
          if arr[i][j] == 1 && !(i == x && j == y)
            live_count += 1
          end

          j += 1
        end

        i += 1
      end

      if arr[x][y] == 1 && live_count < 2
        arr[x][y] = 0
      end

      y += 1
    end
    x += 1
  end

end
```

Passed but so ugly right? Let's refactor it now. I can't wait for it anymore. Such a messy code :yummy:
