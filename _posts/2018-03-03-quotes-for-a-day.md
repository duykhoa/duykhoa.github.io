---
layout: post
title: Quote for a day

---

I just love quotes recently. Quotes make me pondering everyday. I build a website to collect & present my favorite quotes.

Currently, all of my quotes are from Jim Rohn talks.
Some of them are collected from different sources, and some are listened from public youtube videos.

This site can be accessed via [https://quotes.kevintran.ninja](https://quotes.kevintran.ninja)

I use Firebase for hosting and database. It's a serverless service developed [by Google](https://firebase.google.com/).
This platform (I don't know whether I use correct word) supports a lot of features like hosting, real time database, cloud storage,
authentication.

Although the vision of Firebase is more for mobile application, it's totally compatible with Web development.
It provides a SDK which integrates with many Google products like AdWords, Google Analytics, Dynamic links.

It's free for small website. If you want to launch an enterprise app, check out [the pricing page](https://firebase.google.com/pricing/)
In the moment I checked, the Hobbyist (free plan) allows us to use all the features mentioned above, 1GB Database, 5GB storage,
1GB hosting, custom SSL & domain, which is far more than enough for a side project. We can create many projects as needed with zero cost.

By using Firebase for hosting, storing data, I don't have to setup a server myself. It saves me a lot of time, I can focus more in implementing the website's functions.
My website doesn't  need backend to process user request, everything is done from the frontend side.
I decide to use [Polymer JS](https://polymer-project.org). This library is also developed by Google. IMHO, it uses the same concept with
React. The concept of dividing User Interface to a set of different web components, each one handles a unique responsibility, these components can interact with each other.

Polymer until now is far less popular than React JS. However, I can see myself as a true fan with Polymer.

AFAICS, nowaday javascript libraries are very well in promoting themselves in Tech community. At the end, they are quite similar. With a big community, the library is growth faster than other. The library has to adapt to different directions, and it may not go with the direction that it was decided by the author from beginning. I choose Polymer because the tool seems easy to learn, the source code is small and I can read. There is some public components written by Polymer available in [Polymer Element Github](https://github.com/PolymerElements), and in [Web Components](https://www.webcomponents.org/). These elements are easy to use and their source code are able to understand. Those `paper-` elements are built follow the [Material design guideline](https://material.io/guidelines/), we can use them as native **Material** components, this is one of the reason I go with Polymer.

Another reason is Polymer is developed and used by Google. Many [Google products](https://github.com/Polymer/polymer/wiki/Who's-using-Polymer%3F) are built or rebuilt in Polymer. This is count to my decision to use Polymer? Well, compare to React, which is driven by the community, Polymer is built for it own usages. Google products are enterprise products, which means the platform they are building on have to be maintained well, and it has to satisfy all needs. A library from time to time will has different version, which may not fully compatible. If too many versions exists, the core team may not able to maintain all different versions, they may drop support to some of them. However, Polymer is used inside Google, the Polymer maintain team has no choice but support many versions longer, which is also a good thing for smaller companies that want to use Polymer. These companies aren't able to drastically upgrading their platform because of their limited resources.

That's what I thought before going with Polymer instead of other tools like React, Vuejs... But don't trust my word completely, I may be wrong.
Only after few more years, I can answer whether my guess is true.

It's quite a lot of talking, here is some screenshots of the quotes websites. Thanks for reading.

<img width="308" alt="screen shot 2018-03-03 at 4 34 34 pm" src="https://user-images.githubusercontent.com/2004218/36932232-448b0a8a-1f01-11e8-96f9-28e772e8e5d3.png">
<img width="300" alt="screen shot 2018-03-03 at 4 34 52 pm" src="https://user-images.githubusercontent.com/2004218/36932233-44bcd7a4-1f01-11e8-8828-2b9a7ac99ad7.png">
<img width="305" alt="screen shot 2018-03-03 at 4 36 10 pm" src="https://user-images.githubusercontent.com/2004218/36932234-44eb44cc-1f01-11e8-8be7-d131faaaf630.png">
<img width="310" alt="screen shot 2018-03-03 at 4 36 38 pm" src="https://user-images.githubusercontent.com/2004218/36932235-4518ede6-1f01-11e8-933f-196be8ec5074.png">
<img width="309" alt="screen shot 2018-03-03 at 4 36 59 pm" src="https://user-images.githubusercontent.com/2004218/36932236-4547616c-1f01-11e8-8013-655a55b1b9b5.png">
<img width="307" alt="screen shot 2018-03-03 at 4 37 18 pm" src="https://user-images.githubusercontent.com/2004218/36932237-4573f8e4-1f01-11e8-8d87-c97d97e561f4.png">

P.S. By the time I write this post, I find out that any thing that you work on with a sense of seriousness, you always find a way to make it better, even though it seems to be an extremely simple.
