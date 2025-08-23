**Day 4 – Concurrency in Go**

---

### 🚀 Learning Objectives

By the end of today, you will:

- Understand how Go handles lightweight concurrency using **goroutines**.
    
- Coordinate concurrent execution using **channels**.
    
- Manage cancellation and timeouts using the **`context`** package.
    
- Learn Go’s memory-sharing-by-communication model.
    

---

### 🧠 Key Concepts Before Coding

#### 1. **Goroutines**

A **goroutine** is a lightweight thread managed by the Go runtime. You spawn one with the `go` keyword:

```go
go doSomething()
```

They run concurrently, not in parallel unless your machine has multiple cores. They're _non-blocking_.

#### 2. **Channels**

Channels let goroutines communicate. Think of them as **typed pipes**:

```go
ch := make(chan int)
ch <- 42       // Send
x := <-ch      // Receive
```

Unbuffered channels block until both sender and receiver are ready. Buffered channels allow fixed-size storage.

#### 3. **Select**

A `select` statement waits on multiple channel operations and runs the one that’s ready first:

```go
select {
case msg := <-ch1:
    fmt.Println("Got", msg)
case ch2 <- 3:
    fmt.Println("Sent 3 to ch2")
default:
    fmt.Println("Nothing ready")
}
```

#### 4. **Context**

Go's `context` is the standard way to handle cancellation, deadlines, and timeouts in concurrent operations.

```go
ctx, cancel := context.WithCancel(context.Background())
go func() {
    select {
    case <-ctx.Done():
        fmt.Println("Canceled")
    }
}()
cancel() // triggers cancellation
```

---

### 📘 Tasks

#### ✅ Section 1: Goroutines and Blocking

1. Write a function `printNumbers()` that prints numbers 1–5 with a 500ms delay.
    
2. Call it in `main()` normally and then with a goroutine.
    
3. Observe the difference using `time.Sleep()` to keep main alive.
    

Expected output for goroutine version:

```
main done
1
2
3
...
```

**Answer**
```go
package main  
  
import (  
    "fmt"  
    "time")  
  
func printNumbers() {  
    for i := 1; i <= 5; i++ {  
       fmt.Println(i)  
       time.Sleep(500 * time.Millisecond)  
  
    }  
}  
  
func main() {  
    go printNumbers()  
    printNumbers()  
  
}
```
#### ✅ Section 2: Channels

1. Create a function `squares(in <-chan int, out chan<- int)` that reads numbers and sends their squares.
    
2. In `main`, pipe values from one channel to another and print them.
    
3. Extend it to use a buffered channel of size 3. Observe how it changes execution.
    
**Answer**
```go
package main  
  
import "fmt"  
  
func squares(in <-chan int, out chan<- int) {  
    incomingNumber := <-in  
    out <- incomingNumber * incomingNumber  
}  
  
func main() {  
    ch1 := make(chan int, 1)  
    ch2 := make(chan int, 1)  
    for i := 0; i < 10; i++ {  
       ch1 <- i  
       squares(ch1, ch2)  
       fmt.Println(<-ch2)  
    }  
}
```
#### ✅ Section 3: `select` with Multiple Channels

1. Create two channels `ch1`, `ch2`.
    
2. Spawn two goroutines that send a value to each channel after a random delay.
    
3. Use `select` to print whichever message comes first.
    

Expected:

```
Received from ch1: Hello
```

or

```
Received from ch2: World
```


**Answer**
```go
package main  
  
import (  
    "fmt"  
    "math/rand"    "time")  
  
func sendMessageAtRandomTime(stopSignal <-chan struct{}, ch chan<- string) {  
    for {  
       select {  
       case <-stopSignal:  
          fmt.Println("Stop signal was received")  
          return  
       default:  
          waitTime := rand.Intn(5)  
          number := rand.Intn(100)  
          time.Sleep(time.Duration(waitTime) * time.Second)  
          ch <- fmt.Sprintf("Slept for %d seconds and sent %d", waitTime, number)  
       }  
    }  
}  
  
func receiveFromChannels(stopSignal <-chan struct{}, ch1 <-chan string, ch2 <-chan string) {  
    for {  
       select {  
       case ch1Message := <-ch1:  
          fmt.Println("Got message from ch1: ", ch1Message)  
       case ch2Message := <-ch2:  
          fmt.Println("Got message from ch2: ", ch2Message)  
       case <-stopSignal:  
          fmt.Println("Stopping receiving from channels!")  
          return  
  
       }  
    }  
}  
  
func main() {  
    ch1 := make(chan string, 1)  
    ch2 := make(chan string, 1)  
    signalChannel := make(chan struct{}, 1)  
  
    go sendMessageAtRandomTime(signalChannel, ch1)  
    go sendMessageAtRandomTime(signalChannel, ch2)  
  
    go receiveFromChannels(signalChannel, ch1, ch2)  
  
    time.Sleep(10 * time.Second)  
    close(signalChannel)  
    time.Sleep(3 * time.Second)  
}
```


#### ✅ Section 4: Cancellation with `context`

1. Write a function `watch(ctx)` that loops and prints “Working...” every 200ms until `ctx.Done()`.

**Answer**

```go
package main  
  
import (  
    "context"  
    "fmt"    "time")  
  
func watch(ctx context.Context) {  
    for {  
       select {  
       case <-ctx.Done():  
          fmt.Println("Done working!")  
          return  
       default:  
          time.Sleep(500 * time.Millisecond)  
          fmt.Println("Working...")  
       }  
    }  
}  
  
func main() {  
  
    ctx, cancelFunction := context.WithCancel(context.Background())  
    go watch(ctx)  
    time.Sleep(5 * time.Second)  
    fmt.Println("Cancelling ..")  
    cancelFunction()  
    time.Sleep(1 * time.Second)  
  
}
```

2. In main, cancel the context after 1 second using `context.WithTimeout`.
    

**Answer**
```go
package main  
  
import (  
    "context"  
    "fmt"    "runtime"    "time")  
  
func watch(ctx context.Context) {  
    for {  
       select {  
       case <-ctx.Done():  
  
          fmt.Println("Done working!")  
          return  
       default:  
          b := make([]byte, 64)  
          n := runtime.Stack(b, false)  
          var id uint64  
          stringed := string(b[:n])  
          fmt.Sscanf(stringed, "goroutine %d", &id)  
          time.Sleep(500 * time.Millisecond)  
          fmt.Println("Working...")  
       }  
    }  
}  
  
func main() {  
  
    ctx, cancelFunction := context.WithTimeout(context.Background(), 5*time.Second)  
    go watch(ctx)  
    defer func() {  
       fmt.Println("Timeout called. Cancelling ...")  
       cancelFunction()  
    }()  
    for {  
       select {  
       case <-ctx.Done():  
          fmt.Println("Finishing main thread because timeout was called...")  
          return  
       }  
    }  
}
```


---

### 💪 Stretch Goals (Optional)

#### Implement a **worker pool** with goroutines and channels.
**Answer**
```go 
//The Job file 
package job  
  
type GeneratorType = func() *Properties  
  
type Properties struct {  
    id             uint  
    processorName  string  
    processingTime uint  
    result         any  
}  
  
func (p *Properties) Id() uint {  
    return p.id  
}  
  
func (p *Properties) SetWorkerProperties(name string, processingTime uint) {  
    p.processorName = name  
    p.processingTime = processingTime  
}  
  
func (p *Properties) SetResult(result any) {  
    p.result = result  
}  
  
func Generator() GeneratorType {  
    var counter uint = 0  
  
    return func() *Properties {  
       counter++  
       return &Properties{counter, "", 0, nil}  
    }  
}
```

```go 
//the main file 
package main  
  
import (  
    "day1/job"  
    "fmt"    
    "math/rand"    
    "time")  
  
func worker(incoming <-chan job.Properties, outgoing chan<- job.Properties) {  
  
    for j := range incoming {  
       ptime := rand.Intn(3)  
       j.SetWorkerProperties(fmt.Sprintf("Worker for job %d", j.Id()), uint(ptime))  
       time.Sleep(time.Duration(ptime) * time.Second)  
       result := rand.Intn(500)  
       j.SetResult(result)  
  
       outgoing <- j  
    }  
}  
  
func main() {  
  
    const numberOfJobs int = 10  
    const numberOfWorkers int = 2  
  
    incomingJobs := make(chan job.Properties, numberOfJobs)  
    finishedJobs := make(chan job.Properties, numberOfJobs)  
    jg := job.Generator()  
  
    for i := 0; i < numberOfJobs; i++ {  
       incomingJobs <- *jg()  
    }  
  
    for i := 0; i < numberOfWorkers; i++ {  
       go worker(incomingJobs, finishedJobs)  
    }  
  
    close(incomingJobs)  
  
    for i := 0; i < numberOfJobs; i++ {  
       fmt.Printf("%+v\n", <-finishedJobs)  
    }  
}
```
#### Use `sync.WaitGroup` to wait for all goroutines to finish.

**Answer**

```go 
package main  
  
import (  
    "fmt"  
    "math/rand/v2"    "sync"    "time")  
  
func doWork(wg *sync.WaitGroup, id int) {  
    defer func() {  
       fmt.Printf("Goroutime %d done!\n", id)  
       wg.Done()  
  
    }()  
    randomTime := rand.IntN(10)  
    fmt.Printf("Started Goroutine %d Waiting for %d seconds...\n", id, randomTime)  
    time.Sleep(time.Duration(randomTime) * time.Second)  
}  
  
func main() {  
    waitGroup := sync.WaitGroup{}  
  
    for i := 0; i < 10; i++ {  
       waitGroup.Add(1)  
       go doWork(&waitGroup, i)  
    }  
    waitGroup.Wait()  
}
```
#### Chain multiple channel-processing functions (like a pipeline).
    

**Answer**

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func gen(ctx context.Context, wg *sync.WaitGroup) <-chan int {
	out := make(chan int)
	wg.Add(1)
	go func() {
		defer wg.Done()
		defer close(out)
		i := 1
		for {
			select {
			case <-ctx.Done():
				fmt.Println("gen: context cancelled")
				return
			case out <- i:
				i++
				time.Sleep(200 * time.Millisecond)
			}
		}
	}()
	return out
}

func square(ctx context.Context, in <-chan int, wg *sync.WaitGroup) <-chan int {
	out := make(chan int)
	wg.Add(1)
	go func() {
		defer wg.Done()
		defer close(out)
		for {
			select {
			case <-ctx.Done():
				fmt.Println("square: context cancelled")
				return
			case num, ok := <-in:
				if !ok {
					return
				}
				out <- num * num
			}
		}
	}()
	return out
}

func double(ctx context.Context, in <-chan int, wg *sync.WaitGroup) <-chan int {
	out := make(chan int)
	wg.Add(1)
	go func() {
		defer wg.Done()
		defer close(out)
		for {
			select {
			case <-ctx.Done():
				fmt.Println("double: context cancelled")
				return
			case num, ok := <-in:
				if !ok {
					return
				}
				out <- num * 2
			}
		}
	}()
	return out
}

func main() {
	var wg sync.WaitGroup
	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()

	numbers := gen(ctx, &wg)
	squares := square(ctx, numbers, &wg)
	doubles := double(ctx, squares, &wg)

	// Drain final output
	for v := range doubles {
		fmt.Println(v)
	}

	// Wait for all goroutines to finish
	wg.Wait()
	fmt.Println("Pipeline stopped and all goroutines completed")
}

```

