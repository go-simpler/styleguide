# styleguide

A styleguide for the `go-simpler` organization.

It should be treated as an extension to [Effective Go][1], [Go Code Review Comments][2] and [Uber Go Style Guide][3].

## Contents

* [Variables](#variables)
  * [Declaration](#declaration)
  * [Initialization](#initialization)
* [Errors](#errors)
  * [Prefix](#prefix)
  * [Panicking](#panicking)
* [Testing](#testing)
  * [Test package](#test-package)
  * [Assertions](#assertions)
* [Project layout](#project-layout)
* [Interface segregation](#interface-segregation)

## Variables

### Declaration

Use the `var` form when zero value is enough.

```go
var builder strings.Builder // NOT builder := strings.Builder{}
```

Use the `:=` form when initialization is required.

```go
replacer := strings.NewReplacer(...)
```

> This pattern highlights ready-to-use zero values via the Go syntax.

### Initialization

By default, initialize your variables with values, not pointers.

```go
foo := Foo{...} // NOT &Foo{...}
```

When you need a pointer, explicitly take the address via `&`.

```go
// 100+ LoC later...

fn(&foo) // foo is 100% a value, no need to revisit its declaration to clarify.
```

> Thus, there is no need to remember what a variable holds, no matter how big its scope is.

## Errors

### Prefix

Prefix errors with the name of their package.

> It helps to quickly identify the producer of the error.

```go
package foo

var ErrFoo = errors.New("foo: something went wrong")
```

### Panicking

It's ok to panic if the problem is caused by incorrect usage of the library, not by user input.
For example, `strings.NewReplacer` panics on odd number of arguments.

> Better to fall faster and louder if we're absolutely sure that the program is not working correctly.

## Testing

### Test package

Prefer a separate package for tests.
If you need to test unexported functions, do it in a separate `*_internal_test.go` file.

> By doing so, you become the first user of your API, [dogfooding][4] helps to design it better.

```go
// foo_test.go
package foo_test // NOT foo

import "foo"

func TestFoo(t *testing.T) {
    foo.Foo()
}

// foo_internal_test.go
package foo

func Test_foo(t *testing.T) {
    foo()
}
```

### Assertions

Use testing helpers or an assertion package (e.g. [go-simpler/assert][5]) when you need to write a lot of tests.

> Sticking to the standard library is great but sometimes vanilla `testing` is too verbose.

```go
func TestFoo(t *testing.T) {
    // too verbose for big tests.
    if got := foo.Foo(); got != want {
        t.Errorf("got %v; want %v", got, want)
    }

    // less LoC, reads better, behaves the same.
    got := foo.Foo()
    assert.Equal[E](t, got, want)
}
```

## Project layout

Avoid using `pkg`.
Use `internal` for non-public API.
Treat any non-internal package as importable.

> Unlike `pkg`, `internal` is officially supported by the `go` tool.

## Interface segregation

Prefer [smaller interfaces][6] when possible.
Remove unused interface methods on the consumer's side.

> A little copying is better than a little dependency.

```go
// producer.go
package producer

type T struct {}

func (T) Foo() {}
func (T) Bar() {}

// consumer.go
package consumer

type I interface {
    Foo()
    // Bar() is obsolete here.
}

func F(i I) { i.Foo() } // F only uses Foo(), it does not need Bar().
```

[1]: https://go.dev/doc/effective_go
[2]: https://github.com/golang/go/wiki/CodeReviewComments
[3]: https://github.com/uber-go/guide/blob/master/style.md
[4]: https://en.wikipedia.org/wiki/Eating_your_own_dog_food
[5]: https://github.com/go-simpler/assert
[6]: https://en.wikipedia.org/wiki/Interface_segregation_principle
