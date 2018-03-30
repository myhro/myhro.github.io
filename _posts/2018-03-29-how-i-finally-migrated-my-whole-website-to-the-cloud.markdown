---
date: 2018-03-29
layout: post
title: How I finally migrated my whole website to the cloud
...

This is going to be a tale of blood, sweat and tears, describing what I experienced over multiple years while trying to get rid of maintaining my own servers to host [my website][myhroinfo]. This battle lasted for, literally, over a decade, until I was finally able to migrate every piece of code that comprises it (not just this blog). Since this week, it runs on a cloud infrastructure over multiple providers where I have little to no worries about its maintenance, offers more reliability than I actually need and it's also pretty cheap.

The `myhro.info` domain was registered in April 2007. Initially I had no real intentions of hosting anything on top of it, as it was more like "oh, now I have my first domain!". In the first years, the web hosting was provided by [000webhost][000webhost]. It was free, but I also had no guarantee about its availability and faced some downtime every once in a while. This continued until, after finding some interesting offers on [Low End Box][leb], I migrated it to its own VPS server by the end of 2010. I remember the year because it was my first one at the Information Systems course, around the same time I got my first part-time System Administrator job. The experience I got maintaining my own Linux + Apache + PHP + MySQL (LAMP) server was crucial in the beginning of my professional career and some learnings from the time are still useful to me these days.

In April 2011 [this blog was started][first-post] on a [self-hosted WordPress installation][wp], in the same previously mentioned server. At first I had almost no service to really care about its availability and probably the only exception was the [Myhro.info URL Shortener][myhro-net-archive] (hosted under the `myhro.net` domain). The problem is that, after starting a blog, I had to worry about it being online at all times - otherwise people would not be able to read what I spent hours writing.

Maintaining your own WordPress instance is not an easy job, even for small blogs. I spent endless hours fighting comment spam and keeping the installation secure and up-to-date. It was such a hassle that in less than two years, it was [migrated to OctoPress][octopress-migration], a static site generator in blog format, in the beginning of 2013. Publishing posts was now a matter of copying HTML files over `rsync`, but I still had to maintain a HTTP server for it. That's why this blog was [moved to GitHub Pages in 2014 and to Jekyll in 2015][gh-pages-jekyll] and is still hosted there currently. Now I was free from maintaining a web server for it and this became [a GitHub problem][gh-pages-engineering]. At the same time the blog was migrated to Jekyll, its [HTTPS support was re-enabled using Cloudflare][cf-gh-pages] (something that was lost in the GitHub Pages migration).

Migrate `blog.myhro.info` to [GitHub Pages][gh-pages] + [Cloudflare][cf] was marvelous and I haven't worried about its maintenance ever since - not to mention that it also didn't cost me a cent to do it. Now I had to take care of other parts of my website that required server-side scripts, like [myhro.info/ip][ip]: a page that shows visitor's IP address and user agent in a simple plain text format. It's really handy to use it in the command line with `curl` and, in my experience, faster than using [ifconfig.me][ifconfig]. The main issue with this service is that it was written in PHP.

I don't remember exactly when was my first try of migrating the IP page to a cloud service, but it was probably between 2015 and 2016, when I tried [AWS Lambda][lambda], rewriting it in a supported language. This didn't worked, as to make a Lambda function available via HTTP, one have to use the [Amazon API Gateway][api-gateway] and it didn't offered the possibility of using a simple endpoint like `myhro.info/ip`. I think this can be achieved with [Amazon CloudFront][cloudfront], routing a specific path to a different origin, but it seemed too much work (and involved the usage of a bunch of different services) to achieve something that is really simple in nature. Trying to do the same using [Google Cloud Functions][gcf] yielded a [similar][gcf-endpoints] [experience][gcf-custom-domain].

After these frustrating experiences, I stopped looking for alternatives. Maybe the technology to host a few dynamic pages (in this case, only one) for a website which has most static content wasn't there yet. Then, after two hopeless years, I read the [announcement of Cloudflare Workers][cf-workers] which seemed exactly what I wanted: run code on a cloud service to answer specific requests. Finally, after reaching [open beta][cf-open-beta] and [general availability][cf-ga], in 2018 I could truly and easily deploy small "[serverless][serverless]" applications tightly integrated to an already existing website. For that I just had to learn a little bit of JavaScript.

It took me years of waiting and a few hours in a weekend to [write JavaScript replacements][myhroinfo-js] for the PHP and Python (in the end I also migrated [heroku.myhro.info][heroku-myhroinfo], a service that returns random [Heroku][heroku]-style names) implementations, but I had finally reached the Holy Grail. Now it was a matter of moving the [static parts of the website to Amazon S3][s3-static], which is quite straightforward. S3 doesn't offer HTTPS connections for static websites hosted in there, but as I already used Cloudflare, this was a no-brainer.

So, Cloudflare Workers aren't free (the minimum fee is $5/month), neither are they perfect. There are some serious limitations, like the ["one worker per domain" restriction][cf-workers-number] on non-Enterprise accounts, that can be a blocker for larger projects. But in this case, where I wanted to have a couple dynamic pages for a mostly static website, they fit perfectly. I'm also happy to pay an amount I consider reasonable for a service I've been using for free for years. Looking at the company recent innovations, they may become even better in the future.

[000webhost]: https://www.000webhost.com/
[api-gateway]: https://aws.amazon.com/api-gateway/
[cf-ga]: https://blog.cloudflare.com/cloudflare-workers-unleashed/
[cf-gh-pages]: https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/
[cf-open-beta]: https://blog.cloudflare.com/cloudflare-workers-is-now-on-open-beta/
[cf-workers-number]: https://community.cloudflare.com/t/how-many-workers-scripts-can-i-have-per-domain/13600
[cf-workers]: https://blog.cloudflare.com/introducing-cloudflare-workers/
[cf]: https://www.cloudflare.com/
[cloudfront]: https://aws.amazon.com/cloudfront/
[first-post]: /2011/04/hello-world
[gcf-custom-domain]: https://abe.ghost.io/using-a-custom-domain-with-google-cloud-functions-for-firebase/
[gcf-endpoints]: https://stackoverflow.com/a/44770553
[gcf]: https://cloud.google.com/functions/
[gh-pages-engineering]: https://githubengineering.com/rearchitecting-github-pages/
[gh-pages-jekyll]: /2015/08/myhro-blog-agora-via-jekyll
[gh-pages]: https://pages.github.com/
[heroku-myhroinfo]: https://heroku.myhro.info/
[heroku]: https://www.heroku.com/
[ifconfig]: http://ifconfig.me/
[ip]: https://myhro.info/ip
[lambda]: https://aws.amazon.com/lambda/
[leb]: https://lowendbox.com/
[myhro-net-archive]: https://web.archive.org/web/20130624084802/http://myhro.net/
[myhroinfo-js]: https://github.com/myhro/myhroinfo/blob/fc0050e/cf-workers.js
[myhroinfo]: https://myhro.info/
[octopress-migration]: /2013/01/myhro-blog-agora-via-octopress
[s3-static]: https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html
[serverless]: https://martinfowler.com/articles/serverless.html
[wp]: https://wordpress.org/
