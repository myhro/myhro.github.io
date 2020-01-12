---
date: 2020-01-12
layout: post
title: Web app updates without polling
...

A co-worker wasn't happy with the current solution to update a page in our web app, polling some API endpoints every minute. This isn't just wasteful, as most of the time the requests didn't bring new data, but also slow, as every change can take up to a minute to propagate. He asked if I knew a better way to do that and while I didn't have an answer to give right away, I do remember to have heard about solutions for this problem in the past. A [quick Stack Overflow search][so-search] showed three options:

- **Long polling**: basically what we were already doing.
- **WebSockets**: persistent connections that can be used to transfer data in both ways.
- **Server-Sent Events (SSEs)**: one-way option to send data from server to client, where the connection is closed after the request is finished.

WebSockets looked like the most interesting option. Their name also reminded me of another technique applications use to push updates to others: [Webhooks][webhooks]. That's how, for instance, a CI/CD job is triggered after changes to a repository are pushed to GitHub. The only problem is that webhooks are, by definition, a server-to-server interaction. A server cannot send an HTTP request to a client. I started to question myself: if so, how do websites like the super cool [Webhook.site][webhook-site] works?

The `Webhook.site` is a tool meant to debug webhooks. One can set up a webhook, for instance, in GitHub, and inspect the entire body/headers/method of the request in a nice interface, without having to resort to set up a server to do that. The most interesting part is that the requests sent by the webhooks are displayed on the webpage in (near) real-time: exactly the problem I was looking to solve. So I started to look around to figure out how they managed to achieve that.

Digging through the page source, I found some references to [Socket.IO][socket-io], which is indeed an engine that was designed to offer bidirectional real-time communication. Before even trying to use it, I tried to understand if it worked over WebSockets and found the following quote on [its Wikipedia page][socket-io-wiki]:

> Socket.IO is not a WebSocket library with fallback options to other realtime protocols. It is a custom realtime transport protocol implementation on top of other realtime protocols.

So, `Socket.IO` may be a nice tool, but not the best alternative for our use-case. There's no need to use a custom protocol where we might have to, for instance, replace our server implementation when we can opt for a [IETF/W3C standard like WebSockets][websocket]. So I started to think about how webhooks can be integrated with WebSockets.

The easiest way would be to store, in-memory, all the currently open WebSocket connections to an endpoint and loop over them every time a webhook request came. The problem is that this architecture doesn't scale. It's fine when there's a single API server, but it wouldn't work when there are multiple clients connected to different instances of the API server. In the latter case, only a subset of the clients - the ones residing in-memory on the same server which received the webhook - would be notified about this update.

By this point, a previous co-worker and university colleague mentioned that with Redis it would be even simple to achieve that. It didn't immediately make sense to me, as at first, I thought Redis would only replace the in-memory connections list until I found out about the [`PUBLISH` command][redis-publish]. Not only Redis would take care of almost all the logic involved in notifying the connected clients: when combining them with the [`SUBSCRIBE` command][redis-subscribe], it actually offers [an entire Pub/Sub solution][redis-pubsub]. It was awesome to learn that the tool I used for over 5 years mainly as a centralized hash table - or queue at max - was the key to simplify the whole architecture.

Now it was the time to assemble the planned solution. With [Gin][gin] + [Gorilla WebSocket][gorilla] + [Redis][redis] in the Backend, together with [React][react] in the Frontend, I've managed to create [Webhooks + WebSockets][webhooks-websockets], a very basic clone of the [Webhook.site][webhook-site], showing how real-time server updates can be achieved in a web app by combining the two standards, backed by multiple technologies. The source code is [available at GitHub][repo].


[gin]: https://github.com/gin-gonic/gin
[gorilla]: https://github.com/gorilla/websocket
[react]: https://reactjs.org/
[redis-publish]: https://redis.io/commands/publish
[redis-pubsub]: https://redis.io/topics/pubsub
[redis-subscribe]: https://redis.io/commands/subscribe
[redis]: https://redis.io/
[repo]: https://github.com/myhro/webhooks-websockets
[so-search]: https://stackoverflow.com/a/14070911
[socket-io-wiki]: https://en.wikipedia.org/wiki/Socket.IO
[socket-io]: https://socket.io/
[webhook-site]: http://webhook.site/
[webhooks-websockets]: https://webhooks.myhro.info/
[webhooks]: https://zapier.com/blog/what-are-webhooks/
[websocket]: https://en.wikipedia.org/wiki/WebSocket
