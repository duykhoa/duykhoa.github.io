---
layout: post
title: Game of Life
---

Hi there, Kevin here. Welcome to another TDD Kata.

In this TDD Kata, I will show you how to solve the [Game of life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)
I choose it for my second TDD kata because the problem is very classic. The `game` has only 4 rules,
depend on the input, the `game` generate different output. When we let it runs again several times, using the
current output as the input for the next run, you can get output like that:

![https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif](https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif)

or

![https://upload.wikimedia.org/wikipedia/commons/0/07/Game_of_life_pulsar.gif](https://upload.wikimedia.org/wikipedia/commons/0/07/Game_of_life_pulsar.gif)

During this TDD Kata, I will go through this game's rules, and how we implement it using TDD technique.

---

In case you haven't known about the Game of Life, I will write a short description first.
It's a zero player game, the game will generate the next state by the input (initialze state) base on these 4 rules
The input is a 2-demension array, each element (cell) has 2 status: `live` or `dead`. Each cell has 8 neighbors except
the cell near the edge of the array.

To make it easy, we will assign a cell `0` if it's a `dead` cell, and `1` if it's `live` cell.

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
1. Any live cell with fewer than two live neighbours dies, as if caused by under-population.
2. Any live cell with two or three live neighbours lives on to the next generation.
3. Any live cell with more than three live neighbours dies, as if by over-population.
4. Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.

from wikipedia https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
```

These rules are quite straigth forward, for instance the rule #1 mean if a cell with value `1`(live cell)
has fewer than 2 (<2) live neighbor will die in the next iteration.

With this array

```
  arr = [
    [1, 0]
    [0, 1]
  ]
```

This cell at arr[0][0] will die because it only has 1 live neighbor at arr[1][1].

I will explain other game's rules while I implement the `game`. Do not be worry if you still cannot understand them.

---

At the begin, as a practice, I add a `test_nothing` method to make sure I setup everything correctly.

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

```ruby
  def test_iterate
    arr = [[1]]
    result = iterate(arr)
    assert_equal(0, arr[0][0])
  end
```

It failed because we don't have the iterate method.  Also we know there is 0 live neighbor with this input,
we can change the only value of this array to `0`

```ruby
  def iterate(arr)
    arr[0][0] = 0
  end
```

**Passed**!. But we may miss the live neighbors count, let's try with another test, and in that input
the arr[0][0] has exactly 2 live neighbors, so the value is remained, it's still live.

```ruby
  arr = [
    [1, 1],
    [1, 0],
  ]

  result = iterate(arr)
  assert_equal(1, arr[0][0])
```

So it's failed. We need to count the number of live neighbors and check if the number is fewer than 2
Basically we just need to check for the cell at [0][1], [1,0] and [1,1].

The code will look like

```ruby
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

    arr
  end

```

and the test is

```ruby
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

**Passed**

```sh
  Run options: --seed 26161

  # Running:

  .

  Finished in 0.001290s, 775.1938 runs/s, 1550.3876 assertions/s.

  1 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

Notice that we only check for the value of arr[0][0] at this moment. So we need to make the
code work for every cell. First, we add the test

```ruby
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

```ruby
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

  arr
end
```

Passed but so ugly right? Let's refactor it now. I can't wait for it anymore. Such a messy code :yummy:

First, I know that the `live_count` should be in a different method, show I move this code out, we have

```ruby
  def iterate(arr)
    x = 0

    while x < arr.size
      y = 0

      while y < arr.size
        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) < 2
          arr[x][y] = 0
        end

        y += 1
      end
      x += 1
    end

    arr
  end

  def live_neighbor_count(x, y, arr)
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

    live_count
  end
```

Basically we just extract the code, nothing is changed much, but the `iterate` method is more readable now.
Wait, this `live_neighbor_count` has no test, so do we need to add some tests for them.
Of course we can, but I don't recommend to stop refactor progress and write more tests. The other reason is
because the `iterate` tests already cover every possible case of `live_neighbor_count` method. So what you
are trying to test right now is just redundant with the current testcases.

What I learned is we should test only the public interface, which is the `iterate` method only.
The `live_neighbor_count` method is not public, we don't want whoelse use it, this method is created to
to be changed soon. We are created it because we want to make the `iterate` simpler. But the logic
for `iterate` hasn't finished, so any methods that supported or to be called by `iterate` are in a
high chance to be changed, destroyed, moved, renamed ...

Okay, many talks, let's go back the refactor stage. I think the code is good enough for now, but the test
isn't nice yet.

```ruby
  arr = [[1]]
  result = iterate(arr)
  assert_equal(0, arr[0][0])

  arr = [
    [1, 1],
    [1, 0],
  ]

  result = iterate(arr)
  assert_equal(1, arr[0][0])

  arr = [
    [0, 0],
    [1, 1],
  ]

  result = iterate(arr)
  assert_equal(0, arr[1][1])
```

The `result = iterate(arr)` is repeated 3 times. Let's change it to

```ruby
  def assert_live(x, y, input_arr)
    assert_stage(1, x, y, input_arr)
  end

  def assert_die(x, y, input_arr)
    assert_stage(0, x, y, input_arr)
  end

  def assert_stage(st, x, y, input_arr)
    result = iterate(input_arr)
    assert_equal(st, result[x][y])
  end

  def test_iterate
    arr = [[1]]
    assert_die(0, 0, arr)

    arr = [
      [1, 1],
      [1, 0],
    ]

    assert_live(0, 0, arr)
    # ...
  end
```

Like that. **Passed**! Beautiful. We continue adding another test to make it failed.

```ruby
  arr = [
    [1, 0, 0],
    [1, 0, 1],
    [1, 1, 1],
  ]

  assert_die(0, 0, arr)
```

Failed! For this array, the element at 0, 0 only have 1 live neighbors, so it should die.
But the test is failed, because the `live_neighbor_count` method is wrong,
the code was true when the array is 2x2, so i, j start from 0 to 1, but in 3x3 array,
the element at 0, 0, i should be 0 -> 1, and j should be start from 0 -> 1, while for the element at
2,2 i should be 1 -> 2, j should be 1 -> 2

We can change the code to

```ruby
  def live_neighbor_count(x, y, arr)
    minx = x - 1
    minx = 0 if minx < 0

    miny = y - 1
    miny = 0 if miny < 0

    maxx = x + 1
    maxx = arr.size - 1 if maxx >= arr.size

    maxy = y + 1
    maxy = arr.size - 1 if maxy >= arr.size

    i = minx
    live_count = 0

    while i <= maxx
      j = miny
      while j <= maxy
        if arr[i][j] == 1 && !(i == x && j == y)
          live_count += 1
        end

        j += 1
      end

      i += 1
    end

    live_count
  end
```

The tests are all passed. Yay. The code is a little bit ugly. Can I skip refactor it... no, shouldn't right?
Here is the version after I do some refactor

```ruby
  def iterate(arr)
    (0...arr.size).each do |x|
      (0...arr.size).each do |y|
        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) < 2
          arr[x][y] = 0
        end
      end
    end

    arr
  end

  def live_neighbor_count(x, y, arr)
    minx = max(x - 1, 0)
    miny = max(y - 1, 0)

    maxx = min(x + 1, arr.size - 1)
    maxy = min(y + 1, arr.size - 1)

    live_count = 0

    (minx..maxx).each do |i|
      (miny..maxy).each do |j|
        if arr[i][j] == 1 && !(i == x && j == y)
          live_count += 1
        end
      end
    end

    live_count
  end

  def min(a, b)
    a < b ? a : b
  end

  def max(a, b)
    a > b ? a : b
  end
```

I think there is nothing to do anymore, we can go ahead and add more test to implement rule #2, #3 and #4.
For rule #2, the live cell is still live, and the next rule actually cover it. Let me put the rule here again, so you
don't need to scroll up again.

```
1 Any live cell with fewer than two live neighbours dies, as if caused by under-population.
2 Any live cell with two or three live neighbours lives on to the next generation.
3 Any live cell with more than three live neighbours dies, as if by over-population.
4 Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.
```

For rule #3, we can easily add the test

```ruby
  arr = [
    [1, 0, 0],
    [1, 1, 1],
    [1, 1, 1],
  ]

  assert_die(1, 1, arr)
```

Simply add to the code

```ruby
  def iterate(arr)
    (0...arr.size).each do |x|
      (0...arr.size).each do |y|
        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) < 2
          arr[x][y] = 0
        end

        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) > 3
          arr[x][y] = 0
        end
      end
    end

    arr
  end
```

Next is rule #4, sound like easy one right

```ruby
  arr = [
    [0, 0, 0],
    [1, 0, 1],
    [0, 1, 0],
  ]

  assert_live(1, 1, arr)
```

We just need to add this code

```ruby
  if arr[x][y] == 0 && live_neighbor_count(x,y,arr) == 3
    arr[x][y] = 1
  end
```

You guess it's passed? It's failed.
The reason is, when `iterate` method scan the element at [1, 0] (row 1, first column), it just has 1 live neighbor
which is at [2, 1], so follow rule #1, it die, but then, for the element at [1, 1], at we see it has exactly 3
neighbors, so it should be live again, but because the element at [1, 0] just die before the `iterate` scan,
it can't change the value to 1. To fix it, we can duplicate the input array.

```ruby
  def iterate(arr)
    new_arr = arr.inject([]) do |r, row|
      r << row.dup
    end

    (0...arr.size).each do |x|
      (0...arr.size).each do |y|
        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) < 2
          new_arr[x][y] = 0
        end

        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) > 3
          new_arr[x][y] = 0
        end

        if arr[x][y] == 0 && live_neighbor_count(x,y,arr) == 3
          new_arr[x][y] = 1
        end
      end
    end

    new_arr
  end
```

It's passed. Great. I think we can do some more refactor on it.
I also test some of pattern on the wiki page

This is the full code for the Game of Life TDD Kata

```ruby
# base on https://en.wikipedia.org/wiki/Conway%27s_Game_of_Lif://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
require 'minitest/autorun'

class GameOfLifeTest < Minitest::Test
  def iterate(arr)
    new_arr = arr.inject([]) do |r, row|
      r << row.dup
    end

    (0...arr.size).each do |x|
      (0...arr.size).each do |y|
        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) < 2
          new_arr[x][y] = 0
        end

        if arr[x][y] == 1 && live_neighbor_count(x,y,arr) > 3
          new_arr[x][y] = 0
        end

        if arr[x][y] == 0 && live_neighbor_count(x,y,arr) == 3
          new_arr[x][y] = 1
        end
      end
    end

    new_arr
  end

  def live_neighbor_count(x, y, arr)
    minx = max(x - 1, 0)
    miny = max(y - 1, 0)

    maxx = min(x + 1, arr.size - 1)
    maxy = min(y + 1, arr.size - 1)

    live_count = 0

    (minx..maxx).each do |i|
      (miny..maxy).each do |j|
        if arr[i][j] == 1 && !(i == x && j == y)
          live_count += 1
        end
      end
    end

    live_count
  end

  def min(a, b)
    a < b ? a : b
  end

  def max(a, b)
    a > b ? a : b
  end

  def assert_live(x, y, input_arr)
    assert_stage(1, x, y, input_arr)
  end

  def assert_die(x, y, input_arr)
    assert_stage(0, x, y, input_arr)
  end

  def assert_stage(st, x, y, input_arr)
    result = iterate(input_arr)
    assert_equal(st, result[x][y])
  end

  def test_iterate
    arr = [[1]]
    assert_die(0, 0, arr)

    arr = [
      [1, 1],
      [1, 0],
    ]

    assert_live(0, 0, arr)

    arr = [
      [0, 0],
      [1, 1],
    ]

    assert_die(1, 1, arr)

    arr = [
      [1, 0, 0],
      [1, 0, 1],
      [1, 1, 1],
    ]

    assert_die(0, 0, arr)

    arr = [
      [1, 0, 0],
      [1, 1, 1],
      [1, 1, 1],
    ]

    assert_die(1, 1, arr)

    arr = [
      [0, 0, 0],
      [1, 0, 1],
      [0, 1, 0],
    ]

    assert_live(1, 1, arr)

    input_arr = [
      [0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0],
      [0, 1, 1, 1, 0],
      [0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0],
    ]

    output_arr = [
      [0, 0, 0, 0, 0],
      [0, 0, 1, 0, 0],
      [0, 0, 1, 0, 0],
      [0, 0, 1, 0, 0],
      [0, 0, 0, 0, 0],
    ]

    assert_equal(output_arr, iterate(input_arr))

    input_arr = [
      [0, 0, 0, 0, 0],
      [0, 1, 1, 0, 0],
      [0, 1, 0, 0, 0],
      [0, 0, 0, 0, 1],
      [0, 0, 0, 1, 1],
    ]

    output_arr = [
      [0, 0, 0, 0, 0],
      [0, 1, 1, 0, 0],
      [0, 1, 1, 0, 0],
      [0, 0, 0, 1, 1],
      [0, 0, 0, 1, 1],
    ]

    assert_equal(output_arr, iterate(input_arr))
  end
end
```

I know it's already long, so I will keep my lesson to the next one. Again, hope you enjoy this kata,
and see you in the next kata very soon.
