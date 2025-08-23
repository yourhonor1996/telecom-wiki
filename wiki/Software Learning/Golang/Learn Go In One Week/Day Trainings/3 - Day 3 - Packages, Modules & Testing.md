**Great job finishing Day 2! 🔥 Day 3 is where Go starts to _scale_.**

---

## 📘 **Day 3 – Packages, Modules & Testing**

### ✅ **Objectives**

By the end of today, you’ll be able to:

- Structure Go code into packages and modules
    
- Use `go mod` for dependency management
    
- Write and run unit tests using the `testing` package
    
- Understand basic test coverage concepts
    

---

## 🧩 **Today's Sections**

---

### **Section 1 – Modules and Project Structure**

#### 🚧 Mechanism

Go uses **modules** (`go mod`) to manage dependencies and define project root. Modules make it easy to handle imports and version control, especially with external packages.

#### ✅ Task

1. Make a new folder `myapp`, open it in terminal.
    
2. Run:
    
    ```sh
    go mod init myapp
    ```
    
3. Create a file `main.go`:
    
    ```go
    package main
    
    import (
        "fmt"
        "myapp/mathops"
    )
    
    func main() {
        fmt.Println("3 + 5 =", mathops.Add(3, 5))
    }
    ```
    
4. Create a new folder `mathops/`, add `mathops.go`:
    
    ```go
    package mathops
    
    func Add(a, b int) int {
        return a + b
    }
    ```
    

🧪 Try running `go run .` from the `myapp` root.

---

### **Section 2 – Unit Testing with `testing` Package**

#### 🧠 Mechanism

Go uses the built-in `testing` package. You write tests in `*_test.go` files using functions like `func TestXxx(t *testing.T)`.

#### ✅ Task

1. Inside `mathops/`, create `mathops_test.go`:
    
    ```go
    package mathops
    
    import "testing"
    
    func TestAdd(t *testing.T) {
        got := Add(2, 3)
        want := 5
        if got != want {
            t.Errorf("Add(2,3) = %d; want %d", got, want)
        }
    }
    ```
    
2. Run:
    
    ```sh
    go test ./...
    ```
    

---

### **Section 3 – Table-Driven Tests (Go idiom)**

This is the Go way: using a slice of test cases and looping over them.

#### ✅ Task

1. Extend `mathops_test.go` with:
    
    ```go
    func TestAddTableDriven(t *testing.T) {
        tests := []struct {
            a, b, want int
        }{
            {1, 2, 3},
            {2, 2, 4},
            {10, 5, 15},
        }
    
        for _, tt := range tests {
            got := Add(tt.a, tt.b)
            if got != tt.want {
                t.Errorf("Add(%d,%d) = %d; want %d", tt.a, tt.b, got, tt.want)
            }
        }
    }
    ```
    

---

### 🧪 **Stretch Tasks (Optional)**

- Add a `Subtract` and `Multiply` function and test them.
    
- Try `go test -v` and `go test -cover`.
    
- Explore `go get github.com/stretchr/testify` and rewrite one test using `assert.Equal`.
    

---

Let me know when you’re done with Section 1 and I’ll greenlight Section 2. Let’s go!