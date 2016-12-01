---
layout: post
title: Convert Binary number into Integer
---

Welcome back to TDD Kata blog.

Before going for this problem, I am gonna share with you guys some thoughts about the previous problem: Game of life.

If you want to see the Game of Life Kata, here is the [link - check it out](https://duykhoa.github.io/katas/game-of-life/)
So we did solve the game of life problem by TDD, and the code is worked. You may ask whether it is the best code.

For me it depends. If you want you code really SOLID, it's maynot. For example, look at the `iterate` method, it handles
more than one responsibilities, it duplicates the input and generate the output. It violates the Single Responsibility.
But if you just want the code that easy to read, and easy to refactor. If we want to make it single reponsibility, just
create another method and bring the code to the new one, then you can test the new method easily.

The next thing is the test. If you remember, when I added `live_neighbors_count` method, I didn't add any test.
So this piece of code has no test, absolutely no test, should be a defect? May be there is an error.

Yes, what I said in the Game of life kata is we shouldn't add test. Because we are in refactor stage, we only improve code
so we shouldn't introduce new test. But the most important reason is the `iterate` logic still not finished yet.
It means the `live_neighbors_count` method can be changed very soon. And actually when we write code for the next rule of
Game of life, the logic is changed.

That isn't a rule that we shouldn't write test for these methods, also not a rule that one class should have only 1 `call`ed
method. If you limit a class with 1 public method (like `call`), you may end up with a lot of classes and don't know how
to manage them correctly, it can lead you to a big trouble.

Another thing is the TDD rule #1, only write a test that make the code failed. It means if you want to write tests for the
`live_neighbors_count` method, you need to add a failing test. But when you are in that stage, you may not know what is missing
so a better way is to implement the next Game of life's rule, then you can see the missing logic; and when you finishing make
the test passed, you also included the logic to the `live_neighbors_count` already. Therefore no need for the `live_neighbors_count`
needed.

I know that even I can give you more reasons to not writing test for `live_neighbors_count` method, someone will still feel it
comfortable. I am planning to make a kata to comparse if we write test and we don't write test, which one will improve productivity,
and make the code less bug, easy to maintain... but not this one, haha. Hope you guys let me know your thoughts, questions in the
comments, so I can collect and make that kata to answer you.

Okay, that's what I really want to discuss with you, please let me know if you agree or not.

---

Let's talk about the TDD Kata today, it's about converting the binary back to integer.

For example: `10` is 2 in decimal, `11` is 3 in decimal. Let's start to implement this function.

As usual, we start with a `test_nothing`.


```ruby
require 'minitest/autorun'

class ConvertBinaryInt < Minitest::Test
  def test_nothing
    assert_equal(true, true)
  end
end
```

Then the next test will be

```ruby
def test_convert_binaray_to_int
  assert_equal(1, convert([0,1]))
end
```

So we want to convert an array of [0, 1] to value 1. Ofcourse it returns error because we don't have the `convert` method yet.
We just need to add the method

```ruby
def convert(arr)
  1
end
```

**Passed** Yay, that's cool. We can write a test to make it failed. You guess what is it

```ruby
assert_equal(2, convert([1,0]))
```

**Failed**. Well, we can do this to make the test worked

```ruby
def convert(arr)
  arr[1] + arr[0] * 2
end
```

**Passed** Easy. What is the next testcase? May be with [1, 1]? No, because the code already includes this case.
We can go with an array with 3 elements. How about [0, 0, 1]?

```ruby
assert_equal(1, convert([0, 0, 1]))
```

**Failed**, because with the current code, it only gets the position 0 and 1, and ignore the last one (position 2),
to make it passed, we can split the flow base on the size of the array

```ruby
def convert(arr)
  s = 0

  if arr.size == 2
    s = arr[1] + arr[0] * 2
  end

  if arr.size == 3
    s = arr[2] + arr[1] * 2
  end

  s
end
```

**Passed** Wait, if you notices, we don't check for the array with 1 element, should be a bug?
That's the things we usually have in mind when we write code with TDD. We write test & code in a small iteration,
we transform the code to a different form, then we figure out bugs, issues. With a small feedback loop, hunting bug
is much more easier. Okay, we note that there is a problem when the array has only 1 element, we will soon go back to it.
For now, let's keep going with 3 elements array. :smile:

The next test can be [1,0,0], because the current code just ignore the first element, let's see

```ruby
assert_equal(4, convert([1, 0, 0]))
```

**Failed**. To make it passed, we can do this

```ruby
if arr.size == 3
  s = arr[2] + arr[1] * 2 + arr[0] * 2 * 2
end
```

It is passed. I think it's time to wear the **Refactoring** hat.

Let me paste the full code here, so we can see some smells

```ruby
def convert(arr)
  s = 0

  if arr.size == 2
    s = arr[1] + arr[0] * 2
  end

  if arr.size == 3
    s = arr[2] + arr[1] * 2 + arr[0] * 2 * 2
  end

  s
end
```

What I see in the 3 elements array is, the arr[0] need to multiply with 2^2, but the arr[1] with 2^1, and the arr[0] with 2^0
sound like a loop. Let me introduce the loop

```ruby
if arr.size == 3
  i = 2
  while i >=0
    s = s + arr[i] * 2**(2-i)
    i -= 1
  end
end
```

**Passed**. The initial value of i (2), it is the `arr.size - 1` right? Also the `2 - i` And I guess with the loop statement, we can apply it to 2 elements array also.

```ruby
def convert(arr)
  s = 0
  i = arr.size - 1

  while i >=0
    s = s + arr[i] * 2**(arr.size - 1 - i)
    i -= 1
  end

  s
end
```

Well, it's passed, I change every appearance of `2`, like the initial value of `i`, and in the power of 2 to `arr.size - 1`
and it works for 2 and 3 elements array. Now the `arr.size - 1` appears twice, so I make it an variable

```ruby
def convert(arr)
  s = 0
  last_item_pos = arr.size - 1
  i = last_item_pos

  while i >=0
    s = s + arr[i] * 2**(last_item_pos - i)
    i -= 1
  end

  s
end
```

But I don't like to use `while` loop, so I guess I can transform it to a range

```ruby
s = 0
last_item_pos = arr.size - 1

(0..last_item_pos).each do |j|
  i = last_item_pos - j
  s = s + arr[i] * 2**(last_item_pos - i)
end

s
```

I would love to stop refactoring here. Although you can go one more step like this

```ruby
def convert(arr)
  last_item_pos = arr.size - 1

  (0..last_item_pos).inject(0) do |s, j|
    i = last_item_pos - j
    s + arr[i] * 2**j
  end
end
```

Such a brilliant code! I love my code so much haha.

Is it finished? No, if you remembered, the code didn't cover the 1 element array case.
May be we want to checkit out, let write 2 tests

```ruby
assert_equal(1, convert([1]))
assert_equal(0, convert([0]))
```

**Passed**. Since we transfered the code, we included the logic to check 1 element array already.
So may be there is an hypothesis that when the logic becomes more generic, the number of cases we missed will be less than.

How about the test with big number, let's try with

```ruby
assert_equal(8779648525157126756537035775337127464873441751311502405233576708230210494378581122997495198359169805, convert([1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 1, 0, 1, 1, 0, 0, 0, 1, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 0, 0, 1, 0, 0, 1, 0, 1, 1, 1, 0, 0, 1, 0, 1, 1, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 1, 0, 1, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 0, 0, 0, 1, 1, 1, 0, 0, 1, 0, 0, 0, 0, 1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, 0, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 0, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 0, 1, 0, 1, 0, 1, 1, 0, 0, 1, 0, 0, 1, 1, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, 1, 0, 0, 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 0, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 1, 1, 0, 0, 1, 0, 1, 0, 1, 1, 0, 0, 0, 0, 1, 1, 0, 1]))
```

**Passed** This one is just more fun to write than testing anything.
I used the `#to_s(2)` method from Ruby to convert the result to binary number.

So that's it. Thanks for reading the TDD Kata, I hope you enjoy it. See you next time for the next TDD kata.
