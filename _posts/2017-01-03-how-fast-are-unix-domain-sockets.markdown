---
date: 2017-01-03
layout: post
title: "How fast are Unix domain sockets?"
...

> Warning: this is my first post written in English, after over five years writing only in Portuguese. After reading many technical articles written in English by non-native speakers, I've wondered: imagine how much information I would be missing if they wrote those posts in French or Russian. Following their examples, this blog can also reach a much wider audience as well.

It probably happened more than once, when you ask your team about how a reverse proxy should talk to the application backend server. "Unix sockets. They are faster.", they'll say. But how much faster this communication will be? And why a Unix domain socket is faster than an IP socket when multiple processes are talking to each other in the same machine? Before answering those questions, we should figure what Unix sockets really are.

Unix sockets are a form of inter-process communication (IPC) that allows data exchange between processes in the same machine. They are special files, in the sense that they exist in a file system like a regular file (hence, have an inode and metadata like ownership and permissions associated to it), but will be read and written using `recv()` and `send()` syscalls instead of `read()` and `write()`. When binding and connecting to a Unix socket, we'll be using file paths instead of IP addresses and ports.

In order to determine how fast a Unix socket is compared to an IP socket, two proofs of concept (POCs) will be used. They were written in Python, due to being small and easy to understand. Their implementation details will be clarified when needed.

## IP POC

`ip_server.py`

``` python
#!/usr/bin/env python

import socket

server_addr = '127.0.0.1'
server_port = 5000

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind((server_addr, server_port))
sock.listen(0)

print 'Server ready.'

while True:
    conn, _ = sock.accept()
    conn.send('Hello there!')
    conn.close()
```

`ip_client.py`

``` python
#!/usr/bin/env python

import socket
import time

server_addr = '127.0.0.1'
server_port = 5000

duration = 1
end = time.time() + duration
msgs = 0

print 'Receiving messages...'

while time.time() < end:
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect((server_addr, server_port))
    data = sock.recv(32)
    msgs += 1
    sock.close()

print 'Received {} messages in {} second(s).'.format(msgs, duration)
```

## Unix domain socket POC

`uds_server.py`

``` python
#!/usr/bin/env python

import os
import socket

server_addr = '/tmp/uds_server.sock'

if os.path.exists(server_addr):
    os.unlink(server_addr)

sock = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
sock.bind(server_addr)
sock.listen(0)

print 'Server ready.'

while True:
    conn, _ = sock.accept()
    conn.send('Hello there!')
    conn.close()
```

`uds_client.py`

``` python
#!/usr/bin/env python

import socket
import time

server_addr = '/tmp/uds_server.sock'

duration = 1
end = time.time() + duration
msgs = 0

print 'Receiving messages...'

while time.time() < end:
    sock = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
    sock.connect(server_addr)
    data = sock.recv(32)
    msgs += 1
    sock.close()

print 'Received {} messages in {} second(s).'.format(msgs, duration)
```

As we can see by those code snippets, both implementations are close to each other as possible. The differences between them are:

* Their address family: `socket.AF_INET` (IP) and `socket.AF_UNIX` (Unix sockets).
* To bind a process using `socket.AF_UNIX`, the socket file should be removed and created again if it already exists.
* When using `socket.AF_INET`, the `socket.SO_REUSEADDR` flag have to be set in order to avoid `socket.error: [Errno 98] Address already in use` errors that may occur even when the socket is properly closed. This option tells the kernel to reuse the same port if there are connections in the `TIME_WAIT` state.

Both POCs were executed on a Core i3 laptop running Ubuntu 16.04 (Xenial) with stock kernel. There is no output at every loop iteration to avoid the huge performance penalty of writing to a screen. Let's take a look at their performances.

## IP POC

First terminal:

    $ python ip_server.py
    Server ready.

Second terminal:

    $ python ip_client.py
    Receiving messages...
    Received 10159 messages in 1 second(s).

## Unix domain socket POC

First terminal:

    $ python uds_server.py
    Server ready.

Second terminal:

    $ python uds_client.py
    Receiving messages...
    Received 22067 messages in 1 second(s).

The Unix socket implementation can send and receive more than twice the number of messages, over the course of a second, when compared to the IP one. During multiple runs, this proportion is consistent, varying around 10% for more or less on both of them. Now that we figured their performance differences, let's find out why Unix sockets are so much faster.

It's important to notice that both IP and Unix socket implementations are using TCP (`socket.SOCK_STREAM`), so the answer isn't related to how TCP performs in comparison to another transport protocol like UDP, for instance. What happens is that when Unix sockets are used, the entire IP stack from the operating system will be bypassed. There will be no headers being added, checksums being calculated, encapsulation and decapsulation of packets being done nor routing being performed. Although those tasks are performed really fast by the OS, there is still a visible difference when doing benchmarks like this one.

There's so much room for real-world comparisons besides this synthetic measurement demonstrated here. What will be the throughput differences when a reverse proxy like nginx is communicating to a Gunicorn backend server using IP or Unix sockets? Will it impact on latency as well? What about transfering big chunks of data, like huge binary files, instead of small messages? Can Unix sockets be used to avoid Docker network overhead when forwarding ports from the host to a container?

References:

* [Beej's Guide to Unix IPC, 11. Unix Sockets (Brian "Beej Jorgensen" Hall)][beej-unix-sock]
* [Programmation Systèmes, Cours 9 - UNIX Domain Sockets (Stefano Zacchiroli)][zach-unix-sock]
* [Unix Domain Sockets - Python Module of the Week (Doug Hellmann)][pymotw-unix-sock]
* [unix domain sockets vs. internet sockets (Robert Watson)][rwatson-unix-sock]
* [What exactly does SO_REUSEADDR do? (Hermelito Go)][so-reuseaddr]

[beej-unix-sock]: https://beej.us/guide/bgipc/output/html/multipage/unixsock.html
[pymotw-unix-sock]: https://pymotw.com/2/socket/uds.html
[rwatson-unix-sock]: https://lists.freebsd.org/pipermail/freebsd-performance/2005-February/001143.html
[so-reuseaddr]: http://www.unixguide.net/network/socketfaq/4.5.shtml
[zach-unix-sock]: https://upsilon.cc/~zack/teaching/1314/progsyst/cours-09-socket-unix.pdf
