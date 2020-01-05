---
date: 2017-01-07
layout: post
title: "Benchmarking IP and Unix domain sockets (for real)"
...

In [a previous post][how-fast-uds] an artificial benchmark was done to measure the performance difference between IP and Unix domain sockets. The results were somewhat impressive, as Unix sockets performing at least twice as fast as IP sockets. But how these two forms of communication behaves in the real-world, using a battle-tested application protocol? Would the throughput really double just by switching between them? We'll be using a [Flask][flask] app served by [Gunicorn][gunicorn] behind an [nginx][nginx] reverse proxy to find out.

The following tests were executed on a `c4.large` (2 Cores, 3.75GB RAM) instance on [Amazon Web Services (AWS)][aws]. None of the multi-threading/multi-process options offered by Gunicorn were used, so what we've got here was really what it can serve using a single CPU core. This way, we'll also have the benefit of a free core to run both nginx and the benchmarking ([wrk][wrk]) tool itself.

The application is pretty close to the standard Flask "hello world" example:

`requirements.txt`

```
Flask==0.12
gunicorn==19.6.0
```

`server.py`

``` python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello there!"

if __name__ == "__main__":
    app.run()
```

Gunicorn was used to serve the application with no other option besides `--bind`.

* IP: `gunicorn --bind 0.0.0.0:8000 server:app`
* Unix domain socket: `gunicorn --bind unix:/tmp/gunicorn.sock server:app`

This is the nginx virtual host configuration for both Gunicorn instances:

`/etc/nginx/sites-available/gunicorn`

```
server {
    listen 80;
    server_name bench-ip.myhro.info;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    listen 80;
    server_name bench-unix.myhro.info;

    location / {
        proxy_pass http://unix:/tmp/gunicorn.sock;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

We'll have to append both hostnames to our `/etc/hosts`, in order to avoid the need for a DNS server:

```
(...)
127.0.0.1 bench-ip.myhro.info
127.0.0.1 bench-unix.myhro.info
```

The parameters used in this benchmark were pretty much what `wrk` offers by default. Experimenting with more threads or connections didn't resulted in a significant difference, so the only parameter set was `-d5s`, which means "send the maximum number of requests as you can during five seconds".

## IP benchmark

```
Running 5s test @ http://bench-ip.myhro.info/
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     5.44ms  303.33us  11.56ms   99.02%
    Req/Sec     0.92k    16.21     0.96k    66.00%
  9191 requests in 5.00s, 1.60MB read
Requests/sec:   1837.29
Transfer/sec:    328.26KB
```

## Unix domain socket benchmark

```
Running 5s test @ http://bench-unix.myhro.info/
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     4.95ms  283.81us  11.25ms   97.96%
    Req/Sec     1.01k    24.75     1.04k    90.00%
  10107 requests in 5.00s, 1.76MB read
Requests/sec:   2019.39
Transfer/sec:    360.79KB
```

During multiple runs, these numbers were consistent. The Unix socket virtual host answered around 5 to 10% more requests in average. This number is small but can be significant, specially when dealing with high traffic web servers answering thousands of requests per minute. Anyway, this isn't anywhere near the 100% performance improvement we saw when comparing raw sockets instead of a real protocol like HTTP.

It would still be interesting to compare how this application would perform running inside a Docker container. [Docker is known for having network overhead][docker-network-overhead] when using forwarded ports, so we'll see how much it means in this case. Two files will be used to create our application image and its containers:

`Dockerfile`

``` dockerfile
FROM ubuntu:xenial

RUN apt-get update
RUN apt-get install -y python-pip

ADD . /app

RUN pip install -r /app/requirements.txt

WORKDIR /app
```

`docker-compose.yml`

``` yaml
version: "2"
services:
  base:
    build: .
    image: flask
  ip:
    image: flask
    command: gunicorn --bind 0.0.0.0:8000 server:app
    ports:
      - "8000:8000"
    volumes:
      - .:/app
  uds:
    image: flask
    command: gunicorn --bind unix:/tmp/gunicorn.sock server:app
    volumes:
      - .:/app
      - /tmp:/tmp
```

Let's run `wrk` again, after `docker-compose build` and `docker-compose up`:

## Docker IP benchmark

```
$ wrk -d5s http://bench-ip.myhro.info/
Running 5s test @ http://bench-ip.myhro.info/
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     7.03ms  791.63us  16.84ms   93.51%
    Req/Sec   713.54     20.21   747.00     70.00%
  7109 requests in 5.01s, 1.24MB read
Requests/sec:   1420.17
Transfer/sec:    253.73KB
```

## Docker Unix domain socket benchmark

```
$ wrk -d5s http://bench-unix.myhro.info/
Running 5s test @ http://bench-unix.myhro.info/
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     4.94ms  266.67us  10.74ms   97.24%
    Req/Sec     1.02k    29.87     1.04k    95.00%
  10116 requests in 5.00s, 1.76MB read
Requests/sec:   2022.18
Transfer/sec:    361.29KB
```

The difference between IP sockets over forwarded ports and Unix sockets via shared volumes were huge under Docker. 40-45% is a pretty big number when considering web server performance penalty. With a setup like this one, it would be needed almost twice as hardware resources to serve the same number of clients, which would directly reflect on infrastructure and project costs as a whole.

A few conclusions can be drawn from this experiment:

* Avoid Docker forwarded ports in production environments. Use either Unix sockets or the [`host` network mode][docker-host-network] in this case, as it will introduce virtually no overhead.
* Ports can be easier to manage, instead of a bunch of files, when dealing with multiple processes - either regarding many applications or scaling a single one. If you can afford a little drop in throughput, go for IP sockets.
* If you have to extract every drop of performance available, use Unix domain sockets where possible.

[aws]: https://aws.amazon.com/
[docker-host-network]: https://docs.docker.com/compose/compose-file/#/networkmode
[docker-network-overhead]: https://www.percona.com/blog/2016/02/05/measuring-docker-cpu-network-overhead/
[flask]: http://flask.pocoo.org/
[gunicorn]: http://gunicorn.org/
[how-fast-uds]: /2017/01/how-fast-are-unix-domain-sockets
[nginx]: https://www.nginx.com/
[wrk]: https://github.com/wg/wrk
