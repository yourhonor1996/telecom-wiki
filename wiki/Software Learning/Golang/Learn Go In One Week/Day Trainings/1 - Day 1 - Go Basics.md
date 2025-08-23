**Day 1 – Go Basics (Syntax & Tooling)**  
Your goal today is to _get comfortable writing and running Go code_, and _grasp how Go syntax differs from C++/Python_. We'll stay focused on fundamentals, not just syntax but _how Go is structured idiomatically._

---

## ✅ **Learning Objectives**

By the end of Day 1, you should be able to:

- Set up a Go environment on Windows
    
- Understand Go’s file structure, `package main`, and `func main()`
    
- Use `go run`, `go build`, and `go fmt`
    
- Declare variables, constants, and functions
    
- Use control structures: `if`, `for`, `switch`
    
- Understand slices and arrays (basic usage)
    
- Write a small CLI program
    

---

## 🔧 Step-by-Step Tasks

### 1. **Set Up the Environment**

- Install Go from [https://go.dev/dl](https://go.dev/dl)
    
- Add Go to your Windows `PATH`
    
- Run in terminal:
    
    ```bash
    go version
    go env
    ```
    

### 2. **Your First Go Program**

Mechanism: Go starts from `func main()` in the `package main`. Each `.go` file is part of a package.

📂 Create a folder `day1-basics/` and a file `main.go`:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

Now run:

```bash
go run main.go
```

Then:

```bash
go build
.\day1-basics.exe
```

---

### 3. **Variables & Constants**

Mechanism: Go has _explicit_ typing, but supports type inference with `:=`. Constants use `const`.

Try this in `main.go`:

```go
func main() {
    var x int = 42
    y := "hello"
    const pi = 3.14
    fmt.Println(x, y, pi)
}
```

📌 **Task:**  
Print:

- a boolean
    
- a float32
    
- the type of a variable using `fmt.Printf("%T\n", ...)`
    

---

### 4. **Functions**

Mechanism: Go functions _must declare return types_. Multiple return values are common.

Example:

```go
func add(a int, b int) int {
    return a + b
}
```

📌 **Task:**  
Write a function that returns both sum and difference of two integers:

```go
func sumAndDiff(a int, b int) (int, int) {
    // ...
}
```

**Answer**
```go
func sumAndDiff(a int, b int) (int, int) {  
    return a + b, b - a  
}
```

---

### 5. **Control Structures**

Go doesn’t have `while`—you use `for`.

Examples:

```go
for i := 0; i < 5; i++ { fmt.Println(i) }

if x := 10; x > 5 { fmt.Println("big") }

switch day := "Tue"; day {
case "Mon": fmt.Println("Monday")
case "Tue": fmt.Println("Tuesday")
default: fmt.Println("Other")
}
```

📌 **Task:**

- Loop from 1 to 10, printing only even numbers.
**Answer**
```go
for i := 0; i < 10; i++ {  
    if i%2 == 0 {  
       fmt.Println(i, " is even")  
    }  
}
```
- Use a `switch` to print whether a number (from `math/rand`) is <10, =10, or >10.

**Answer:**
```go
randomNumber := rand.Intn(20)  
switch {  
case randomNumber == 10:  
    fmt.Println("Number is equal to 10", randomNumber)  
case randomNumber < 10:  
    fmt.Println("Number is lesser than 10", randomNumber)  
case randomNumber > 10:  
    fmt.Println("Number is bigger than 10", randomNumber)  
}
```

---

### 6. **Arrays & Slices**

Mechanism:

- Arrays: `[3]int{1,2,3}`
    
- Slices: more flexible, like Python lists
    

```go
s := []int{1, 2, 3}
s = append(s, 4)
```

📌 **Task:**

- Create a slice with 3 strings, append a 4th.
**Answer**
```go
b2 := []int{3, 4, 5}  
b2 = append(b2, 34)  
fmt.Println(b2)
```
- Print length and capacity with `len()` and `cap()`.
**Answer**
```go
b2 := []int{3, 4, 5, 4, 4, 6}  
b2 = append(b2, 34)  
fmt.Println(b2)  
fmt.Println("Array length is: ", len(b2))  
fmt.Println("Array capacity is: ", cap(b2))  
```    

---

### 7. **Mini Challenge – CLI Adder**

📌 Write a CLI app that:

- Takes two numbers from `os.Args`
    
- Parses them with `strconv.Atoi`
    
- Prints their sum
    

Expected run:

```bash
go run main.go 3 5
# Output: Sum is 8
```

**Answer**
```go
args := os.Args[1:]  
if len(args) < 2 {  
    fmt.Println(message)  
}  
  
sum := 0  
for _, number := range args {  
    converted, er := strconv.Atoi(number)  
    if er == nil {  
       sum += converted  
    } else {  
       fmt.Println("The argument you entered (", number, ") cannot be converted to a number!")  
       return  
    }  
}  
fmt.Println(sum)
```


---

## 🚀 Stretch Goals (If Time Allows)

- Read about `go mod init` and try creating a module
    
- Explore `go fmt` and auto-format your code
    
- Try using `defer` and `panic/recover` briefly
    

---

Let me know when you’re done with each section or need a hint. Ready? Start with **Step 1: Setup + Hello Go** and tell me once it's working.

[^1]: something interesting 
