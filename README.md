---
lang: en
title: Wherr Error Handling Package
---

Wherr 🚨
=======

**Wherr** is a Go package designed for **structured error reporting**
that automatically captures file path and line number information,
making debugging and logging easier. It wraps standard Go errors while
providing methods for clear console output and immediate program exit on
failure.

📦 Installation
--------------

To use `wherr` in your Go project, run:
```bash
go get github.com/phillip-england/wherr
``` 

🚀 Usage
-------

### 1. Capturing Location

The core function is `Here()`, which captures the file and line number
of the call site.

```go
import "wherr"

location := wherr.Here()
// location now contains the file path, line number, and working directory
```   

### 2. Creating New Errors (Err)

Use `Err` to create a new `*Wherr` error with a custom message,
embedding the location information.

```go
func someFunction() error {
  // ... logic ...
  return wherr.Err(wherr.Here(), "failed to initialize resource: %s", "db")
}
```        

### 3. Wrapping Existing Errors (Consume)

Use `Consume` to wrap an existing `error` (e.g., from an I/O operation),
prepending a context message and embedding the location. Returns `nil`
if the input error is `nil`.

```go
func readFile(path string) error {
  data, err := os.ReadFile(path)
  if err != nil {
    // Automatically wraps 'err' and adds the context
    return wherr.Consume(wherr.Here(), err, "could not read file")
  }
  return nil
}
```   
  

✨ Features and Methods
----------------------

The `*Wherr` type implements the standard `error` interface and provides
additional utility methods.

### `Error() string`

Implements the `error` interface, returning a formatted string with ANSI
escape codes for **colored terminal output**.

**Format:** `[WHERR][relative/path/to/file.go:123]: Your error message`

### `Unwrap() error`

Implements the standard Go `errors.Wrapper` interface, returning the
original wrapped error (`e.Err`). This allows external tools like
`errors.Is()` and `errors.As()` to function correctly.

### `Print()`

Prints the error message, relative path, and line number to
**`os.Stderr`**.
```go
func execute() {
  if err := someFunction(); err != nil {
    if w, ok := err.(*wherr.Wherr); ok {
      w.Print() // Prints to stderr: 🚨 path/to/file.go:123 — error message
    }
  }
}
```
        

### `Fail()`

Calls `e.Print()` to display the error and then calls **`os.Exit(1)`**
to immediately terminate the program with a non-zero exit code.
