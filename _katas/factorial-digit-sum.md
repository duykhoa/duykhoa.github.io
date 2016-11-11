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
