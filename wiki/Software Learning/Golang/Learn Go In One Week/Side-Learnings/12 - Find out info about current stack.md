In Go a _goroutine_ is deliberately anonymous: the runtime does **not** expose a stable public “goroutine ID” or any other mutable metadata you can query at will.  
That philosophy keeps code portable across schedulers and OS threads. When you need to tag work, the idiomatic way is to pass your own identifier through a `context.Context`, not to ask the runtime _“who am I?”_.

That said, you can still inspect **where the current goroutine is executing, what its stack looks like, and what all the other goroutines are doing**—just not its official ID. Below are the practical tools you have inside the standard library, plus the (discouraged) hacks people use when they really want an ID.

---

### 1. Cheap call-site info with `runtime.Caller` / `runtime.Callers`

```go
// Which file / line / function called me?
func whereAmI(skip int) string {
    pc, file, line, ok := runtime.Caller(skip)
    if !ok {
        return "unknown"
    }
    fn := runtime.FuncForPC(pc)
    return fmt.Sprintf("%s:%d %s", file, line, fn.Name())
}
```

`runtime.Caller` gives you the file, line, and program-counter of one frame; `runtime.Callers` + `runtime.CallersFrames` lets you walk the whole stack and reconstruct a back-trace yourself. Both work only for the **current** goroutine.

---

### 2. Grab an on-the-fly stack trace

```go
buf := make([]byte, 1<<14)          // 16 KiB is enough for most traces
n := runtime.Stack(buf, false)      // false ⇒ *just* this goroutine
fmt.Print(string(buf[:n]))
```

`runtime.Stack` formats the goroutine’s header line (“goroutine 123 [running]:”) followed by the PC/line pairs down the stack ([pkg.go.dev](https://pkg.go.dev/runtime "runtime package - runtime - Go Packages")).  
A convenience wrapper `debug.Stack()` does the same thing with an internal, auto-sized buffer ([pkg.go.dev](https://pkg.go.dev/runtime/debug "debug package - runtime/debug - Go Packages")).

If you need _all_ goroutines, pass `true` to `runtime.Stack`, or use the higher-level:

```go
pprof.Lookup("goroutine").WriteTo(w, 2) // 0=plain, 1=hex, 2=with states
```

This is the same dump the runtime prints when your program gets `SIGQUIT`.

---

### 3. Programmatic profile of every goroutine

```go
records := make([]runtime.StackRecord, runtime.NumGoroutine())
n, _ := runtime.GoroutineProfile(records) // fill slice up to n
records = records[:n]
```

Each `StackRecord` holds the PCs for one goroutine; you can turn those into function / file / line with `runtime.CallersFrames` just as above ([pkg.go.dev](https://pkg.go.dev/runtime?utm_source=chatgpt.com "runtime - Go Packages")).  
You still don’t get an ID, only the stack.

---

### 4. Counting goroutines

```go
fmt.Println("alive:", runtime.NumGoroutine())
```

Good for health checks or sanity assertions.

---

### 5. If you _really_ think you need the goroutine ID

Go’s maintainers have resisted adding an API for this for over a decade (see dozens of issues on the tracker).  
Two common **debug-only** tricks exist, both relying on undocumented internals that _may break on any release_:

1. **Parse `runtime.Stack`’s first line.**
    
    ```go
    func curGID() uint64 {
        b := make([]byte, 64)
        n := runtime.Stack(b, false)
        var id uint64
        fmt.Sscanf(string(b[:n]), "goroutine %d ", &id)
        return id
    }
    ```
    
2. **Use `go:linkname` or C to reach into the runtime’s `g` struct** (packages such as `github.com/petermattis/goid` do this).  
    This ties your code to the exact Go version and is unsafe in production.
    

Use either only for local logging or diagnostics; never build core logic on top of them.

---

### 6. Recommended pattern: carry your own label

```go
func worker(ctx context.Context) {
    traceID := ctx.Value("traceID").(string)
    log := slog.With("trace_id", traceID)

    // ...
}

ctx := context.WithValue(context.Background(), "traceID", uuid.NewString())
go worker(ctx)
```

You get a stable identifier that survives across goroutines you spawn, works with structured logging, and doesn’t depend on runtime internals.

---

## TL;DR

- No public goroutine ID or mutable metadata is available.
    
- You **can** inspect the current goroutine’s _call stack_ (`runtime.Caller`, `runtime.Stack`, `debug.Stack`) and get profiles of **all** goroutines (`runtime.GoroutineProfile`, `pprof`).
    
- If you only want unique tags in logs, pass your own identifier through `context.Context` instead of poking the runtime.