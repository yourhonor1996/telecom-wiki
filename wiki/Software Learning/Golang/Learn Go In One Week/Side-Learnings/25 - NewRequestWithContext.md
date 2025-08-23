In Go, `http.NewRequestWithContext` is a function provided by the `net/http` package that allows you to create an HTTP request with a **context** attached. This context can be used to cancel the request, enforce a timeout, or pass request-scoped values.

---

### 🔧 Function Signature

```go
func NewRequestWithContext(ctx context.Context, method, url string, body io.Reader) (*http.Request, error)
```

### 📥 Parameters

- `ctx`: A `context.Context` object that lets you cancel the request or apply timeouts/deadlines.
    
- `method`: HTTP method string like `"GET"`, `"POST"`, etc.
    
- `url`: URL string of the request.
    
- `body`: Optional request body (for POST, PUT, etc.), passed as an `io.Reader`. Can be `nil`.
    

### 📤 Returns

- A pointer to an `http.Request` with the context attached.
    
- An error if the request couldn't be created (e.g., due to a malformed URL).
    

---

### 🧠 Why use `NewRequestWithContext`?

It’s an enhanced version of `http.NewRequest` that supports context control.

Use it to:

- **Cancel** the request manually.
    
- **Set a timeout** for the request.
    
- **Pass metadata** via context (e.g., for tracing or logging).
    

---

### 🧪 Example: Cancel after timeout

```go
package main

import (
    "context"
    "net/http"
    "time"
    "log"
)

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()

    req, err := http.NewRequestWithContext(ctx, "GET", "https://example.com", nil)
    if err != nil {
        log.Fatal(err)
    }

    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        log.Println("Request failed:", err)
        return
    }
    defer resp.Body.Close()

    log.Println("Response status:", resp.Status)
}
```

In this example, if the request takes longer than 2 seconds, the context cancels it.

---

### 🧩 Related Concepts

- `context.WithCancel`, `context.WithTimeout`, `context.WithDeadline` — for creating contexts.
    
- `req.Context()` — gets the context from an `http.Request`.
    
- `http.NewRequest` — older version, doesn't support context.
    

---

Let me know if you want advanced usage examples or common mistakes to avoid.