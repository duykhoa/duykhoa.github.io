---
layout: post
title: Find the smallest prime that less or equal than n
---

Hi there, I am Kevin Tran. Welcome back to TDD Kata.

The kata requirement is exactly the same with the title of this post.

```
  Find the smallest prime that less or equal than n
```

For example, given the value n = 9, we have the value 7, and n = 50, we have the value 47.

---

Well, which test should we write first? I want to check a number is a prime, so we start with it first.

```ruby
  def test_is_prime
    assert_equal(true, is_prime?(2))
  end
```

**Failed** To fix it, we simply just add

```ruby
  def is_prime?(n)
    true
  end
```

**Passed** But there is nothing to do write, until we can write a failling test.
Here is it

```ruby
  assert_equal(true, is_prime?(4))
```

Why not 3? You know the answer, because the test is still passed. And we don't want this test.

To make it passed again, we can write this:

```ruby
  def is_prime?(n)
    if n % 2 == 0
      return false
    end

    return true
  end
```

Hmm, it is still failed. Because in this case, 2 is divisible by 2. So we can add a condition like that

```ruby
  def is_prime?(n)
    if n ==2
      return true
    end

    if n % 2 == 0
      return false
    end

    return true
  end
```

Next testcase must be 9, because otherwise, 6, 8 will passed because they are divisible by 2.

```ruby
  assert_equal(false, is_prime?(9))
```

```ruby
def is_prime?(n)
  if n == 2
    return true
  end

  i = 2
  if n % i == 0
    return false
  end

  i = 3
  if n % i == 0
    return false
  end

  return true
end
```

**Passed**. But I know the current code will be failed for n = 3. To make it passed, we can add a condition

```ruby
def is_prime?(n)
  if n == 2 || n == 3
    return true
  end

  i = 2
  if n % i == 0
    return false
  end

  i = 3
  if n % i == 0
    return false
  end

  return true
end
```

**Passed**, But at this point, I think we can introduce a loop to refactor the current code

```ruby
def is_prime?(n)
  if n == 2 || n == 3
  return true
  end

  i = 2

  while i * i <= n
    if n % i == 0
      return false
    end

    i += 1
  end

  return true
  end
```

Why the condition is `i * i < n`? I knew this from math. But actually if we don't even know math, for n = 9,
the numbers we need to check is 2, 3; for 8, it is 2; for 25, it's 2,3,4,5. Let's see

```ruby
assert_equal(false, is_prime?(25))
```

or bigger value

```ruby
assert_equal(false, is_prime?(991))
```

**Passed**. Here is a refactored version

```ruby
def is_prime?(n)
  i = 2
  is_prime = true

  while i * i <= n && is_prime = (n % i != 0)
    i += 1
  end

  is_prime
end
```

We remove the condition to check if n equal to 2, 3 and change the code inside the loop command a bit.
I don't say that it's better or not, it depends on your style.

Good. We're done with the first part. For the second part, which is finding the biggest prime that less or equal than n,
we can introduce a loop until we find the first prime, then return and exit. Sound easy right, but let's add a test

```ruby
def test_find_max_prime_less_than
  assert_equal(2,find_max_prime_less_than(2))
end
```

```ruby
def find_max_prime_less_than(n)
  n
end
```

The next testcase is n = 4, and we expect the result is 3. You know why we don't test n = 3 already right?
If not, read the post again, haha. I think it's time to introduce the some complicated code like that.

```ruby
def test_find_max_prime_less_than
  assert_equal(2,find_max_prime_less_than(4))
end
```

```ruby
def find_max_prime_less_than(n)
  i = n
  while !is_prime?(i)
    i -= 1
  end

  i
end
```

**Passed**. Okay, time for adding more test with bigger numbers.

```ruby
assert_equal(1999993,find_max_prime_less_than(2_000_000))
assert_equal(19999999,find_max_prime_less_than(20_000_000))
```

Yo, quite fast. So that's it for this TDD kata. I hope you learn something. See you again in the next TDD Kata.
