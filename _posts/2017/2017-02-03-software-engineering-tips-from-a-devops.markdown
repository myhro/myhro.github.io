---
date: 2017-02-03
layout: post
title: "Software engineering tips from a DevOps"
...

There's a pattern that I've observed in nearly every software development company that I worked for. When discussing solutions with developer teams, two distinct things can happen: if it's something related to operating systems, networking or infrastructure in general, they agree with me almost never arguing against anything. Sometimes this is bad, as I could possibly be missing an important detail which they thought that's already figured out. On the other hand, when the matter is related to programming and software engineering, they ignore me virtually every time. It's almost like if I've not even said anything meaningful.

Based on this behavior, I've compiled a list of software engineering tips (or best practices) that I urge every developer to follow (and every DevOps/SysAdmin to remember them about it). None of these items were invented by me, nor they are just theoretical things. Those are battle-worn recommendations from someone who is part of this industry for quite a few years, which have seen many things that can go wrong when bad decisions are made. They are not hard to follow, but I've seen even experienced developers making the same mistakes mentioned here.

## Do not hardcode configuration parameters

This one is tempting and source of "works on my machine" symptoms. We know that developers love to have control about what they are creating and sometimes afraid of someone running their software with badly formatted input. This is even worse with configuration parameters: "you put an 'http://' in there, please just use the hostname". So this may be difficult to ask, but please, [read configuration parameters from environment variables][12f-config] not only when you should, but every time you can. A default value can not only be useful for a specific environment (e.g. development), but also works as documentation for the expected format. See this Python example:

``` python
import os

database_url = os.environ.get('DATABASE_URL', 'postgres://user:pwd@localhost/app_db')
redis_host = os.environ.get('REDIS_HOST', 'localhost')
```

You can assume some things like "this web app will only run on port 80", but there isn't a way to know this for sure when it goes to production. It can be inside a container that have its ports forwarded or not. A reverse proxy can be in front of it and the app server will have to bind to a higher port. If the application can dynamically read this kind of information from its environment, you'll be making both of our jobs way easier. I won't have to ask you for changes and you won't have to change the implementation (or worse, force me to do this).

## Do not try reinvent the wheel

We all know: software development is great. You can ask a computer to do anything you want and it will do it over and over again. Because of this, you may be attracted by the idea that not only you can do anything, but also get the best possible solution without thinking too much about it. The reality is that this is not going to happen, maybe not even if you are truly a genius. I've seen this happening multiple times, specially with parsers for structured text, "things that can be solved with a clever regex" and the like.

The advice I can do to avoid this is: be humble. Assume that your solution may not work for every case. Actually, the most important part is to realize that if you can't think about an use case, it doesn't mean that it doesn't exist and won't ever appear. A few weeks from now an edge case can come to bite you. Look for opensource frameworks and libraries that can do what you need. Learn to appreciate the work from people that have been polishing these pieces of software for years - and allowing you to use it for free. Maybe you can even do a significant contribution to make them better.

[Gerald Sussman][sussman], the legendary MIT professor who co-authored the [SICP book][sicp], was once asked about the switch from Scheme (a Lisp dialect) to Python in the Computer Science undergraduate program. [His answer was that it made sense][mit-sicp-switch], because these days programming is very different from what it was in the 80s and 90s. Today it's "more like science", where you grab some libraries and figure if they can do what you want. So, stand on the shoulder of giants and only write from scratch what you really need.

## Opt for rock-solid battle-tested solutions

This one is related to "not reinventing the wheel", but it's more about choosing mature implementations that have a greater chance to work. Sometimes you may be tempted to pick this new framework that everyone is talking about (some of them without having ever touched it), but maybe it is not really ready to be used. Of course someone will have to start using it to prove if it works or not, but you probably don't want to face the problems these early adopters will hit. At least not on a production environment.

The same can be said when you need, for instance, a network protocol. Using raw sockets can be fast and fun, but when you application grows a little bit you'll realize you need a real protocol. Then you will be implementing compression to trade data efficiently, defining a format to receive arguments, etc. In the end, something like [GRPC][grpc] was the answer of all the problems you were trying to solve, but what you got is a stripped-down version of it that wasn't tested by thousands of other developers around the globe.

## Closing thoughts

I could list a few more things about this subject, but it's better if we keep this post short. Unfortunately, I've experienced some of the mentioned problems more than once in the last couple months. I'm not an expert in managing people, but one of the causes seems to be closely related to ego reasons. Sometimes I think that this may be naivety, when the person in question can't see the broader picture of the problem they are facing. At the same time, this is funny because it also happens with multiple-year experienced individuals.

If you are a developer who identified with at least part of this list, you don't really need to listen to me. Think about yourself, as a professional in our area, and what you can do to write software that is easy and robust to be executed in different environments. Always remember that your responsibility doesn't end when you push code in commits to a repository. You are at least responsible as I am for running your code in production. In the end, it's your creation, right?

[12f-config]: https://12factor.net/config
[grpc]: http://www.grpc.io/
[mit-sicp-switch]: http://www.posteriorscience.net/?p=206
[sicp]: https://en.wikipedia.org/wiki/Structure_and_Interpretation_of_Computer_Programs
[sussman]: https://en.wikipedia.org/wiki/Gerald_Jay_Sussman
