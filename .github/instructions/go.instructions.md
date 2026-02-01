---
description: 'Go (Golang) coding conventions, idioms, and best practices based on Effective Go and official Go style guidelines for writing clear, idiomatic, and production-ready Go code.'
applyTo: '**/*.go, **/go.mod, **/go.sum'
---

# Go Language Development Guidelines

Comprehensive coding conventions and best practices for Go (Golang) development based on Effective Go and official Go documentation. These guidelines ensure idiomatic, readable, and maintainable Go code.

## Project Context

- Language: Go 1.21+
- Style Guide: Effective Go, Go Code Review Comments
- Formatter: `gofmt` / `go fmt`
- Linter: `golint`, `staticcheck`, `golangci-lint`
- Build System: Go modules (`go.mod`)

---

## Formatting

### Use gofmt

- **Always run `gofmt`** on all Go source files before committing
- Use `go fmt ./...` at the package level to format the entire project
- Do not manually adjust formatting that `gofmt` handles; let the tool decide
- All Go code in the standard library and most open-source projects uses `gofmt`

### Indentation

- Use **tabs** for indentation, not spaces
- `gofmt` emits tabs by default
- Use spaces only if absolutely necessary for alignment within a line

### Line Length

- Go has **no official line length limit**
- If a line feels too long, wrap it and indent continuation with an extra tab
- Prioritize readability over strict line limits

### Parentheses

- Go requires fewer parentheses than C or Java
- Control structures (`if`, `for`, `switch`) do not use parentheses around conditions
- Use spacing to clarify operator precedence: `x<<8 + y<<16` reads as expected

```go
// Good - no unnecessary parentheses
if x > 0 {
    return y
}

// Bad - unnecessary parentheses
if (x > 0) {
    return y
}
```

### Brace Placement

- Opening braces **must be on the same line** as the control statement
- Due to automatic semicolon insertion, placing braces on the next line causes syntax errors

```go
// Good
if i < f() {
    g()
}

// Bad - causes syntax error due to semicolon insertion
if i < f()
{
    g()
}
```

---

## Naming Conventions

### General Rules

- Names are **semantically significant** in Go: visibility is determined by case
- **Exported names** (accessible outside the package) start with an uppercase letter
- **Unexported names** (package-private) start with a lowercase letter
- Use **MixedCaps** or **mixedCaps** rather than underscores for multi-word names

### Package Names

| Guideline | Example |
|-----------|---------|
| Use lowercase, single-word names | `bytes`, `http`, `json` |
| Avoid underscores and mixedCaps | `encoding` not `encoding_base64` |
| Keep names short and concise | `bufio` not `bufferedIO` |
| Package name is the base of import path | `encoding/base64` → package `base64` |

```go
// Good
import "encoding/json"
json.Marshal(v)

// Avoid - redundant naming
import "encoding/json"
json.JSONMarshal(v)  // JSON is redundant with package name
```

### Avoiding Repetition

- Exported names are qualified by their package name
- Do not repeat the package name in the identifier

```go
// Good - accessed as bufio.Reader
package bufio
type Reader struct { ... }

// Bad - accessed as bufio.BufReader (redundant)
package bufio
type BufReader struct { ... }
```

### Getters and Setters

- Go does **not** provide automatic getters/setters
- Getter methods should **not** include `Get` in the name
- Setter methods should use `Set` prefix

```go
// Good
func (obj *Object) Owner() string { return obj.owner }
func (obj *Object) SetOwner(owner string) { obj.owner = owner }

// Bad - don't use Get prefix for getters
func (obj *Object) GetOwner() string { return obj.owner }
```

### Interface Names

- One-method interfaces are named by the **method name plus -er suffix**
- Honor canonical names and signatures for well-known methods

| Method | Interface Name |
|--------|----------------|
| `Read` | `Reader` |
| `Write` | `Writer` |
| `Close` | `Closer` |
| `Flush` | `Flusher` |
| `String` | `Stringer` |

```go
// Good - follows convention
type Reader interface {
    Read(p []byte) (n int, err error)
}

// Good - compound interfaces embed single-method interfaces
type ReadWriter interface {
    Reader
    Writer
}
```

### Variable and Function Names

| Scope | Convention | Example |
|-------|------------|---------|
| Local variables | Short, concise | `i`, `n`, `err`, `buf` |
| Package-level variables | Descriptive | `DefaultTimeout`, `ErrNotFound` |
| Function names | Descriptive, concise | `NewReader`, `ParseInt` |
| Constants | MixedCaps | `MaxIdleConns`, `EOF` |

```go
// Good - short names for local scope
for i := 0; i < len(items); i++ { ... }

// Good - descriptive for package-level
var DefaultHTTPClient = &http.Client{Timeout: 30 * time.Second}

// Good - error variables prefixed with Err
var ErrNotFound = errors.New("not found")
```

---

## Semicolons

### Automatic Insertion

- Go's lexer automatically inserts semicolons after certain tokens
- Semicolons appear in source only in `for` loop clauses or to separate multiple statements on one line

### Rules for Semicolon Insertion

A semicolon is inserted after a newline if the preceding token is:

- An identifier (including keywords like `int`, `float64`)
- A basic literal (number, string, character)
- One of: `break`, `continue`, `fallthrough`, `return`, `++`, `--`, `)`, `}`

```go
// Semicolons are implicit
x := 1
y := 2

// Explicit semicolons only in for loops
for i := 0; i < 10; i++ { ... }
```

---

## Control Structures

### If Statements

- Braces are mandatory, even for single-line bodies
- Accept an initialization statement before the condition
- Omit `else` when the `if` body ends with `break`, `continue`, `goto`, or `return`

```go
// Good - initialization statement
if err := file.Chmod(0644); err != nil {
    log.Print(err)
    return err
}

// Good - omit unnecessary else
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)

// Bad - unnecessary else
f, err := os.Open(name)
if err != nil {
    return err
} else {
    codeUsing(f)  // else is unnecessary
}
```

### For Loops

Go unifies `for` and `while` into a single construct:

```go
// C-style for loop
for i := 0; i < 10; i++ { ... }

// While-style loop
for condition { ... }

// Infinite loop
for { ... }

// Range over slice/array
for index, value := range slice { ... }

// Range over map
for key, value := range m { ... }

// Range - ignore index
for _, value := range slice { ... }

// Range - keys only
for key := range m { ... }
```

### String Iteration

- `range` on strings iterates over **Unicode code points (runes)**, not bytes
- Each iteration yields the byte position and the rune value

```go
for pos, char := range "日本語" {
    fmt.Printf("character %c at byte position %d\n", char, pos)
}
```

### Switch Statements

- Cases are evaluated top to bottom until a match is found
- **No automatic fallthrough** (unlike C)
- Cases can be comma-separated lists
- `switch` without an expression switches on `true`

```go
// Switch on value
switch c {
case ' ', '?', '&', '=', '#', '+':
    return true
}

// Switch on true (if-else chain)
switch {
case x < 0:
    return -1
case x > 0:
    return 1
default:
    return 0
}

// Type switch
switch v := value.(type) {
case string:
    fmt.Println("string:", v)
case int:
    fmt.Println("int:", v)
default:
    fmt.Printf("unknown type: %T\n", v)
}
```

### Break with Labels

- Use labeled `break` to exit an outer loop from within a switch

```go
Loop:
    for n := 0; n < len(src); n++ {
        switch {
        case src[n] < threshold:
            if shouldBreak {
                break Loop  // Breaks outer for loop
            }
        }
    }
```

---

## Functions

### Multiple Return Values

- Functions can return multiple values
- Use this for error handling and returning related values

```go
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

// Usage
result, err := divide(10, 2)
if err != nil {
    log.Fatal(err)
}
```

### Named Return Values

- Named return values serve as documentation
- A bare `return` returns the current values of named return variables
- Use judiciously; can reduce clarity in long functions

```go
// Good - named returns document meaning
func ReadFull(r io.Reader, buf []byte) (n int, err error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return  // Returns current values of n and err
}
```

### Defer

- `defer` schedules a function call to run when the surrounding function returns
- Deferred calls execute in **LIFO order** (last in, first out)
- Arguments to deferred functions are evaluated when `defer` executes, not when the deferred call runs

```go
// Good - guarantee resource cleanup
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // Will run when function returns

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf)
        result = append(result, buf[:n]...)
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err
        }
    }
    return string(result), nil
}
```

### Variadic Functions

- Use `...` to accept variable number of arguments
- Pass a slice to a variadic function with `slice...`

```go
func Min(values ...int) int {
    min := math.MaxInt
    for _, v := range values {
        if v < min {
            min = v
        }
    }
    return min
}

// Usage
Min(1, 2, 3)
Min(nums...)  // Expand slice
```

---

## Data Types

### Allocation: new vs make

| Function | Purpose | Returns | Types |
|----------|---------|---------|-------|
| `new(T)` | Allocates zeroed storage | `*T` (pointer to zero value) | Any type |
| `make(T, args)` | Creates and initializes | `T` (initialized value) | Slices, maps, channels only |

```go
// new - allocates and zeros
p := new(SyncedBuffer)  // *SyncedBuffer, zero value

// make - creates and initializes
s := make([]int, 10)           // Slice with length 10
m := make(map[string]int)      // Empty, initialized map
ch := make(chan int, 100)      // Buffered channel
```

### Zero Values

- Design types so the zero value is useful without initialization
- Examples: `bytes.Buffer` (empty buffer), `sync.Mutex` (unlocked mutex)

```go
// Good - zero value is useful
type SyncedBuffer struct {
    lock   sync.Mutex
    buffer bytes.Buffer
}

var sb SyncedBuffer  // Ready to use immediately
```

### Composite Literals

- Create and initialize structs, arrays, slices, and maps in one expression
- Field order matters when unlabeled; use labels for clarity

```go
// Struct literal - labeled fields (preferred)
return &File{fd: fd, name: name}

// Struct literal - unlabeled (all fields required in order)
return &File{fd, name, nil, 0}

// Slice literal
primes := []int{2, 3, 5, 7, 11}

// Map literal
timeZone := map[string]int{
    "UTC": 0,
    "EST": -5 * 60 * 60,
    "PST": -8 * 60 * 60,
}
```

### Arrays

- Arrays are **values** in Go (copying an array copies all elements)
- Array size is part of the type: `[10]int` ≠ `[20]int`
- Prefer slices over arrays in most cases

```go
// Array declaration
var a [10]int

// Array literal with inferred size
b := [...]string{"one", "two", "three"}

// Pass array pointer for efficiency (but prefer slices)
func Sum(a *[3]float64) float64 {
    var sum float64
    for _, v := range *a {
        sum += v
    }
    return sum
}
```

### Slices

- Slices are **references** to underlying arrays
- Use slices as the primary sequence type
- Modifying slice elements affects the underlying array

```go
// Create slice
s := make([]int, 10)      // length 10, capacity 10
s := make([]int, 10, 100) // length 10, capacity 100

// Slice from array
arr := [5]int{1, 2, 3, 4, 5}
s := arr[1:4]  // [2, 3, 4]

// Append to slice
s = append(s, 6, 7, 8)

// Append slice to slice
s1 := []int{1, 2}
s2 := []int{3, 4}
s1 = append(s1, s2...)
```

### Maps

- Maps are **reference types** (changes visible to caller)
- Keys must be comparable (equality operator defined)
- Use "comma ok" idiom to distinguish missing keys from zero values

```go
// Create map
m := make(map[string]int)

// Map literal
m := map[string]int{
    "one":   1,
    "two":   2,
    "three": 3,
}

// Check if key exists
value, ok := m["key"]
if ok {
    // key exists
}

// Delete key
delete(m, "key")

// Implement a set
seen := make(map[string]bool)
seen["item"] = true
if seen["item"] { ... }
```

---

## Methods

### Value vs Pointer Receivers

| Receiver Type | When to Use |
|---------------|-------------|
| Value `(t T)` | Method doesn't modify receiver; receiver is small |
| Pointer `(t *T)` | Method modifies receiver; receiver is large; consistency with other methods |

```go
// Value receiver - doesn't modify
func (s Sequence) Len() int {
    return len(s)
}

// Pointer receiver - modifies receiver
func (p *ByteSlice) Append(data []byte) {
    *p = append(*p, data...)
}
```

### Method Invocation Rules

- Value methods can be called on both values and pointers
- Pointer methods can **only** be called on pointers (or addressable values)
- The compiler automatically takes the address when calling a pointer method on an addressable value

```go
var b ByteSlice
b.Append(data)      // Compiler rewrites to (&b).Append(data)
(&b).Append(data)   // Explicit pointer

// Cannot call pointer method on non-addressable value
ByteSlice{}.Append(data)  // Error: ByteSlice{} is not addressable
```

---

## Interfaces

### Interface Design

- Interfaces define behavior, not data
- Keep interfaces small (one or two methods)
- A type implements an interface by implementing its methods (no explicit declaration)

```go
// Good - small, focused interface
type Reader interface {
    Read(p []byte) (n int, err error)
}

// Compose interfaces from smaller ones
type ReadWriter interface {
    Reader
    Writer
}
```

### Interface Satisfaction

- Any type that implements all methods of an interface satisfies it
- No explicit "implements" declaration needed
- Compile-time interface check with blank identifier:

```go
// Verify *RawMessage implements json.Marshaler at compile time
var _ json.Marshaler = (*RawMessage)(nil)
```

### Type Assertions

- Extract concrete type from interface value
- Use "comma ok" idiom to safely assert type

```go
// Type assertion (panics if wrong type)
s := value.(string)

// Safe type assertion
s, ok := value.(string)
if ok {
    fmt.Println(s)
}

// Type switch
switch v := value.(type) {
case string:
    fmt.Println("string:", v)
case int:
    fmt.Println("int:", v)
default:
    fmt.Printf("unknown: %T\n", v)
}
```

### Common Interfaces

Implement these standard interfaces when appropriate:

| Interface | Method | Use Case |
|-----------|--------|----------|
| `fmt.Stringer` | `String() string` | Custom string representation |
| `error` | `Error() string` | Error values |
| `io.Reader` | `Read([]byte) (int, error)` | Reading data |
| `io.Writer` | `Write([]byte) (int, error)` | Writing data |
| `io.Closer` | `Close() error` | Resource cleanup |
| `sort.Interface` | `Len`, `Less`, `Swap` | Custom sorting |

---

## Error Handling

### Error Conventions

- Errors have type `error` (interface with `Error() string`)
- Return errors as the last return value
- Error strings should not be capitalized or end with punctuation
- Prefix error strings with the package or function name

```go
// Good - error as last return value
func Read(name string) ([]byte, error) {
    f, err := os.Open(name)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    return io.ReadAll(f)
}

// Good - error string format
return fmt.Errorf("parse %s: %w", filename, err)
```

### Error Handling Pattern

- Check errors immediately after function calls
- Handle errors before success path
- Do not ignore errors with `_`

```go
// Good - check and handle immediately
f, err := os.Open(name)
if err != nil {
    return err  // Return early on error
}
defer f.Close()
// Success path continues

// Bad - ignoring errors
f, _ := os.Open(name)  // Never ignore errors
```

### Custom Error Types

- Create custom error types for rich error information
- Implement the `error` interface

```go
type PathError struct {
    Op   string
    Path string
    Err  error
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}

func (e *PathError) Unwrap() error {
    return e.Err
}
```

### Panic and Recover

- Use `panic` only for unrecoverable errors
- Use `recover` in deferred functions to catch panics
- Do not expose panics to package consumers; convert to errors

```go
// Good - recover and convert to error
func Parse(input string) (result *AST, err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("parse error: %v", r)
        }
    }()
    return doParse(input)
}

// Good - panic for truly impossible situations
func MustCompile(pattern string) *Regexp {
    r, err := Compile(pattern)
    if err != nil {
        panic("invalid pattern: " + pattern)
    }
    return r
}
```

---

## Concurrency

### Goroutines

- Goroutines are lightweight threads managed by Go runtime
- Start with `go` keyword before function call
- Goroutines share address space; coordinate with channels or sync primitives

```go
// Start goroutine
go func() {
    // Concurrent work
}()

// Goroutine with parameters
go func(msg string) {
    fmt.Println(msg)
}("hello")
```

### Share by Communicating

> **"Do not communicate by sharing memory; instead, share memory by communicating."**

- Prefer channels over shared memory with mutexes
- Design so only one goroutine has access to data at a time

```go
// Good - communicate via channels
func worker(jobs <-chan Job, results chan<- Result) {
    for job := range jobs {
        results <- process(job)
    }
}
```

### Channels

```go
// Unbuffered channel (synchronous)
ch := make(chan int)

// Buffered channel
ch := make(chan int, 100)

// Send and receive
ch <- value    // Send
value := <-ch  // Receive

// Close channel (sender's responsibility)
close(ch)

// Range over channel
for value := range ch {
    // Process value until channel closed
}
```

### Channel Patterns

```go
// Wait for goroutine completion
done := make(chan bool)
go func() {
    // Do work
    done <- true
}()
<-done  // Wait

// Semaphore pattern (limit concurrency)
sem := make(chan struct{}, maxConcurrent)
for _, item := range items {
    sem <- struct{}{}  // Acquire
    go func(item Item) {
        defer func() { <-sem }()  // Release
        process(item)
    }(item)
}

// Select for multiple channels
select {
case msg := <-ch1:
    handle(msg)
case ch2 <- value:
    // Sent successfully
case <-time.After(timeout):
    // Timeout
default:
    // Non-blocking
}
```

### Channels of Channels

- Channels are first-class values; can be sent on channels
- Useful for request/response patterns

```go
type Request struct {
    Args       []int
    ResultChan chan int
}

// Client
req := &Request{Args: []int{1, 2, 3}, ResultChan: make(chan int)}
requestChan <- req
result := <-req.ResultChan

// Server
func handle(queue chan *Request) {
    for req := range queue {
        req.ResultChan <- compute(req.Args)
    }
}
```

---

## The Blank Identifier

### Discarding Values

```go
// Ignore value in range
for _, value := range slice { ... }

// Ignore return value
_, err := io.Copy(dst, src)

// Import for side effects only
import _ "net/http/pprof"
```

### Compile-Time Interface Check

```go
// Verify type implements interface at compile time
var _ json.Marshaler = (*MyType)(nil)
```

### Silence Unused Imports (Temporary)

```go
import (
    "fmt"
    "io"
)

var _ = fmt.Printf  // For debugging; delete when done
var _ io.Reader     // For debugging; delete when done
```

---

## Embedding

### Interface Embedding

```go
type ReadWriter interface {
    Reader
    Writer
}
```

### Struct Embedding

- Embedded types' methods become methods of the outer type
- Access embedded type directly by its type name

```go
type Job struct {
    Command string
    *log.Logger  // Embedded - Job now has Logger methods
}

func NewJob(cmd string) *Job {
    return &Job{cmd, log.New(os.Stderr, "Job: ", log.Ldate)}
}

// Use Logger methods directly
job.Println("starting...")

// Access embedded field by type name
job.Logger.SetPrefix("NewPrefix: ")
```

---

## Initialization

### Constants

- Constants are created at compile time
- Can only be numbers, characters (runes), strings, or booleans
- Use `iota` for enumerated constants

```go
type ByteSize float64

const (
    _           = iota  // Ignore first value
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
)
```

### Variables

- Package-level variables initialized before `main` runs
- Can use computed values (unlike constants)

```go
var (
    home   = os.Getenv("HOME")
    user   = os.Getenv("USER")
    gopath = os.Getenv("GOPATH")
)
```

### The init Function

- Each file can have multiple `init` functions
- Called after all variable declarations are evaluated
- Called after all imported packages are initialized

```go
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
}
```

---

## Printing

### Format Verbs

| Verb | Description |
|------|-------------|
| `%v` | Default format |
| `%+v` | Struct with field names |
| `%#v` | Go syntax representation |
| `%T` | Type of value |
| `%d` | Integer (decimal) |
| `%x` | Integer (hexadecimal) |
| `%s` | String |
| `%q` | Quoted string |
| `%p` | Pointer |
| `%t` | Boolean |
| `%f` | Floating point |

```go
type Point struct{ X, Y int }
p := Point{1, 2}

fmt.Printf("%v\n", p)   // {1 2}
fmt.Printf("%+v\n", p)  // {X:1 Y:2}
fmt.Printf("%#v\n", p)  // main.Point{X:1, Y:2}
fmt.Printf("%T\n", p)   // main.Point
```

### Stringer Interface

```go
func (p Point) String() string {
    return fmt.Sprintf("(%d, %d)", p.X, p.Y)
}

// Avoid infinite recursion
type MyString string

func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", string(m))  // Convert to avoid recursion
}
```

---

## Testing

### Test File Naming

- Test files end with `_test.go`
- Test functions start with `Test` followed by capitalized name
- Place tests in the same package or `_test` package

```go
// In math_test.go
func TestAdd(t *testing.T) {
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Add(2, 3) = %d; want 5", result)
    }
}
```

### Table-Driven Tests

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -1, -2},
        {"zero", 0, 0, 0},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            if got := Add(tt.a, tt.b); got != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, got, tt.expected)
            }
        })
    }
}
```

### Benchmarks

```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
```

---

## Validation Commands

```bash
# Format code
go fmt ./...
gofmt -s -w .

# Vet (static analysis)
go vet ./...

# Build
go build ./...

# Test
go test ./...
go test -v ./...
go test -race ./...

# Test coverage
go test -cover ./...
go test -coverprofile=coverage.out ./...

# Lint (with golangci-lint)
golangci-lint run

# Module management
go mod tidy
go mod verify
```

---

## Project Structure

```
project/
├── cmd/
│   └── myapp/
│       └── main.go         # Application entry point
├── internal/               # Private packages
│   ├── config/
│   └── service/
├── pkg/                    # Public packages
│   └── api/
├── go.mod
├── go.sum
└── README.md
```

---

## Common Patterns

### Constructor Functions

```go
// NewXxx convention for constructors
func NewServer(addr string, opts ...Option) *Server {
    s := &Server{addr: addr}
    for _, opt := range opts {
        opt(s)
    }
    return s
}
```

### Functional Options

```go
type Option func(*Server)

func WithTimeout(d time.Duration) Option {
    return func(s *Server) {
        s.timeout = d
    }
}

func WithLogger(l *log.Logger) Option {
    return func(s *Server) {
        s.logger = l
    }
}

// Usage
srv := NewServer(":8080", WithTimeout(30*time.Second), WithLogger(logger))
```

### Context Usage

```go
func DoSomething(ctx context.Context, arg string) error {
    // Check for cancellation
    select {
    case <-ctx.Done():
        return ctx.Err()
    default:
    }
    
    // Pass context to called functions
    return callOther(ctx, arg)
}
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Better Approach |
|--------------|-----------------|
| Ignoring errors with `_` | Always handle errors |
| Using `panic` for regular errors | Return errors |
| Getters named `GetXxx` | Name getter `Xxx` |
| Exposing package-level mutexes | Encapsulate synchronization |
| Passing context in struct fields | Pass context as first parameter |
| Using `init` for complex logic | Use explicit initialization |
| Naked `return` in long functions | Explicit return values |
| Channels for simple synchronization | Use `sync.Mutex` or `sync.WaitGroup` |

---

## Additional Resources

- [Effective Go](https://go.dev/doc/effective_go)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Go Proverbs](https://go-proverbs.github.io/)
- [Standard Library Documentation](https://pkg.go.dev/std)
- [The Go Programming Language Specification](https://go.dev/ref/spec)
