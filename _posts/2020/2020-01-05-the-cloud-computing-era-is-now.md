---
date: 2020-01-05
layout: post
title: The Cloud Computing Era is now
...

I've been impressed by how the Cloud Computing landscape changed in the [past two years][cloud-migration]. I totally agree with this [quote from Cloudflare][cf-dev-xp]:

> "And yet, with many serverless offerings today, the first thing they do is the thing that they promised you they wouldn’t — they make you think about servers."

A solution isn't serverless if it makes the user think about how much computing resources they need or where these will be located. A truly serverless offer will figure everything that is needed to run a service, in a declarative, not imperative, way. I've been amazed by how it's now easy to combine different services to achieve use cases that simply wouldn't be possible just a couple of years ago.

One thing that I've been doing in the past weeks is to automate some of the manual steps in the [tool about water distribution restrictions][temagua] I wrote. For instance, the [page where they post the interruption schedule][copasa] doesn't offer an RSS feed. This forced me to bookmark the link and visit it every week, waiting for the new schedule to be published - something that can happen any day between Wednesday and Friday. So I started to wonder: "what if I crawl that page and create an RSS feed for that? This way I can put it in [Feedly][feedly] and be actively informed about new publications".

Been playing with different HTML parsers recently, like [cheerio][cheerio] and [goquery][goquery] - both inspired by the nice, easy-to-use and battle-tested [jQuery][jquery] API, crawling the web page would be the easiest part. The problem is: how was it going to be hosted - and worse, updated frequently? I could set up a cron job in one of my machines, generate the files and publish it with any HTTP server, but that looked like too much of a hassle: one shouldn't need a server to host and regularly update a static web page in 2020.

I began to think if it would be possible to use the two services that were already in place, [Cloudflare Workers Sites][cf-sites] and [GitHub Actions][gh-actions], the CI/CD solution, to achieve this goal. I mean, of course, the hosting part was already solved, but by the time I didn't know that it's [possible to schedule events][gh-actions-schedule] in GitHub Actions, down to 5-minute intervals, in a cron-like syntax:

```yaml
on:
  schedule:
    - cron:  '*/5 * * * *'
```

I'm truly astonished by what can we do these days without thinking about servers at all - it's like [IFTTT][ifttt] on steroids. I didn't have to choose in which region those services are located, nor specify how much CPU/memory/disk/other hardware resources should be allocated to them: I only asked for well-defined tasks, like building the project and deploying it, to be executed on a scheduled basis. And in the GitHub Actions case, there wasn't even a need to pay for it.

Thinking about how straightforward the whole process have been, I'm inclined to not host anything on a server owned by me ever again.


[cf-dev-xp]: https://blog.cloudflare.com/just-write-code-improving-developer-experience-for-cloudflare-workers/
[cf-sites]: https://workers.cloudflare.com/sites/
[cheerio]: https://github.com/cheeriojs/cheerio
[cloud-migration]: /2018/03/how-i-finally-migrated-my-whole-website-to-the-cloud
[copasa]: http://www.copasa.com.br/wps/portal/internet/imprensa/noticias/plano-de-racionamento/filter?area=/site-copasa-conteudos/internet/perfil/imprensa/noticias/plano-de-racionamento/em-racionamento/co-montes-claros
[feedly]: https://feedly.com/
[gh-actions-schedule]: https://help.github.com/en/actions/automating-your-workflow-with-github-actions/events-that-trigger-workflows#scheduled-events-schedule
[gh-actions]: https://github.com/features/actions
[goquery]: https://github.com/PuerkitoBio/goquery
[ifttt]: https://ifttt.com/
[jquery]: https://jquery.com/
[temagua]: https://github.com/myhro/temagua
