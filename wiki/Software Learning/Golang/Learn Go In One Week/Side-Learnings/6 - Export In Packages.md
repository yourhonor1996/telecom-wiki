In Go, to **export** a function (make it accessible from outside its package), you simply **capitalize the first letter** of its name.

---

### 🔧 Mechanism (How it works)

Go uses **capitalization as a visibility modifier**:

- **Capitalized names** (e.g. `PrintHello`) are **exported** (public).
    
- **Lowercase names** (e.g. `printHello`) are **unexported** (private to the package).
    

This applies to:

- Functions
    
- Types
    
- Variables
    
- Constants
    
- Struct fields
    

---

### ✅ Example

```go
// file: greeting/greet.go
package greeting

import "fmt"

// Exported function
func SayHello(name string) {
    fmt.Println("Hello", name)
}

// Unexported function
func sayBye(name string) {
    fmt.Println("Bye", name)
}
```

Usage from another package (e.g. `main`):

```go
package main

import "your_module_name/greeting"

func main() {
    greeting.SayHello("Mohammad") // ✅ Works
    // greeting.sayBye("Mohammad") // ❌ Compile error: sayBye is not exported
}
```

---

Let me know if you'd like to test this in a mini-project or want me to walk you through package imports with local files.