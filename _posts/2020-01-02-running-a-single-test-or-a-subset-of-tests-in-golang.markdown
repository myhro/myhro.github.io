---
date: 2020-01-02
layout: post
title: Running a single test (or a subset of tests) in Go
...

In the very beginning of the Go documentation about tests in the standard library, there's a [section about subtests][golang-subtests]. The idea is that with the `-run` argument of `go test` it's possible to choose which tests are going to be executed in a single run of a project's test suite. That doesn't just makes it possible to focus on tests which are relevant to the parts of the code which are being worked on, but it's specially useful when there's a need to avoid slow tests or a big suite full of them.

The following code uses the standard `testing` package:

```go
package main

import (
	"testing"
)

func TestA(t *testing.T) {
	want := 2
	got := 2
	if got != want {
		t.Fatal("Wrong result!")
	}
}

func TestB(t *testing.T) {
	want := 4
	got := 4
	if got != want {
		t.Fatal("Wrong result!")
	}
}
```

These tests can be filtered as expected:

```
$ go test -v .
=== RUN   TestA
--- PASS: TestA (0.00s)
=== RUN   TestB
--- PASS: TestB (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.181s
$ go test -run TestA -v .
=== RUN   TestA
--- PASS: TestA (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.188s
```

When using the standard `testing` package, `-run` is all that is needed. The problem is that this doesn't work for third-party test suites, like [gocheck][gocheck]:

```go
package main

import (
	"testing"

	. "gopkg.in/check.v1"
)

func Test(t *testing.T) {
	TestingT(t)
}

type MainSuite struct{}

var _ = Suite(&MainSuite{})

func (s *MainSuite) TestA(c *C) {
	want := 2
	got := 2
	c.Assert(got, Equals, want)
}

func (s *MainSuite) TestB(c *C) {
	want := 4
	got := 4
	c.Assert(got, Equals, want)
}
```

When running this test suite with `-run`, all that it yields is `no tests to run`:

```
$ go test -v .
=== RUN   Test
OK: 2 passed
--- PASS: Test (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.270s
$ go test -run TestA -v .
testing: warning: no tests to run
PASS
ok      github.com/myhro/go-tests-example       0.187s [no tests to run]
```

This happens because `gocheck` needs a different parameter to filter tests out. The right way to do it is with `-check.f`:

```
$ go test -check.f TestA -v .
=== RUN   Test
OK: 1 passed
--- PASS: Test (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.687s
```

Another test library that needs a different parameter is [Testify][testify]. With this example:

```go
package main

import (
	"testing"

	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/suite"
)

type MainSuite struct {
	suite.Suite
}

func TestMainSuite(t *testing.T) {
	suite.Run(t, new(MainSuite))
}

func (s *MainSuite) TestA() {
	want := 2
	got := 2
	assert.Equal(s.T(), want, got)
}

func (s *MainSuite) TestB() {
	want := 4
	got := 4
	assert.Equal(s.T(), want, got)
}
```

Tests are filtered with `-testify.m`:

```
$ go test -v .
=== RUN   TestMainSuite
=== RUN   TestMainSuite/TestA
=== RUN   TestMainSuite/TestB
--- PASS: TestMainSuite (0.00s)
    --- PASS: TestMainSuite/TestA (0.00s)
    --- PASS: TestMainSuite/TestB (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.762s
$ go test -testify.m TestA -v .
=== RUN   TestMainSuite
=== RUN   TestMainSuite/TestA
--- PASS: TestMainSuite (0.00s)
    --- PASS: TestMainSuite/TestA (0.00s)
PASS
ok      github.com/myhro/go-tests-example       0.220s
```

I wasn't able to find the exact explanation why `-run` doesn't work for third-party test suites. My reasoning is that the regular `go test` command is only aware of `Test*` functions, not methods that are tied to a particular test suite `struct`. The later have to be properly loaded, and filtered if needed, by their own libraries. Hence the need for a different argument.

And what about running more than one test? All of the mentioned test libraries accept regular expressions when specifying test names, so both `TestA` and `TestB` can be selected with `Test[AB]`.


[gocheck]: http://labix.org/gocheck
[golang-subtests]: https://golang.org/pkg/testing/#hdr-Subtests_and_Sub_benchmarks
[testify]: https://github.com/stretchr/testify
