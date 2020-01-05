---
date: 2019-12-25
layout: post
title: Migrating "Tem Água Hoje?" to Cloudflare Workers
...

Last year I mentioned how some small dynamic parts of my main website - [Myhro.info][myhro.info] - were [migrated to Cloudflare Workers][myhro.info-migration]. This time I did a similar move, but instead of migrating just a couple parts, I moved a whole webapp, including both backend and frontend, to there. Before mentioning how much better Cloudflare Workers are one and a half years later - and they already rocked in the first time! - let's go through all the iterations "[Tem Água Hoje?][temagua]", a website created to [better inform people about water distribution restrictions][temagua-background], went through its lifetime.

# Debut

The first iteration of the website came in form of an all-JavaScript application running in the browser. Literally everything was JavaScript in one form or another: the frontend, parser and even the database itself was structured in a JSON format. This happened for two reasons: first, I wanted to practice a bit of JavaScript. The second reason - and maybe the most import one - is that in this way, there was no backend whatsoever, no server-side part which I would have to worry about. The whole webapp was served as a [static site straight from S3][s3-site], which absurdly simplified its deployment and maintenance.

This version was good enough for the start. It was a bit annoying to generate the database dump, as I had to manually go to the browser and save it as a file, but as the water restrictions were lifted less than two months after starting the project, I wasn't worried about it anymore. Then in the beginning of November this year, [it was announced][announcement] that water restrictions would be again in place for the next months. I had to decide to either just feed the new data to the webapp or rewrite a good chunk of it.

# Component decoupling

Being a full-time [Go][golang] programmer for a while made me biased towards rewriting the parser in Go and separating the actual logic about the water availability from the frontend, moving it to a real backend. With a bit of [Gin][gin] - my favourite web framework - and [SQLite][sqlite], I was able to offer a REST API, powered by a small-but-full-featured relational database. The result was nice and delightful to build. All of that while teaching my nephew, who is studying Informatics, about all these components and how they play along together at the same time - a win-win for both him and me.

I really liked the result: a vanilla JavaScript frontend, fetching information from a Go API, backed by SQLite. There was just one problem: I would still have to deploy, host, maintain and care about the backend availability - otherwise the whole webapp would become useless. It was hosted in a server that I use as a developer workstation from time to time, but that was too fragile. I contemplated deploying it to a [Kubernetes][k8s] cluster, but that would be too much. I had to find a better and easier - preferable cheaper - alternative.

# Workers to the rescue

As I mentioned, [Cloudflare Workers][workers] have evolved considerably after its launch. I've been following the [new features being offered][workers-kv-news], but wasn't playing with them - something which bothered me a bit, as I'm in love for the platform since day one. This seemed like a perfect opportunity for me to try new things out, like their key-value store, [Workers KV][workers-kv]. By being a somewhat limited storage solution, as literally only string keys and values are available, I had to come up with a new modeling for the database, as no relational features like columns or queries would be available.

To be honest, I actually liked the limitations. There's beauty in how simple the environment is, where one can use basically one language - JavaScript, if you don't consider the [WebAssembly option][workers-webassembly] - and what is almost literally a hash table as storage layer. In the end, that actually simplified the logic to check for water interruption intervals, without forcing us to get back to the first JSON-based database format - which wouldn't be supported anyway, as it wasn't based only in strings.

# Bonus points and conclusion

One interesting thing is that Cloudflare Workers can now also host static files in the form of [Cloudflare Workers Sites][workers-sites] - although not literally. What they do is to offer some logic to store file contents as strings in the KV store and fetch them from HTTP requests. A [really clever usage of two simple features][workers-sites-blog] that can be used together to form a new one. By also deploying the frontend to Cloudflare Workers, S3 wasn't needed anymore at all, simplifying the infrastructure and deployment procedures even further.

So, now we have [temagua.myhro.info][temagua] in a much more simple, faster, resilient - and also cheap - platform. I don't have to care about it being up and running all the time - there are way more competent Engineers at Cloudflare doing that for me. And, by being free from the maintenance hassle, I was able to invest time in much more interesting things, like [porting the frontend][react-migration] to [React][react], something I did for the first time in my life.

Keep rocking, Cloudflare!


[announcement]: http://www.copasa.com.br/wps/portal/internet/imprensa/noticias/plano-de-racionamento/em-racionamento/co-montes-claros/rodizio-montes-claros-14-11-2019/
[gin]: https://github.com/gin-gonic/gin
[golang]: https://golang.org/
[k8s]: https://kubernetes.io/
[myhro.info-migration]: /2018/03/how-i-finally-migrated-my-whole-website-to-the-cloud
[myhro.info]: https://myhro.info/
[react-migration]: https://github.com/myhro/temagua/commit/4864519
[react]: https://reactjs.org/
[s3-site]: https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html
[sqlite]: https://www.sqlite.org/
[temagua-background]: https://github.com/myhro/temagua#background
[temagua]: https://temagua.myhro.info/
[workers-kv-news]: https://blog.cloudflare.com/whats-new-with-workers-kv/
[workers-kv]: https://www.cloudflare.com/products/workers-kv/
[workers-sites-blog]: https://blog.cloudflare.com/extending-the-workers-platform/
[workers-sites]: https://workers.cloudflare.com/sites/
[workers-webassembly]: https://developers.cloudflare.com/workers/archive/webassembly/
[workers]: https://workers.cloudflare.com/
