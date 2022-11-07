# Go Style Guide

> An opinionated style guide for Go

This guide should be treated as an extension to [Effective Go][1],
[Go Code Review Comments][2] and [Uber Go Style Guide][3], not their
replacement.

## 📋 Contents

* [Style](#-style)
  * [Variable declaration](#variable-declaration)
  * [Variable initialization](#variable-initialization)
  * [Error prefixes](#error-prefixes)
* [Structure](#-structure)
  * [Project layout](#project-layout)
  * [Interface segregation](#interface-segregation)
* [Testing](#-testing)
  * [Test package](#test-package)
  * [Assertions](#assertions)
* [See also](#-see-also)

## 🕶 Style

### Variable declaration

Use the `var` form when zero value is enough. Use the `:=` form when
initialization is required.

> Highlighting ready-to-use zero values via Go syntax boosts readability.

```go
// declared via `var`: safe to use as zero value.
var builder strings.Builder

// declared via `:=`: initialization is required.
replacer := strings.NewReplacer(...)
```

⚠️ One exception may be writing `flag := false` instead of `var flag bool`
because the former reads better.

### Variable initialization

By default, initialize your variables with values, not pointers. When you need a
pointer, explicitly take the address via `&`.

> This way there is no need to remember what a variable holds, no matter how big
> its scope.

```go
cfg := config{...} // NOT cfg := &config{...}

// 100+ LoC later...

fn(&cfg) // cfg is 100% a value, no need to revisit its declaration.
```

### Error prefixes

Prefix errors with the name of their package.

> It helps to quickly identify the producer of the error.

```go
package foo

var ErrFoo = errors.New("foo: something went wrong")
```

## 🧱 Structure

### Project layout

Avoid `pkg` in libraries. Avoid `pkg`/`internal` in applications.

> Any package in a library should be treated as importable (except for
> `internal/*`). Any package in an application should be treated as
> non-importable.

### Interface segregation

Prefer [smaller interfaces][4] when possible. Remove unused interface methods on
the consumer's side.

> A little copying is better than a little dependency.

```go
// Package foo is a producer.
package foo

type Foo struct {}

func (Foo) DoA() {}
func (Foo) DoB() {}

// Package bar is a consumer.
package bar

type A interface {
	DoA()
	// DoB() is obsolete here.
}

// Bar only uses DoA(), no need to require DoB() in the interface.
func Bar(a A) { a.DoA() }
```

## 🧪 Testing

### Test package

Prefer a separate package for tests. If you need to test unexported functions,
do it in a separate `*_internal_test.go` file.

> By doing so you become the first user of your API, [dogfooding][5] helps to
> design it better.

```go
package foo_test // NOT foo

import "foo"

func TestFoo(t *testing.T) {
	foo.Foo() // try the API from a user's perspective while writing tests.
}
```

### Assertions

Use testing helpers or an assert package (e.g. [junk1tm/assert][6]) when you
need to write a lot of tests.

> Sticking to the standard library is great but sometimes vanilla `testing` is
> too verbose.

```go
func TestFoo(t *testing.T) {
	// ok for small tests, too verbose for bigger ones.
	if got := foo.Foo(); got != want {
		t.Errorf("got %v; want %v", got, want)
	}

	// less LoC, reads better, behaves the same.
	assert.Equal[E](t, foo.Foo(), want)
}
```

## 👀 See also

* https://github.com/cristaloleg/go-advice
* https://github.com/bahlo/go-styleguide

[1]: https://go.dev/doc/effective_go
[2]: https://github.com/golang/go/wiki/CodeReviewComments
[3]: https://github.com/uber-go/guide/blob/master/style.md
[4]: https://en.wikipedia.org/wiki/Interface_segregation_principle
[5]: https://en.wikipedia.org/wiki/Eating_your_own_dog_food
[6]: https://github.com/junk1tm/assert
