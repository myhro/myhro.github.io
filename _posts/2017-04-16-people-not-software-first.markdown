---
date: 2017-04-16
layout: post
title: "People, not software, first"
...

During my first year at the university, I remember asking my Algorithms and Data Structures professor about how to properly handle user input in C. "If we are going to store this input, how we can be sure that the user only types an integer and not a string?". I don't actually recall his exact answer, but it was the first time that someone mentioned to me that handling user input is a recurring problem in Computer Science. And this was way before I learned about related problems like [database input sanitization][bobby-tables].

Unfortunately, I learned the wrong lesson that day. To me, the problem was that [we can't trust the user][every-user-lies], so we have to babysit every step of its human-computer interaction. If we don't, they will crash our beloved hand-crafted software, which will make us - software developers - sad. And I couldn't be more wrong. When a piece of software crashes, no one can ever gets sadder than the users who faced an error screen themselves. After all, they needed to use our software and weren't able to.

I took a few years to figure this out. Even a little after I've graduated at the university, I thought that every software that goes to production should be perfect. Every exception that can be raised in its code should be treated and it cannot ever crash in an unpredictable way. But in the real world, things do not work like this. And guess what? Users don't care if your software isn't perfect, as long as it suits their needs. All you have to care is about offering a friendly UI and giving proper feedback when things doesn't work as expected.

A couple weeks ago I found ([on this post by Julia Evans][jvns-wizard] - her blog is awesome, you should check it out) [a series of tweets by Kelsey Hightower][kelsey-tweets]. He talks about how his working life got a lot more meaningful when he started to put people first. The part which I like most is when he mentions that computers are just machines waiting to break and that software is worse, because it's always broken! Accepting that software isn't just not perfect, but also broken by design, may be the best way to deal with the issues we face everyday in this industry.

See, I'm not saying that we should be narcissist/nihilist professionals that don't care about the quality of the work we publish to whoever our users are. I think that maybe, if we treat problems with its proper importance (e.g. pretty serious when it breaks user experience, and not so much if its a known problem that is not user-visible and we can ignore), we can feel a little more proud about the systems we maintain. Otherwise we'll be doing a [Sisyphus][sisyphus] job, which can only led to an eternity of useless efforts and unending frustration.


[bobby-tables]: https://xkcd.com/327/
[every-user-lies]: https://blog.codinghorror.com/every-user-lies/
[jvns-wizard]: https://jvns.ca/blog/so-you-want-to-be-a-wizard/
[kelsey-tweets]: https://twitter.com/kelseyhightower/status/841446059641466880
[sisyphus]: https://en.wikipedia.org/wiki/Sisyphus
