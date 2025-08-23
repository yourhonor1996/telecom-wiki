# Usage In `done`, `quit` and `context.Context`
Great — let’s explore **how `chan struct{}` is used idiomatically** in Go under the names like `done`, `quit`, or even in conjunction with `context.Context`.

---

## ✅ 1. `done` / `quit` channels — graceful shutdown

The most classic idiom is using a `chan struct{}` as a **signal channel** to tell goroutines:  
👉 “It’s time to stop.”

### 🔁 Pattern:

```go
done := make(chan struct{})

go func() {
    for {
        select {
        case <-done:
            fmt.Println("stopping worker")
            return
        default:
            fmt.Println("working...")
            time.Sleep(500 * time.Millisecond)
        }
    }
}()

time.Sleep(2 * time.Second)
close(done) // send the stop signal (no value needed!)
```

✅ Works great when:

- You want to **exit a loop early**.
    
- You have multiple goroutines watching the same signal.
    

---

## ✅ 2. Fan-out stop signal — multiple goroutines, one stop

Let’s say you launch several workers, and want to stop them all at once:

```go
done := make(chan struct{})

for i := 0; i < 3; i++ {
    go func(id int) {
        for {
            select {
            case <-done:
                fmt.Printf("Worker %d stopping\n", id)
                return
            default:
                fmt.Printf("Worker %d working\n", id)
                time.Sleep(400 * time.Millisecond)
            }
        }
    }(i)
}

time.Sleep(2 * time.Second)
close(done)
```

Output:

```
Worker 0 working
Worker 1 working
Worker 2 working
...
Worker 2 stopping
Worker 0 stopping
Worker 1 stopping
```

✅ Efficient: All goroutines **listen to the same signal**, no data is passed.

---

## ✅ 3. With `select` and timeout

```go
done := make(chan struct{})

go func() {
    select {
    case <-done:
        fmt.Println("canceled")
    case <-time.After(3 * time.Second):
        fmt.Println("completed")
    }
}()

time.Sleep(1 * time.Second)
close(done)
```

🧠 This is useful if you want to **either cancel early or finish normally**, whichever comes first.

---

## ✅ 4. Replacing `chan struct{}` with `chan bool` ❌

You **could** do this:

```go
done := make(chan bool)
done <- true
```

But:

- You must **send a value** (`true`)
    
- If multiple goroutines read from it, **you must send multiple values**
    
- You can’t just **close it once and wake everyone** like with `chan struct{}`
    

**So:**

|`chan bool`|`chan struct{}`|
|---|---|
|must send a value|can just `close()`|
|uses more memory|zero size (efficient)|
|intent: “true/false”|intent: “stop signal”|

👉 Go style guides **strongly recommend** `chan struct{}` for signals.

---

## ✅ 5. `context.Context` uses this internally!

When you call:

```go
ctx, cancel := context.WithCancel(context.Background())

go func() {
    select {
    case <-ctx.Done(): // <-chan struct{}
        fmt.Println("context canceled")
    }
}()

cancel() // like close(done)
```

✅ `ctx.Done()` returns a **`<-chan struct{}`** — a read-only signal channel.  
✅ `cancel()` closes that channel to signal cancellation.

This is the **production-grade pattern** built into Go’s `context` package.

---

## ✅ Summary

|Use case|Code|What it means|
|---|---|---|
|Stop loop|`<-done` inside `select`|"Time to stop"|
|Shutdown|`close(quit)`|Signal to multiple goroutines|
|Timeout or done|`select { case <-done, ... }`|Wait for multiple outcomes|
|Context cancel|`<-ctx.Done()`|Built-in idiomatic cancel|

---

Let me know if you want a real-world **multi-component example** using `chan struct{}` or `context.Context` in a REST server, data pipeline, or similar.