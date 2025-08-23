### 1 Goroutines — lightweight threads

A **goroutine** is Go’s ultra-cheap unit of concurrent execution.  
You start one with the keyword `go` in front of a function call:

```go
func say(msg string) {
    fmt.Println(msg)
}

func main() {
    go say("concurrent")   // runs in its own goroutine
    say("sequential")      // runs in the main goroutine
}
```

_Key points_

|What|Why it matters|
|---|---|
|**Stacks grow & shrink**|Each goroutine starts with ~2 kB and grows as needed, so you can spawn thousands.|
|**Same address space**|No copy of globals; goroutines communicate through shared memory or, idiomatically, through channels.|
|**Scheduler**|Go’s runtime multiplexes goroutines onto a small pool of OS threads—no manual tuning needed.|

---

### 2 Channels — typed conduits for communication

A **channel** is a typed pipe that lets one goroutine send values to another safely:

```go
c := make(chan int)   // unbuffered channel of ints

go func() {
    c <- 42           // send
}()

v := <-c              // receive; blocks until a value arrives
fmt.Println(v)        // 42
```

_Features you’ll meet often_

- **Unbuffered** (`make(chan T)`) – send blocks until a receiver is ready and vice-versa.
    
- **Buffered** (`make(chan T, n)`) – up to _n_ values can sit in the buffer before a send blocks.
    
- **Directional types** (`chan<- T`, `<-chan T`) – limit a parameter to send-only or receive-only.
    

---

### 3 `select` — wait on many channel ops at once

`select` lets a goroutine sleep until one of several communications is ready:

```go
select {
case v := <-c1:
    fmt.Println("got", v, "from c1")
case c2 <- v2:
    fmt.Println("sent", v2, "to c2")
case <-time.After(500 * time.Millisecond):
    fmt.Println("timeout")
}
```

_Rules of the game_

- If more than one case can proceed, **one is chosen pseudorandomly**.
    
- A `default` clause makes the whole statement non-blocking.
    
- `select {}` with no cases is an elegant **permanent sleep** (used to keep `main` alive).
    

---

### 4 Putting them together

Below is a tiny _fan-in worker pool_ that illustrates the trio working in concert:

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {                // receive jobs until channel is closed
        time.Sleep(time.Millisecond * time.Duration(rand.Intn(250)))
        results <- id * j               // send back a result
    }
}

func main() {
    rand.Seed(time.Now().UnixNano())

    const nWorkers = 3
    jobs    := make(chan int, 5)        // buffered so dispatching is non-blocking
    results := make(chan int)

    // spin up workers
    for w := 1; w <= nWorkers; w++ {
        go worker(w, jobs, results)     // goroutines
    }

    // send jobs
    for j := 1; j <= 10; j++ {
        jobs <- j
    }
    close(jobs)                         // no more jobs

    timeout := time.After(2 * time.Second)

    // gather results, but bail if it’s taking too long
    for received := 0; received < 10; {
        select {                        // select coordinates multiple channels
        case r := <-results:
            fmt.Println("result:", r)
            received++
        case <-timeout:
            fmt.Println("aborted – too slow")
            return
        }
    }
    fmt.Println("all done")
}
```

**What you just saw**

|Concept|Where it shows up|Why it’s useful|
|---|---|---|
|_Goroutines_|`go worker(...)`|3 workers do CPU-bound or I/O work in parallel.|
|_Channels_|`jobs`, `results`|Provide safe queueing without explicit locks.|
|_select_|Result-gathering loop|Multiplexes data channel and a timeout channel to avoid dead-waits.|

---

### 5 Mental model cheat-sheet

```
      goroutine A                 goroutine B
          |                            |
          |   send / receive           |
          + ----- channel -------------+
          |                            |
   select { … }            (maybe another select)
          |
       reacts to whichever
       communication is ready
```

1. **Start work** in goroutines.
    
2. **Synchronize** through typed channels, not mutable shared state.
    
3. **Coordinate** multiple conversations (or timeouts) with `select`.
    

With those three primitives, Go gives you a powerful but minimal toolbox for building safe, readable concurrent programs.

## Examples Simple To Complex
Here’s a **progressive set of Go concurrency examples** using goroutines, channels, and `select` — from very **simple** to **intermediate/complex** — so you can practice and master them step by step.

## 🟢 Level 1: Very Simple — Goroutine Basics

### 1.1. Single Goroutine

```go
package main

import "fmt"

func sayHello() {
    fmt.Println("Hello from goroutine!")
}

func main() {
    go sayHello()
    fmt.Println("Main finished")
}
```

⚠️ This might not print `"Hello from goroutine!"` because `main` exits too soon. That’s the first lesson: goroutines don’t block the main thread.

---

### 1.2. Wait for Goroutine Using `time.Sleep`

```go
package main

import (
    "fmt"
    "time"
)

func sayHello() {
    fmt.Println("Hello from goroutine!")
}

func main() {
    go sayHello()
    time.Sleep(1 * time.Second) // gives goroutine time to finish
    fmt.Println("Main finished")
}
```

---

## 🟡 Level 2: Channels for Communication

### 2.1. Simple Send/Receive

```go
package main

import "fmt"

func main() {
    ch := make(chan string)

    go func() {
        ch <- "Hello from goroutine"
    }()

    msg := <-ch
    fmt.Println("Received:", msg)
}
```

---

### 2.2. Buffered Channels

```go
package main

import "fmt"

func main() {
    ch := make(chan int, 2) // buffer size 2

    ch <- 1
    ch <- 2
    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```

---

### 2.3. Directional Channels

```go
package main

import "fmt"

func sendOnly(ch chan<- int) {
    ch <- 42
}

func receiveOnly(ch <-chan int) {
    fmt.Println(<-ch)
}

func main() {
    ch := make(chan int)
    go sendOnly(ch)
    receiveOnly(ch)
}
```

---

## 🟠 Level 3: Using `select`

### 3.1. Two Goroutines Competing

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        ch1 <- "from ch1"
    }()

    go func() {
        time.Sleep(2 * time.Second)
        ch2 <- "from ch2"
    }()

    select {
    case msg1 := <-ch1:
        fmt.Println("Received:", msg1)
    case msg2 := <-ch2:
        fmt.Println("Received:", msg2)
    }
}
```

Try changing the delays to see which one wins.

---

### 3.2. `select` with `default`

```go
package main

import "fmt"

func main() {
    ch := make(chan int)

    select {
    case val := <-ch:
        fmt.Println("Received:", val)
    default:
        fmt.Println("No value ready")
    }
}
```

---

### 3.3. Timeout with `time.After`

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ch := make(chan string)

    go func() {
        time.Sleep(3 * time.Second)
        ch <- "Done"
    }()

    select {
    case msg := <-ch:
        fmt.Println("Received:", msg)
    case <-time.After(2 * time.Second):
        fmt.Println("Timeout!")
    }
}
```

---

## 🔴 Level 4: Fan-Out and Worker Pool

### 4.1. Fan-Out with 3 Workers

```go
package main

import (
    "fmt"
    "time"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {
        fmt.Printf("Worker %d started job %d\n", id, j)
        time.Sleep(500 * time.Millisecond)
        fmt.Printf("Worker %d finished job %d\n", id, j)
        results <- j * 2
    }
}

func main() {
    jobs := make(chan int, 5)
    results := make(chan int, 5)

    for i := 1; i <= 3; i++ {
        go worker(i, jobs, results)
    }

    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)

    for a := 1; a <= 5; a++ {
        fmt.Println("Result:", <-results)
    }
}
```


> [!IMPORTANT] Take Notice!
> It is important which operations are blocking and which ones are non-blocking. It is better explained here: 

```go
func worker(id int, jobs <-chan int, results chan<- int) {
    // 1️⃣ ——— BLOCKING RECEIVE
    for j := range jobs {            // <- blocks until (a) a value is ready or (b) channel closes
        fmt.Printf("Worker %d started job %d\n", id, j)

        // 2️⃣ ——— SLEEP (blocks this goroutine only)
        time.Sleep(500 * time.Millisecond)

        fmt.Printf("Worker %d finished job %d\n", id, j)

        // 3️⃣ ——— POSSIBLY-BLOCKING SEND
        results <- j * 2             // <- blocks if results' buffer is full
    }                                // loop exits when jobs channel is closed *and* drained
}

func main() {
    // Channel creation never blocks
    jobs    := make(chan int, 5)     // buffered to 5
    results := make(chan int, 5)     // buffered to 5

    // 4️⃣ ——— NON-BLOCKING: starts three new goroutines instantly
    for i := 1; i <= 3; i++ {
        go worker(i, jobs, results)
    }

    // 5️⃣ ——— POSSIBLY-BLOCKING SENDS
    for j := 1; j <= 5; j++ {
        jobs <- j                    // each send blocks only
                                     //   when the 5-slot buffer is full
                                     //   *and* no worker has freed a slot yet
    }

    // 6️⃣ ——— NON-BLOCKING CLOSE
    close(jobs)                      // close never blocks

    // 7️⃣ ——— BLOCKING RECEIVES
    for a := 1; a <= 5; a++ {
        fmt.Println("Result:", <-results)  // <- blocks until a value is ready
    }
}

```

|#|Statement|Why it can block|When it _won’t_ block in this program|
|---|---|---|---|
|1|`j := range jobs`|Waits for the next value (or channel close after buffer empty).|While there’s still at least one pending job or the channel is already closed _and_ drained.|
|2|`time.Sleep(…)`|Puts **this** goroutine to sleep; the others keep running.|Never skips the pause; it always blocks for 0.5 s.|
|3|`results <- …`|Send blocks when the `results` buffer is full.|The buffer holds five results; as soon as `main` consumes even one, space opens and the send proceeds.|
|4|`go worker(…)`|Creating a goroutine is asynchronous; it returns immediately.|Always non-blocking.|
|5|`jobs <- j`|Send blocks when the `jobs` buffer is full.|The buffer size is 5 and we send exactly 5 jobs, so if workers haven’t dequeued anything yet, the **last** send would block— but in practice the first worker usually consumes early, preventing blockage.|
|6|`close(jobs)`|`close` itself never blocks; it just marks the channel done.|Always non-blocking.|
|7|`<-results`|Receive blocks until there is _some_ result in the buffer.|Each job always yields exactly one result, so eventually all five receives succeed; they just wait if workers aren’t done yet.|

---

### 4.2. Graceful Cancellation with `select` and `done`

```go
package main

import (
    "fmt"
    "time"
)

func worker(stop <-chan struct{}) {
    for {
        select {
        case <-stop:
            fmt.Println("Worker stopped")
            return
        default:
            fmt.Println("Working...")
            time.Sleep(500 * time.Millisecond)
        }
    }
}

func main() {
    stop := make(chan struct{})
    go worker(stop)

    time.Sleep(2 * time.Second)
    close(stop) // signal the worker to stop
    time.Sleep(1 * time.Second)
}
```

---

## 🧠 Next Practice Ideas:

- Implement a **rate limiter** using goroutines and `time.Ticker`.
    
- Create a **ping-pong** program where two goroutines send messages back and forth.
    
- Simulate a **concurrent bank account** with locking or channels.
    

---

Would you like a **GitHub repo-style project** where you go from beginner to advanced with unit tests and exercises? I can make that too.