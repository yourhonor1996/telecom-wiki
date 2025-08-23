In Go, you can get user input using the `fmt` package — specifically `fmt.Scan`, `fmt.Scanf`, or `fmt.Scanln`. Here are the most common ways:

---

### 1. **Using `fmt.Scanln` (recommended for basic input):**

```go
package main

import (
	"fmt"
)

func main() {
	var name string
	fmt.Print("Enter your name: ")
	fmt.Scanln(&name)
	fmt.Println("Hello,", name)
}
```

- `Scanln` reads input until a newline (`\n`) is encountered.
    
- It automatically trims whitespace and stops at the first space.
    

---

### 2. **Using `fmt.Scan`:**

```go
package main

import (
	"fmt"
)

func main() {
	var name string
	fmt.Print("Enter your name: ")
	fmt.Scan(&name)
	fmt.Println("Hello,", name)
}
```

- `Scan` is similar to `Scanln`, but it stops reading at the first space or newline, which makes it less reliable for full-line inputs.
    

---

### 3. **Using `bufio.NewReader` for full lines (if you need spaces in input):**

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	reader := bufio.NewReader(os.Stdin)
	fmt.Print("Enter your full name: ")
	name, _ := reader.ReadString('\n')
	fmt.Println("Hello,", name)
}
```

- This allows reading entire lines, including spaces.
    
- `ReadString('\n')` reads input until the user presses Enter.
    

---

Let me know if you want to read multiple values at once, numbers, or parse complex input like JSON or structs.