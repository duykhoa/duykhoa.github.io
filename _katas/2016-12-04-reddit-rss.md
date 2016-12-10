---
layout: post
title: Reddit RSS (part 1)
---

Welcome back to TDD Kata.

Well, we already go through a few examples of TDD Kata. I and you already know how to write code to solve a problem using TDD
technique, I hope. Also I have experienced with writing code and writing an explanation next, which I haven't done
before. It's great. I'm kind of lazy developer who never document my code. Sometimes I leave some comments for the method
I wrote, like a contract to another method that I will implement next.

**TLDR;**

One of my TDD addictor, he asked my why don't do a real kata, for example interact with an api or connect to database.
I have considered about it many times, there are my 2 concerns:

- The real problem one is usually aren't like any of katas. The problem is bigger. And with bigger problem, we need more code.
Last time I tried with the `game of life`, it is a problem related to math and computer science only, but still quite long
already. I don't know if we do a real problem, I wonder if my readers - **you** can diggest it?
- For a real problem, I would love to use OOP (object oriented programming), which is class and object. Then we have some more
things to discuss, like dependency inversion, single responsibility and design pattern. There aren't just 3 things, but more
than that. And with my limited knowledge, I am not really sure that I can send the message to you. Also when talk about these
techniques too much, feel like I am trying to overwhelm every one here, haha.

That's my concerns when introducing a real problem for TDD kata. But I decided to do it anyway. So I hope you guys will help me
by give me some comments if I am wrong or describe something not clear enough. Thanks very much for your feedback. I promise I
will improve the content better.

---

Enough for talking. The problem today is about building a feed system. It is a collection of feeds, and store it in our db.
So Reddit will be the first feed source that we want to collect. I will split the big problem to a few small problems, so
the level of annoying will be reduced a bit for you :grin:.

This is the first TDD Kata I do with a real problem, and as I said, I will use OOP for this kata. Because OOP til now is the
best way to represent a real problem.

As the title of the kata, I and you should only care about collecting Reddit RSS only. If we don't do it, we will keep in
mind somethings like FeedSource, DatabaseGateway, RedditFeedSource inhirited from FeedSource. Too headache with the
class hirachy right? Why don't make it easier by just limit the problem in to `Get the Reddit RSS`.
And the RSS url is `https://www.reddit.com/.rss`.


To get started, we have the `test_nothing` as usual. What do we do next?
Okay, to have the RSS, we need to download it first, then convert it to a ruby structure.

Obviously, we see there are 2 class. These classes can be named as "RSS Downloader" and "RSS Parser".
So we can start with one of them? May be `RSSDownloader`, because without downloading, how do we know the content to parse.
Then how to download. We can use Net::HTTP or Httparty gem. The RSS URL is quite hard code, so we can use a `.env` file to
store it, and use a gem to load the .env to get the url variable. Then what is the output? A xml String, we will pass this xml
String to RSS Parser, this one will parse it to some kind of ruby object...

I call the way of design is Depth First. So I start with the class most general class, then explore it's functionalities.
Then I pick a functionality, explore again, then pick another one, explore again. I keep doing it until I have the full class
diagram.

For example

```
  RedditRSS  -->    RSSDownloader    -->    Net::HTTP/Httparty

                    - call (string)  |
                                     | -->  Dotenv


                    RSSParser       -->    Nokogiri/LibXML

                    - parse(string -> ResponseObject)

```

Basically when working with an OOP application, I don't try to spend 15 minutes to design this class diagram.
I did design, but in a simple way.
For this Kata, the problem is quite small, the class Diagram is quite small, I can know all the classes I have to implement.
For a bigger problem, the level of class will be higher, and you can't list down all of them.

Producing a big diagram isn't a good idea, because you may think about it when you are working with another class.
You may think it's okay if you draw it down to a paper, so you don't need to keep in mind. But many things can happen when
we are coding, some designs don't work, requirements are changed, so the diagram will show up automatically inside you, and
you must stop, go and update the diagram. I found it quite frustrated when changing diagram many times, sometimes I need to
change some of the classes as well.

That's my thought about a big design. A design is just a exercise to help you understand the requirements better.
Manytimes my final code does not relate to the original design.

So may be no design is better, no need to update, no need to maintain. Please let me your thoughts in the comments.

Okay, back to the kata. Here is my first test.

```ruby
require 'minitest/autorun'

class RedditTest < Minitest::Test
  class RedditRss
    def rss
    end
  end

  def test_parse_reddit
    downloader = FakeRssDownloader.new
    reddit_rss = RedditRss.new(downloader: downloader)
    reddit_rss.rss

    assert_equal(true, downloader.called?)
  end
end
```

So the test will make sure when you call reddit rss, a `downloader` will be called to get the latest rss.

Ofcourse the test is failed. We don't have the `FakeRssDownloader` class, and the `called?` method.
I will go ahead a fix

```ruby
class RedditTest < Minitest::Test
  class RedditRss
    def initialize(options = {})
      @downloader = options[:downloader]
    end

    def rss
      @downloader.call(redis_url)
    end

    def redis_url
      "http://www.reddit.com/.rss"
    end
  end

  class FakeRssDownloader
    def call(_) @called = true ""
    end

    def called?
      @called
    end
  end

  def test_parse_reddit
    downloader = FakeRssDownloader.new
    reddit_rss = RedditRss.new(downloader: downloader)
    reddit_rss.rss

    assert_equal(true, downloader.called?)
  end
end
```

The FakeRssDownloader just do nothing except set the `@called` to true, so we can check if it was called.
Also to inject the downloader into RedditRss object, I allow the constructor receive a hash options, so we can pass
the real downloader later.


Cool. So the next step is parsing the RSS and return some kind of Object. I don't know which class I should return yet.
But Ruby just doesn't care, it has duck typing.

To drive the code, I simply add one more test, which is

```
parser = FakeRedditRssParser.new
downloader = FakeRedditRssDownloader.new
reddit_rss = RedditRss.new(downloader: downloader, parser: parser)
reddit_rss.rss
assert_equal(true, parser.called?)
```

The code will be transformed to

```ruby
class RedditTest < Minitest::Test
  class RedditRss
    def initialize(options = {})
      @downloader = options[:downloader]
      @parser = options[:parser]
    end

    def rss
      raw_rss = @downloader.call(redis_url)
      @parser.call(raw_rss)
    end

    def redis_url
      "http://www.reddit.com/.rss"
    end
  end

  class FakeRedditRssDownloader
    def call(_)
      @called = true
      ""
    end

    def called?
      @called
    end
  end

  class FakeRedditRssParser
    def call(_)
      @called = true
      Object.new
    end

    def called?
      @called
    end
  end

  def test_rss
    parser = FakeRedditRssParser.new
    downloader = FakeRedditRssDownloader.new
    reddit_rss = RedditRss.new(downloader: downloader, parser: parser)
    reddit_rss.rss

    assert_equal(true, downloader.called?)
    assert_equal(true, parser.called?)
  end

end
```
