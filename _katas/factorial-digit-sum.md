---
layout: post
title: Factorial digit sum
---

Welcome to the first TDD kata.

In every TDD kata I use TDD to solve a problem, the problem can be one of [Project Euler](https://projecteuler.net), or [Code Abbey](http://www.codeabbey.com/) problems, or some where else. So beside learning TDD, we can also learn math and algorithm.

Okay, let's start.

First, let me explain the requirement. This is the [problem 20th](https://projecteuler.net/problem=20) in [Project Euler].
(https://projecteuler.net)


```md
  n! means n × (n − 1) × ... × 3 × 2 × 1

  For example, 10! = 10 × 9 × ... × 3 × 2 × 1 = 3628800,
  and the sum of the digits in the number 10! is 3 + 6 + 2 + 8 + 8 + 0 + 0 = 27.

  Find the sum of the digits in the number 100!
```

Sound like an easy one huh?

After reading the requirement, we know the problem includes 2 operators: calculate factorial and sum of digits.

The calculate factorial will received a number, and return a factorial of this number, so we have a method call
`factorial(n)`

The sum of digits operator will receive a number, and return a number is the sum of all digits (e.g. 13 -> 4, 21 -> 3)
so we have a method call `sum_digits(n)`

Oh, I will use Ruby for this kata. Because I'm quite a fan of [Minitest](https://github.com/seattlerb/minitest). If you aren't familar with Ruby neither Minitest, why don't give it a try, I bet you will love it :relaxed:

---

What is the first test I should write? Just follow the rule of TDD, we have the first one: `test_nothing`

```ruby

# file_name: sum_digits.rb

require 'minitest/autorun'

class SumDigitsTest < Minitest::Test
  def test_nothing
    assert_equal(true, true)
  end
end
```

The output of `ruby sum_digits.rb` is (ofcourse :grin:)

```sh
$ ruby sum_digits.rb
Run options: --seed 28516

# Running:

.

Finished in 0.002010s, 497.5124 runs/s, 497.5124 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

I want to implement the factorial method first, so in the next section, we will write for factorial method.

The first test for this method is

```ruby
# file_name: sum_digits.rb

  def test_factorial
    assert_equal(1, factorial(0))
  end

```

We want our factorial method will return `1` for `0!`, but we haven't define the method yet, so we get the error:

```sh
NoMethodError: undefined method `factorial' for #<SumDigitsTest:0x007fc557195270>
    sum_digits.rb:5:in `test_factorial'
```

To fix the error, I simply add the definition of this method like

```ruby
  def factorial(n)
    1
  end
```

It is passed. We just write enough logic to pass the test, if we want to write more code (actually the correct logic for factorial), we need to add more tests.

The next test won't be `assert_equal(1, factorial(1))`, because the tests are still passed, so the test is not neccessary.
I choose n=2 instead

```ruby
    assert_equal(2, factorial(2))
```

The test will be failed. How can I fix? Here is a way

```ruby
  def factorial(n)
    if n < 2
      1
    else
      n
    end
  end
```

We move to the next test that make the code fail, I choose n=3

```
    assert_equal(6, factorial(3))
```

The test will be failed. To fix it, hmm, can be write like that???

```ruby
    if n < 2
      1
    else
      if n == 3
        2 * n
      else
        2
      end
    end
```

the logic is, for the case n >= 2, if n == 2, we just return 2, else we return `2 * n`. We see it's wrong because we know the formula already, but assume that we don't know that our method will execute for n == 4,5,6..., so this implementation is perfectly correct, right?

To prove this code is wrong, we simply add another test with n == 4

```
    assert_equal(24, factorial(4))
```

yay, the logic is wrong, so how to fix it? I can put some messy code to fix like that

```ruby
  def factorial(n)
    if n < 2
      1
    else
      if n == 4
        return 2 * 3 * n
      end

      if n == 3
        2 * n
      else
        2
      end
    end
```

Output:

```sh
$ ruby sum_digits.rb
Run options: --seed 50174

# Running:

.

Finished in 0.001329s, 752.4455 runs/s, 3009.7818 assertions/s.

1 runs, 4 assertions, 0 failures, 0 errors, 0 skips
```

Pass. but it's so ugly code, we should fix it before going to the next one.

First, I will make fix this code first

```ruby
if n == 4
  return 2 * 3 * n
end

if n == 3
  2 * n
else
  2
end
```

to


```ruby
if n == 4
  s = 2
  s = s * 3
  s = s * 4
  return s
end

if n == 3
  s = 2
  s = s * 3
  return s
end

if n == 2
  s = 2
  return s
end
```

With this code, we can easily introduce a while loop

```ruby
if n == 4
  i = 3
  s = 2

  while i <= 4
    s = s * i
    i += 1
  end

  return s
end
```

hoho, it's passed, we will use it for the case n==3

```ruby
if n == 4
  i = 3
  s = 2

  while i <= 4
    s = s * i
    i += 1
  end

  return s
end

if n == 3
  i = 3
  s = 2

  while i <= 3
    s = s * i
    i += 1
  end

  return s
end

```

Wow, at this point, the case 4 and 3 have exactly same code, we can merge them together.
Also I found `i` always end by by n - 1, so the code will become

```ruby
  def factorial(n)
    if n < 2
      1
    else
      i = 3
      s = 2

      while i <= n
        s = s * i
        i += 1
      end

      return s
    end
  end
```

But i = 3 and s = 2 seem like a hard code, right? If I start with x = 1, mean s must be 1. I think it'll pass. Let's try

```ruby
if n < 2
  return 1
else
  i = 1
  s = 1

  while i <= n
    s = s * i
    i += 1
  end

  return s
end
```

**Pass** Yay! I think the code is good enough for now, but for some rubyists they will prefer to use a better syntax, like

```ruby
if n < 2
  1
else
  (1..n).inject(1) { |s,i| s * i }
end
```

Whatever haha, the point is, with 4 testcases, we finished the code for the factorial method. With each test case, the code gets more abstract and become more generic. And after 4 testcases, the code can cover all cases of input. I hope you will follow the progress of writing test, adding code and refactor to see how to generate the final code that works correctly and fully covered by the tests.

The next thing is `sum_digits` method, I suggest you to try it yourself before continue reading it. I just post the tests, and the final code with a brieft explanation, so make sure you try or think about it.

1. Test

  ```ruby
  assert_equal(1, sum_digits(1))
  ```
  
  Code

  ```ruby
  def sum_digits(n)
    1
  end
  ```

- Test

  ```ruby
  assert_equal(2, sum_digits(2))
  ```
  
  Code

  ```ruby
  def sum_digits(n)
    n
  end
  ```

- Test

  ```ruby
  assert_equal(1, sum_digits(10))
  ```
  
  Code

  ```ruby
  def sum_digits(n)
    n % 10
  end
  ```

- Test

  ```ruby
  assert_equal(2, sum_digits(11))
  ```
  
  Code

  ```ruby
  def sum_digits(n)
    n / 10 + n % 10
  end
  ```

- Test

  ```ruby
  assert_equal(1, sum_digits(100))
  ```
  
  Code

  ```ruby
  def sum_digits(n)
    s = 0

    if n >= 100
      n = n / 10
    end

    s = s + n / 10 + n % 10
    s
  end
  ```
  
- Test

  ```ruby
  assert_equal(1, sum_digits(1000))
  ```
  
  Code

  ```ruby
    def sum_digits(n)
    s = 0

    if n >= 1000
      s = s + n % 10
      n = n / 10 # -> 100

      s = s + n % 10
      n = n / 10 #-> 10
    end

    if n >= 100
      s = s + n % 10
      n = n / 10
    end

    s = s + n / 10 + n % 10
    s
  end
  ```
  
  Refactor
  
  ```
  def sum_digits(n)
    s = 0

    while n >= 100
      s = s + n % 10
      n = n / 10
    end

    s = s + n / 10 + n % 10
    s
  end
  ```

  More refactor
  
  ```
  def sum_digits(n)
    s = 0

    while n > 0
      s = s + n % 10
      n = n / 10
    end

    s
  end

  ```
  
Done, yo. I hope you enjoy the kata like me. I know it may not make sense for you in some points, why don't I do write the code in a different way, or you wonder how I refactor the code to another form, is it any rule to do this. If you have this in mind, be patient, you are getting some ideas about TDD already. Please follow my next TDD Katas, I will explain more about these techniques I am using in this one and another katas.

Hope you enjoy it. Please drop me a feedback below. Thank so much and see you again next week.
