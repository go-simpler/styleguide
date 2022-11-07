# Go Style Guide

> An opinionated style guide for Go

This guide should be treated as an extension to [Effective Go][1],
[Go Code Review Comments][2] and [Uber Go Style Guide][3], not their
replacement.

## 📋 Contents

* [Style](#-style)
  * [Variable declaration](#variable-declaration)
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

## 🧱 Structure

## 🧪 Testing

[1]: https://go.dev/doc/effective_go
[2]: https://github.com/golang/go/wiki/CodeReviewComments
[3]: https://github.com/uber-go/guide/blob/master/style.md
