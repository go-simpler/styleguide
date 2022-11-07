# Go Style Guide

> An opinionated style guide for Go

This guide should be treated as an extension to [Effective Go][1],
[Go Code Review Comments][2] and [Uber Go Style Guide][3], not their
replacement.

## 📋 Contents

* [Style](#-style)
  * [Variable declaration](#variable-declaration)
  * [Variable initialization](#variable-initialization)
* [Structure](#-structure)
* [Testing](#-testing)

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

## 🧱 Structure

## 🧪 Testing

[1]: https://go.dev/doc/effective_go
[2]: https://github.com/golang/go/wiki/CodeReviewComments
[3]: https://github.com/uber-go/guide/blob/master/style.md
