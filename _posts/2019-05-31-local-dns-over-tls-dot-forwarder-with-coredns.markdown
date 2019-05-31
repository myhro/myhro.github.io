---
date: 2019-05-31
layout: post
title: Local DNS-over-TLS (DoT) forwarder with CoreDNS
...

The first time I heard about [DNS-over-TLS (DoT)][dot] was about a year ago, when [Cloudflare launched their `1.1.1.1` public resolver][1-1-1-1-launch]. It immediately appeared to be a more natural successor to regular plain-text DNS than [DNS-over-HTTPS (DoH)][doh]. The problem is that, back then, it was not so easy to use. Pages like the [client list in the DNS Privacy Project][dns-privacy-clients] mentions lengthy configuration options for forwarders like [Bind][bind]/[Knot DNS][knot-dns]/[Unbound][unbound], which aren't exactly known for being user-friendly. What I wanted was something like [Simple DNSCrypt][simple-dnscrypt] on Windows, but for Linux/macOS and supporting DoT instead of DNSCrypt, [which is a different protocol][dnscrypt-protocol].

After a while I realized that [CoreDNS][coredns] supported DoT in [its forward plugin][coredns-forward]. By then, the only use-case I knew for CoreDNS was that it is commonly used for [service discovery inside Kubernetes clusters][coredns-kubernetes]. Starting from this point, I began investigating how hard would be to use it on my laptop, running macOS, and a couple remote servers that I use as workstations, running Linux. A DoT proxy/forwarder that supported caching seemed exactly what I was looking for.

The architecture goes like:

```
Applications
    |
127.0.0.1:53 (forwarder, plain text)
    |
1.1.1.1:853 (upstream, TLS-encrypted)
```

And the needed configuration is, literally, four lines:

```
. {
    cache
    forward . tls://1.1.1.1 tls://1.0.0.1
}
```

In this case, CoreDNS will forward all (`.`) DNS queries to `1.1.1.1` and `1.0.0.1` over TLS, load-balancing between them. It will also cache the responses, respecting their time-to-live (TTL), answering repeated queries in sub-millisecond latency.

Understanding how simple the needed CoreDNS configuration is, the next step is to install it and configure its service to start on boot. The problem is that, being a relatively new project, it isn't available on most package managers:

```
$ brew search coredns
No formula or cask found for "coredns".
$ brew info coredns
Error: No available formula with the name "coredns"
```
```
$ apt search coredns
Sorting... Done
Full Text Search... Done
$ apt show coredns
N: Unable to locate package coredns
E: No packages found
```

But there's a bright side: CoreDNS is a [Go][golang] project. Having all its dependencies statically-linked, installing it is a matter of downloading the corresponding release for the target Operating System/Architecture in [the project GitHub release page][coredns-releases] - in exchange for a slightly large binary (`> 40MB`). Putting it under `/usr/local/bin/coredns`, it can be easily configured to be treated as a service on both macOS and Linux (systemd):

macOS: `/Library/LaunchDaemons/coredns.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>coredns</string>
    <key>ProgramArguments</key>
    <array>
      <string>/usr/local/bin/coredns</string>
      <string>-conf</string>
      <string>/usr/local/etc/coredns/coredns.conf</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
  </dict>
</plist>
```

Linux (systemd): `/lib/systemd/system/coredns.service`
```
[Unit]
Description=CoreDNS
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/coredns -conf /etc/coredns.conf
Type=simple

[Install]
WantedBy=multi-user.target
```

After starting it with `sudo launchctl load -w /Library/LaunchDaemons/coredns.plist` on macOS, or `sudo service coredns start` on Linux, we can confirm it's working by analysing the DNS packets that goes over the network. It's a matter of creating a `tcpdump` session on one terminal:

```
$ sudo tcpdump -n 'host 1.1.1.1 or host 1.0.0.1'
tcpdump: data link type PKTAP
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on pktap, link-type PKTAP (Apple DLT_PKTAP), capture size 262144 bytes
```

And running `dig` on another:

```
$ dig blog.myhro.info @1.1.1.1
(...)
$ dig blog.myhro.info @127.0.0.1
(...)
```

The first request, done straight to the upstream server, can be literally seen on the wire together with its response:

```
15:29:43.200468 IP 192.168.0.2.56184 > 1.1.1.1.53: 23554+ [1au] A? blog.myhro.info. (56)
15:29:43.215999 IP 1.1.1.1.53 > 192.168.0.2.56184: 23554$ 2/0/1 A 104.27.179.51, A 104.27.178.51 (76)
```

While the second one, which goes through CoreDNS, can't be sniffed:

```
15:33:41.238099 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [S], seq 2286465987, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 464696127 ecr 0,sackOK,eol], length 0
15:33:41.251513 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [S.], seq 1040019771, ack 2286465988, win 29200, options [mss 1460,nop,nop,sackOK,nop,wscale 10], length 0
15:33:41.251607 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [.], ack 1, win 4096, length 0
15:33:41.251863 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [P.], seq 1:192, ack 1, win 4096, length 191
15:33:41.267167 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [.], ack 192, win 30, length 0
15:33:41.267608 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [.], seq 1:1461, ack 192, win 30, length 1460
15:33:41.268328 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [P.], seq 1461:2667, ack 192, win 30, length 1206
15:33:41.268392 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [.], ack 2667, win 4077, length 0
15:33:41.284748 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [P.], seq 192:285, ack 2667, win 4096, length 93
15:33:41.297023 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [P.], seq 2667:2718, ack 285, win 30, length 51
15:33:41.297104 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [.], ack 2718, win 4095, length 0
15:33:41.297403 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [P.], seq 285:316, ack 2718, win 4096, length 31
15:33:41.297495 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [P.], seq 316:401, ack 2718, win 4096, length 85
15:33:41.308614 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [.], ack 401, win 30, length 0
15:33:41.311106 IP 1.1.1.1.853 > 192.168.0.2.55911: Flags [P.], seq 2718:2877, ack 401, win 30, length 159
15:33:41.311178 IP 192.168.0.2.55911 > 1.1.1.1.853: Flags [.], ack 2877, win 4093, length 0
```

Now it's a matter of configuring the system to use `127.0.0.1` as the DNS server.

**P.S.:** it's important to notice that using DNS-over-TLS together with regular HTTPS connections is still not enough to guarantee total browsing privacy. The target hostname is still sent in plain text during the TLS handshake. That will change when the [encrypted SNI extension to the TLS 1.3 protocol][encrypted-sni] becomes widely available. Given this particularity, Cloudflare offers a page to [check how secure/private your browsing experience is][cloudflare-sec-check].


[1-1-1-1-launch]: https://blog.cloudflare.com/dns-resolver-1-1-1-1/
[bind]: https://www.isc.org/downloads/bind/
[cloudflare-sec-check]: https://www.cloudflare.com/ssl/encrypted-sni/
[coredns-forward]: https://coredns.io/plugins/forward/
[coredns-kubernetes]: https://kubernetes.io/docs/tasks/administer-cluster/coredns/
[coredns-releases]: https://github.com/coredns/coredns/releases
[coredns]: https://coredns.io/
[dns-privacy-clients]: https://dnsprivacy.org/wiki/display/DP/DNS+Privacy+Clients
[dnscrypt-protocol]: https://dnscrypt.info/protocol/
[doh]: https://en.wikipedia.org/wiki/DNS_over_HTTPS
[dot]: https://en.wikipedia.org/wiki/DNS_over_TLS
[encrypted-sni]: https://blog.cloudflare.com/encrypted-sni/
[golang]: https://golang.org/
[knot-dns]: https://www.knot-dns.cz/
[simple-dnscrypt]: https://simplednscrypt.org/
[unbound]: https://nlnetlabs.nl/unbound
