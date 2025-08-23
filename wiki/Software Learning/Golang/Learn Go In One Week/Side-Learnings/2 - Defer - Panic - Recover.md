
## 🧠 `defer`, `panic`, `recover` in Go

### 🔹 `defer`

Mechanism: `defer` schedules a function to run _after the current function completes_, **even if it panics**.

It’s most often used to:

- Close files
    
- Unlock mutexes
    
- Recover from panics
    
- Log exit of a function
    

```go
func main() {
    defer fmt.Println("this runs last")
    fmt.Println("this runs first")
}
```

**Output:**

```
this runs first
this runs last
```

---

### 🔸 `panic`

Mechanism: `panic` stops normal execution of the current goroutine. It’s used for unrecoverable errors (e.g. nil pointer dereference, unexpected condition).

```go
func doSomething() {
    panic("something went wrong")
}
```

---

### 🔸 `recover`

Mechanism: `recover()` _catches_ a panic and resumes normal execution—**but only inside a deferred function**.

```go
func safe() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from panic:", r)
        }
    }()
    fmt.Println("About to panic")
    panic("boom!")
    fmt.Println("This will not run")
}
```

---

## ✅ Mini Task for You

Write a function `safeDivide(a, b int) int` that:

- Panics if `b == 0`
    
- Uses `defer` + `recover` to print a message and return -1 if a panic occurs
    

**Example run:**

```go
fmt.Println(safeDivide(10, 2)) // Output: 5
fmt.Println(safeDivide(10, 0)) // Output: Recovering from panic: division by zero
                               //         -1
```

==Answer==

```go
func safeDivide(a, b int) (result float32) {  
    defer func() {  
       if r := recover(); r != nil {  
          fmt.Println("Recovering from panic!")  
          result = -1  
       }  
    }()  
  
    if b == 0 {  
       fmt.Println("About to panic!")  
       panic("Boom!!")  
    }  
    return float32(a / b)  
}  
  
func main() {  
    fmt.Println(safeDivide(23, 2))  
    fmt.Println(safeDivide(23, 0))  
}
```

> [!IMPORTANT] Pay Attention
> Pay attention that in a defer function, returning something won't be compiled! You must name the return value of the function and the fill it with some value!

---

Let me know when you're done or want help structuring the `safeDivide` function.

## Isn't This Mechanism Like Try Catch In C++?
Yes, defer + recover + panic in Go serve a similar role to try/catch in C++, but with key differences in philosophy and usage.

### ✅ **Comparison: Go vs C++**

|Concept|Go|C++|
|---|---|---|
|**Try Block**|Code before `panic()`|`try { ... }`|
|**Throw**|`panic("message")`|`throw "message"`|
|**Catch**|`recover()` inside a `defer`|`catch (...)`|
|**Finally**|`defer`|`~Destructor()` or RAII|

---

### 🔥 Key Differences

1. **Go discourages using panic/recover for normal errors.**  
    In C++, throwing exceptions is a normal part of control flow.  
    In Go, **returning `error` values** is preferred. `panic/recover` is **only for truly unexpected situations**.
    
2. **No stack unwinding with resource cleanup**  
    Go doesn’t destruct like C++. You **must use `defer`** to clean up resources (close files, unlock mutexes, etc.).
    
3. **No catch blocks**  
    Go doesn’t have a `catch`. Instead, you must manually recover inside a deferred function. This makes exception-like logic more deliberate.
    

---

### ✅ Idiomatic Use in Go

- ❌ Not idiomatic:
    
    ```go
    if err != nil {
        panic(err)
    }
    ```
    
- ✅ Idiomatic:
    
    ```go
    if err != nil {
        return err
    }
    ```
    

---

### ✅ When to use `panic` in Go?

- Corrupted state
    
- Impossible code paths
    
- Init-time fatal config problems
    
- Debug assertions
    
