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

One of my TDD addictor, he asked me why don't I do a real problem in kata, for example interact with an api or connect to database.
I have considered about it many times, there are my 2 concerns:

- The real problem one is usually aren't like any of katas. It's always bigger. With bigger problem, we need more code.
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

Basically when working with an OOP application, I will draft a simple design on paper before actually writing any line of code.

Here is my first test

```
def test_rss
  parser = RedditRssParser.new
  downloader = FakeRedditRssDownloader.new

  reddit_rss = RedditRss.new(downloader: downloader, parser: parser)
  feed = reddit_rss.rss
  assert_equal(1, feed.entries_size)
end
```

I initalize a `parser` object, which have 2 dependencies: downloader and parser base on the class diagram we drew.
We expect the output is an object that respond to the method `#entries_size`. Let me add the FakeRedditRssDownloader code

```ruby
class FakeRedditRssDownloader
  def download(_)
    <<-XML
      <?xml version="1.0" encoding="UTF-8"?>
      <feed xmlns="http://www.w3.org/2005/Atom">
        <category term=" reddit.com" label="/r/ reddit.com"/>
        <updated>2016-12-10T10:03:39+00:00</updated>
        <id>/.rss</id>
        <link rel="self" href="https://www.reddit.com/.rss" type="application/atom+xml" />
        <link rel="alternate" href="https://www.reddit.com/" type="text/html" />
        <title>reddit: the front page of the internet</title>
        <entry>
          <author>
            <name>/u/iBleeedorange</name>
            <uri>https://www.reddit.com/user/iBleeedorange</uri>
          </author>
          <category term="movies" label="/r/movies"/>
          <content type="html">&lt;table&gt; &lt;tr&gt;&lt;td&gt; &lt;a href=&quot;https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/&quot;&gt; &lt;img src=&quot;https://a.thumbs.redditmedia.com/gh5mwonP0nFkUfkl4lFmuLGuEYguyAxRnrWMHx4Q1U4.jpg&quot; alt=&quot;Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask&quot; title=&quot;Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask&quot; /&gt; &lt;/a&gt; &lt;/td&gt;&lt;td&gt; &amp;#32; submitted by &amp;#32; &lt;a href=&quot;https://www.reddit.com/user/iBleeedorange&quot;&gt; /u/iBleeedorange &lt;/a&gt; &amp;#32; to &amp;#32; &lt;a href=&quot;https://www.reddit.com/r/movies/&quot;&gt; /r/movies &lt;/a&gt; &lt;br/&gt; &lt;span&gt;&lt;a href=&quot;http://i.imgur.com/DCP06ud.jpg&quot;&gt;[link]&lt;/a&gt;&lt;/span&gt; &amp;#32; &lt;span&gt;&lt;a href=&quot;https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/&quot;&gt;[comments]&lt;/a&gt;&lt;/span&gt; &lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;</content>
          <id>t3_5hij2r</id>
          <link href="https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/" />
          <updated>2016-12-10T04:56:44+00:00</updated>
          <title>Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask</title>
        </entry>
      </feed>
    XML
  end
end

class RedditRssParser
  class RedditRssDocument
    def initialize(feed_xml)
    end

    def entries_size
      1
    end
  end

  def parse(feed_xml)
    RedditRssDocument.new(feed_xml)
  end
end
```

The setup is a little bit complicated. I will explain the reason I do that later. Right now, we need to pass the code

```ruby
class RedditRss
  def initialize(options = {})
    @downloader = options[:downloader]
    @parser = options[:parser]
  end

  def rss
    url = "http://www.reddit.com/.rss"
    feed_xml = @downloader.download(url)
    @parser.parse(feed_xml)
  end
end
```

So simple code, right? Next, we want the parse will do something actually, not just create an object.
I add a test for it.

```ruby
def test_feed_title
  parser = RedditRssParser.new
  downloader = FakeRedditRssDownloader.new

  reddit_rss = RedditRss.new(downloader: downloader, parser: parser)
  feed = reddit_rss.rss
  title = "Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask"
  assert_equal(title, feed[0].title)
end
```

**Failed** Of course.
Well, to fix, we need to make the RedditRssParser class dirty a bit.
I will add LibXML gem and parse the feed_xml. Don't be shock when look at the code

```ruby
class RedditRssParser
  class RedditRssDocument
    class Entry
      def initialize(entry_xml)
        @entry_xml = entry_xml
      end

      def title
        node = @entry_xml.find_first('x:title')
        node.content if node
      end
    end

    def initialize(feed_xml)
      @feed_xml = feed_xml
      @feed_xml.root.namespaces.default_prefix = "x"
    end

    def entries
      @entries ||= @feed_xml.find("/x:feed/x:entry")
    end

    def entries_size
      entries.size
    end

    def [](index)
      Entry.new entries[index]
    end
  end

  def parse(feed_xml)
    xml_document = XML::Parser.string(feed_xml).parse
    RedditRssDocument.new(xml_document)
  end
end
```

How to say? If you aren't familar with LibXML, you may get lost. Also what we did in previous katas, the changing code should be small, but in this kata,
it's huge. Is it still TDD?

With experiences, I knew that I missed a case when `entries` are empty or nil. The `entry.title` will complain that no method title for nil.
But the test that produces a big change in code, is it a good test and did we do TDD? That's where Design Pattern term come.

Like the definition of "Algorithm": `the word is used by programmers when they don't want to explain about what they did`. Design pattern may be a good excuse
to **not** writing test. Here we use a pattern call `Iteration Pattern`, and other thing is just LibXML usages.

I did some refactors on test.

```ruby
def setup
  @parser = RedditRssParser.new
  @downloader = FakeRedditRssDownloader.new

  @reddit_rss = RedditRss.new(downloader: @downloader, parser: @parser)
  @feed = @reddit_rss.rss
end

def test_rss
  assert_equal(1, @feed.entries_size)
end

def test_feed_title
  title = "Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask"
  assert_equal(title, @feed[0].title)
end
```

Actually we can add more method like author, category... But right now we don't use them, so we don't need to implement them yet.

Do we miss any more test for the parser? Well, if the feed are empty, or we get an error, how do we handle that?
To return an empty feed, we simply add a new downloader class that returns empty feed

```ruby
class FakeRedditRssNoFeedDownloader
  def download(_)
    <<-XML
<?xml version="1.0" encoding="UTF-8"?>
      <feed xmlns="http://www.w3.org/2005/Atom">
      </feed>
    XML
  end
end
```

and the test is

```ruby
def test_empty_feed
  downloader = FakeRedditRssNoFeedDownloader.new
  reddit_rss = RedditRss.new(downloader: downloader, parser: @parser)
  feed = reddit_rss.rss
  assert_equal(0, feed.entries_size)
  assert_equal(nil, feed[0].title)
end
```

The first is passed, but the next test with title is failed

```
  NoMethodError: undefined method `find_first' for nil:NilClass`
```

Here is my tiny fix

```ruby
def title
  return nil unless @entry_xml
  node = @entry_xml.find_first('x:title')
  node.content if node
end
```

I can use NullObject pattern or some fancy technique, but this is the perfect solution.

Next, we will test what's happened with empty response. We do the same thing: create a new downloader, return empty response

```ruby
class FakeRedditRssEmptyResponseDownloader
  def download(_)
    " "
  end
end

def test_empty_respone
  downloader = FakeRedditRssEmptyResponseDownloader.new
  reddit_rss = RedditRss.new(downloader: downloader, parser: @parser)
  feed = reddit_rss.rss
  assert_equal(0, feed.entries_size)
  assert_equal(nil, feed[0].title)
end
```

What error message do you guess? Here is it

```
LibXML::XML::Error: Fatal error: Start tag expected, '<' not found at :1.
```

The code is failed in the the LibXML level. What we can do is rescue the LibXML::XML::Error

```ruby
class RedditRssParser
  def parse(feed_xml)
    XML::Error.set_handler do |error|
      nil
    end

    begin
      xml_document = XML::Parser.string(feed_xml).parse
    rescue XML::Error => e
      default_xml_string = <<-XML
<?xml version="1.0" encoding="UTF-8"?><feed xmlns="http://www.w3.org/2005/Atom"></feed>
      XML
      xml_document = XML::Parser.string(default_xml_string).parse
    end

    RedditRssDocument.new(xml_document)
  end
end
```

I think it's every thing we need for this class, we can move on to the downloader logic.
Well we have 3 Fake downloader class already. Just need to create a real one.

```ruby
class RedditRssDownloader
  def download(url)
    Net::HTTP.get(URI(url))
  end
end

class RedditRssDownloaderTest < Minitest::Test
  def test_download_rss
    downloader = RedditRssDownloader.new
    url = "https://www.reddit.com/.rss"
    assert_equal(String, downloader.download(url).class)
  end
end
```

Hell yeah, the test takes very long to finish

```
Run options: --seed 48989

# Running:

.....

Finished in 12.089110s, 0.4136 runs/s, 0.4963 assertions/s.
```

Because the test depends on the network connection and Reddit Server. Sometimes because we run the test many times, Reddit
can block the IP, so the test is failed. So this test isn't stable and reliable.

If you look at the code, you will see the behavior of #download method is calling `Net::HTTP.Get(url)`, so it just call another
class to do the job, it doesn't do the job itself. And if the method doesn't dothing but calling another method, we don't need
to write test for it (Practical Object Oriented Design, Sandi Metz). Although we do TDD, which means we need to have a test first
then write code later. But sometimes, for example this case, the method doesn't do anything, so writing a good test is harder
than no test.

In case you really want to write a test, you can inject Net::HTTP as a dependency.
For example

```ruby
class RedditRssDownloader
  def download(url, http_klass = Net::HTTP)
    http_klass.get(URI(url))
  end
end

class RedditRssDownloaderTest < Minitest::Test
  class FakeHTTPClient
    def self.get(_)
      "string"
    end
  end

  def test_download_rss
    downloader = RedditRssDownloader.new
    url = "https://www.reddit.com/.rss"
    assert_equal("string", downloader.download(url, FakeHTTPClient))
  end
end
```

Like that. Depends on your rule also. For me, I am quite lazy, so I stop. I post the full code here for your reference

```ruby
require 'minitest/autorun'
require 'xml'
require 'net/http'

class RedditTest < Minitest::Test
  class RedditRssParser
    class RedditRssDocument
      class Entry
        def initialize(entry_xml)
          @entry_xml = entry_xml
        end

        def title
          return nil unless @entry_xml
          node = @entry_xml.find_first('x:title')
          node.content if node
        end
      end

      def initialize(feed_xml)
        @feed_xml = feed_xml
        @feed_xml.root.namespaces.default_prefix = "x"
      end

      def entries
        @entries ||= @feed_xml.find("/x:feed/x:entry")
      end

      def entries_size
        entries.size
      end

      def [](index)
        Entry.new entries[index]
      end
    end

    def parse(feed_xml)
      XML::Error.set_handler do |error|
        nil
      end

      begin
        xml_document = XML::Parser.string(feed_xml).parse
      rescue XML::Error => e
        default_xml_string = <<-XML
<?xml version="1.0" encoding="UTF-8"?><feed xmlns="http://www.w3.org/2005/Atom"></feed>
        XML
        xml_document = XML::Parser.string(default_xml_string).parse
      end

      RedditRssDocument.new(xml_document)
    end
  end

  class RedditRss
    def initialize(options = {})
      @downloader = options[:downloader]
      @parser = options[:parser]
    end

    def rss
      url = "http://www.reddit.com/.rss"
      feed_xml = @downloader.download(url)
      @parser.parse(feed_xml)
    end
  end

  class RedditRssDownloader
    def download(url)
      Net::HTTP.get(URI(url))
    end
  end

  class RedditRssDownloaderTest < Minitest::Test
    def test_download_rss
      downloader = RedditRssDownloader.new
      url = "https://www.reddit.com/.rss"
      assert_equal(String, downloader.download(url).class)
    end
  end

  class FakeRedditRssDownloader
    def download(_)
      <<-XML
<?xml version="1.0" encoding="UTF-8"?>
        <feed xmlns="http://www.w3.org/2005/Atom">
          <entry>
            <author>
              <name>/u/iBleeedorange</name>
              <uri>https://www.reddit.com/user/iBleeedorange</uri>
            </author>
            <category term="movies" label="/r/movies"/>
            <content type="html">&lt;table&gt; &lt;tr&gt;&lt;td&gt; &lt;a href=&quot;https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/&quot;&gt; &lt;img src=&quot;https://a.thumbs.redditmedia.com/gh5mwonP0nFkUfkl4lFmuLGuEYguyAxRnrWMHx4Q1U4.jpg&quot; alt=&quot;Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask&quot; title=&quot;Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask&quot; /&gt; &lt;/a&gt; &lt;/td&gt;&lt;td&gt; &amp;#32; submitted by &amp;#32; &lt;a href=&quot;https://www.reddit.com/user/iBleeedorange&quot;&gt; /u/iBleeedorange &lt;/a&gt; &amp;#32; to &amp;#32; &lt;a href=&quot;https://www.reddit.com/r/movies/&quot;&gt; /r/movies &lt;/a&gt; &lt;br/&gt; &lt;span&gt;&lt;a href=&quot;http://i.imgur.com/DCP06ud.jpg&quot;&gt;[link]&lt;/a&gt;&lt;/span&gt; &amp;#32; &lt;span&gt;&lt;a href=&quot;https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/&quot;&gt;[comments]&lt;/a&gt;&lt;/span&gt; &lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;</content>
            <id>t3_5hij2r</id>
            <link href="https://www.reddit.com/r/movies/comments/5hij2r/arnold_schwarzenegger_and_jackie_chan_are_making/" />
            <updated>2016-12-10T04:56:44+00:00</updated>
            <title>Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask</title>
          </entry>
        </feed>
      XML
    end
  end

  class FakeRedditRssNoFeedDownloader
    def download(_)
      <<-XML
<?xml version="1.0" encoding="UTF-8"?>
        <feed xmlns="http://www.w3.org/2005/Atom">
        </feed>
      XML
    end
  end

  class FakeRedditRssEmptyResponseDownloader
    def download(_)
      " "
    end
  end

  def setup
    @parser = RedditRssParser.new
    @downloader = FakeRedditRssDownloader.new

    @reddit_rss = RedditRss.new(downloader: @downloader, parser: @parser)
    @feed = @reddit_rss.rss
  end

  def test_rss
    assert_equal(1, @feed.entries_size)
  end

  def test_feed_title
    title = "Arnold Schwarzenegger and Jackie Chan are making a movie together: Journey to China: The Mystery of Iron Mask"
    assert_equal(title, @feed[0].title)
  end

  def test_empty_feed
    downloader = FakeRedditRssNoFeedDownloader.new
    reddit_rss = RedditRss.new(downloader: downloader, parser: @parser)
    feed = reddit_rss.rss
    assert_equal(nil, feed[0].title)
  end

  def test_empty_respone
    downloader = FakeRedditRssEmptyResponseDownloader.new
    reddit_rss = RedditRss.new(downloader: downloader, parser: @parser)
    feed = reddit_rss.rss
    assert_equal(0, feed.entries_size)
    assert_equal(nil, feed[0].title)
  end
end
```

Okay, that's it for today. I hope you enjoy the very first OOP TDD Kata. I looking forward to seeing you in the next TDD Kata. It
is a next part of this one, and we will talk more about the Iteration Pattern that we used to store the feed data. Any feedback,
please let me know under the comments. Bye.
