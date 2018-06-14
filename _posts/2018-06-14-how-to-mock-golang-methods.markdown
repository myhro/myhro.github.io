---
date: 2018-06-14
layout: post
title: How to mock Go methods
...

> Warning: this post wouldn't exist if it wasn't the help of my long-time friend, previous university and work colleague, [Fernando Matos][fernandomrm]. We discussed the possibilities for a few hours in order to figure the following implementations. I hope we can work together on a daily basis again in the future.

Imagine the following Go code:


`main.go`

```go
package main

import "time"

type Client struct {
	timeout time.Duration
}

func New() Client {
	return Client{timeout: 1 * time.Second}
}

func (c *Client) Fetch() string {
	time.Sleep(c.timeout)
	return "actual Fetch"
}

func main() {
	c := New()
	c.Fetch()
}
```

In this example, the `Fetch()` method is just sleeping for a pre-defined duration, but imagine that it is a real external API call, involving a slow and expensive network request. How can we test that?

`main_test.go`

```go
package main

import "testing"

func TestFetch(t *testing.T) {
	c := New()
	r := c.Fetch()
	t.Fatal(r)
}
```

If the actual `Fetch()` implementation is called, the test execution will take too long:

```
$ go test
--- FAIL: TestFetch (1.00s)
        main_test.go:8: actual Fetch
FAIL
exit status 1
FAIL    _/Users/myhro/tmp     1.009s
```

No one is going to wait a few seconds in each test run where this method is called a couple times. A naive approach to circumvent that would be trying to replace this method with another one with the same name that would avoid the slow operation:

```go
func (c *Client) Fetch() string {
	return "mocked Fetch"
}
```

But in Go, this isn't possible:

```
./main_test.go:5:6: (*Client).Fetch redeclared in this block
        previous declaration at ./main.go:13:6
```

So we have to look for another solution, like the [delegation design pattern][delegation-pattern]. Instead of having the `Fetch()` method do what it is supposed to do, it _delegates_ its responsibility to an encapsulated object.

`main.go`

```go
package main

import "time"

type Client struct {
	delegate clientDelegate
	timeout  time.Duration
}

type clientDelegate interface {
	delegatedFetch(time.Duration) string
}

func (c *Client) delegatedFetch(t time.Duration) string {
	time.Sleep(t)
	return "actual Fetch"
}

func New() Client {
	n := Client{
		delegate: &Client{},
		timeout:  1 * time.Second,
	}
	return n
}

func (c *Client) Fetch() string {
	return c.delegate.delegatedFetch(c.timeout)
}

func main() {
	c := New()
	c.Fetch()
}
```

This way, we can replace the implementation of this inner object without having to override the entire object that is being tested:

`main_test.go`

```go
package main

import (
	"testing"
	"time"
)

type fakeClient struct{}

func (c *fakeClient) delegatedFetch(t time.Duration) string {
	return "mocked Fetch"
}

func TestFetch(t *testing.T) {
	c := New()
	c.delegate = &fakeClient{}
	r := c.Fetch()
	t.Fatal(r)
}
```

Now the mocked `Fetch()` is called and the test execution finishes in no time:

```
$ go test
--- FAIL: TestFetch (0.00s)
        main_test.go:18: mocked Fetch
FAIL
exit status 1
FAIL    _/Users/myhro/tmp     0.006s
```

So the delegation pattern approach works, but there are a few drawbacks:

- It needs an interface that is going to be used only by the methods that are supposed to be mocked;
- The inner object can't see its parent attributes, so they have to be passed as arguments;
- This looks too verbose and there should probably be a shorter/simpler way to that.

One cool thing about Go functions is that they can be treated as types, so they can be used as struct members or [passed as arguments to other functions][effective-go-funcs]. This allows us to do things like:

`main.go`

```go
package main

import "time"

type fetchType func(time.Duration) string

type Client struct {
	fetchImp fetchType
	timeout  time.Duration
}

func sleepFetch(t time.Duration) string {
	time.Sleep(t)
	return "actual Fetch"
}

func New() Client {
	n := Client{
		fetchImp: sleepFetch,
		timeout:  1 * time.Second,
	}
	return n
}

func (c *Client) Fetch() string {
	return c.fetchImp(c.timeout)
}

func main() {
	c := New()
	c.Fetch()
}
```

And to replace the `Fetch()` implementation when testing:

`main_test.go`

```go
package main

import (
	"testing"
	"time"
)

func FakeFetch(t time.Duration) string {
	return "mocked Fetch"
}

func TestFetch(t *testing.T) {
	c := New()
	c.fetchImp = FakeFetch
	r := c.Fetch()
	t.Fatal(r)
}
```

Achieving the same results:

```
$ go test
--- FAIL: TestFetch (0.00s)
        main_test.go:16: mocked Fetch
FAIL
exit status 1
FAIL    _/Users/myhro/tmp     0.007s
```

It's interesting to notice that the `FetchType` declaration itself can be omitted, resulting in:

```go
type Client struct {
	fetchImp func(time.Duration) string
	timeout  time.Duration
}
```

Thus avoiding the creation of a dummy `interface`, `type` or `struct` only for mocking it later.

Updates:

1. [Sandor Szücs][szuecs] pointed out that we have to care about not unintentionally exporting fake/internal methods or structs. Thanks!


[delegation-pattern]: https://en.wikipedia.org/wiki/Delegation_pattern
[effective-go-funcs]: https://golang.org/doc/effective_go.html#interface_methods
[fernandomrm]: https://github.com/fernandomrm
[szuecs]: https://github.com/szuecs
