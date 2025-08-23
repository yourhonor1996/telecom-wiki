### 🎯 **Learning Objectives**

Today, you will:

1. Grasp **idiomatic Go** patterns ("The Go way").
    
2. Master key parts of the **standard library**: `sort`, `strings`, `io`, `time`, `context`, `errors`, etc.
    
3. Learn to read and follow **Go doc conventions** and common usage patterns.
    

---

### ✅ **Today's Roadmap**

#### **Section 1 – Idiomatic Go (1 hour)**

🧠 **Concept**:  
Go favors _simplicity_, _explicitness_, and _composition over inheritance_. Common idioms include:

- Prefer **composition over inheritance**.
    
- Return **multiple values** instead of exceptions.
    
- Use **short variable names** in small scopes (`i`, `v`, `ok`).
    
- Use `ok` idiom for map lookups or type assertions.
    
- Avoid getters/setters — **export fields** unless encapsulation is really needed.
    
- Prefer **for + range** over traditional C-style loops.
    
- Use `defer` to close resources **immediately after opening**.
    

---

🔧 **Task 1: Read & Rewrite Idioms**

You're given some unidiomatic Go. Rewrite it the Go way:

```go
type Circle struct {
	radius float64
}

func (c Circle) getArea() float64 {
	return 3.14 * c.radius * c.radius
}

func main() {
	circle := Circle{}
	circle.radius = 10.5
	area := circle.getArea()
	fmt.Println("Area is:", area)
}
```

➡️ Your job: Rewrite this code _idiomatically_ — remove unidiomatic naming and structure.

**Answer**
```go
package main  
  
import (  
    "fmt"  
)  
  
type Circle struct {  
    Radius float64  
}  
  
func (c Circle) Area() float64 {  
    return 3.14 * c.Radius * c.Radius  
}  
  
func main() {  
    circle := Circle{10.5}  
    fmt.Println("Area is:", circle.Area())  
}
```

---

#### **Section 2 – Standard Library Exploration (2.5 hours)**

You're going to build **small examples** using key stdlib packages.

---

🔧 **Task 2: String Processing with `strings`**

- Read user input.
    
- Convert it to lowercase.
    
- Count how many times the word `"go"` appears.
    
- Print the result.
    

Use `strings.ToLower`, `strings.Count`.

**Answer**
```go
package main  
  
import (  
    "bufio"  
    "fmt"    "os"    "strings")  
  
func main() {  
    reader := bufio.NewReader(os.Stdin)  
    fmt.Println("Enter a full sentence: ")  
    input, _ := reader.ReadString('\n')  
    input = strings.TrimSpace(input)  
  
    input = strings.ToLower(input)  
    fmt.Println(strings.Count(input, "go"))  
  
}
```

---

🔧 **Task 3: Sorting with `sort`**

- Make a slice of strings.
    
- Sort them in **reverse alphabetical order**.
    
- Print before and after.
    

Use `sort.Slice`.


**Answer**
```go
import (  
    "fmt"  
    "sort")  
 //this is a sort for a slice of ints  
func main() {  
    x := []int{1, 4, 5, -3, 56}  
    sort.Ints(x)  
    fmt.Println(x)  
}
```

```go
package main  
  
import (  
    "fmt"  
    "sort")  
  
func main() {  
    words := []string{"a", "b", "c", "d", "e", "f", "g"}  
    sort.Slice(words, func(i, j int) bool { return words[i] > words[j] })  
    fmt.Println(words)  
}
```

---

🔧 **Task 4: Timing with `time`**

- Print the **current local time**.
    
- Add **3 hours** to it.
    
- Use a `time.Timer` to wait **2 seconds**, then print `"Done!"`.

**Answer**
```go
package main  
  
import (  
    "fmt"  
    "time")  
  
func main() {  
    t := time.Now()  
    fmt.Println("Current time is: ", t)  
  
    timer := time.NewTimer(2 * time.Second)  
    <-timer.C  
      
    fmt.Println("Done!", " New Time: ", t.Add(3 * time.Hour))  
}
```
---

🔧 **Task 5: File Reading with `os` and `io`**

- Write a file named `data.txt` with some content (manually or from Go).
    
- Write code to open and print its contents.
    

Use `os.Open`, `io.ReadAll`, `defer file.Close()`.

```go
package main  
  
import (  
    "bufio"  
    "fmt"    "os"    "strings")  
  
func main() {  
    fileName := "D:\\repos\\day1\\someFile.txt"  
    file, er := os.OpenFile(fileName, os.O_CREATE|os.O_RDWR, 0644)  
    if er != nil {  
       fmt.Println("Error opening file !")  
       return  
    }  
  
    s, _ := file.Stat()  
    fmt.Println("Stat: ", s)  
  
    message := "This is a new file !\n"  
  
    for i := 0; i < 100; i++ {  
       _, _ = file.WriteString(message)  
    }  
  
    if er := file.Close(); er != nil {  
       fmt.Println("Couldn't close file !")  
       return  
    }  
    //====================================================  
  
    {  
       file, er := os.OpenFile(fileName, os.O_RDWR, 0644)  
       defer func() {  
          file.Close()  
          fmt.Println("Closed file!")  
       }()  
       if er != nil {  
          fmt.Println("Error opening file !")  
          return  
       }  
       r := bufio.NewReader(file)  
       counter := 0  
       for {  
          s, e := r.ReadString('\n')  
          if e != nil {  
             fmt.Println("Finished file!, error:", e.Error())  
             break  
          }  
          fmt.Println(strings.TrimSpace(s))  
          counter++  
       }  
       fmt.Printf("Number of Lines Read: %d\n", counter)  
  
    }  
  
}
```

---

#### **Section 3 – Context & Errors (1.5 hours)**

🧠 **Concepts**:

- `context.Background()` is the root.
    
- `WithCancel`, `WithTimeout`, `WithDeadline` add control.
    
- `errors.Is`, `errors.As`, `fmt.Errorf("...: %w", err)` for wrapping.
    

---

🔧 **Task 6: Use `context.WithTimeout`**

- Start a goroutine that runs a loop.
    
- Let it listen for a `context.Done()` signal.
    
- Cancel it after 2 seconds with a timeout context.
    

---

🔧 **Task 7: Use `fmt.Errorf` and `errors.Is`**

- Create a function `loadUser(id int) error`.
    
- If `id == 0`, return a wrapped error like: `fmt.Errorf("invalid ID: %w", ErrBadID)`
    
- Check with `errors.Is(err, ErrBadID)`.
    

---

### ⭐️ **Stretch Goals (Optional)**

1. Implement a CLI tool that accepts a filename and counts word frequency using `bufio.Scanner` and `map[string]int`.

**Answer**
```go
package main  
  
import (  
    "bufio"  
    "fmt"    
    "os"   
     "strings")  
  
func main() {  
    filename := os.Args[1]  
  
    file, e := os.OpenFile(filename, os.O_RDONLY, 0644)  
    defer file.Close()  
    if e != nil {  
       fmt.Println("Error opening file: ", e.Error())  
       return  
    }  
  
    reader := bufio.NewScanner(file)  
    m := map[string]int{}  
    for reader.Scan() {  
       for _, str := range strings.Split(reader.Text(), " ") {  
          m[str] += 1  
       }  
    }  
    fmt.Println(m)  
  
}
```

2. Implement a context-aware `HTTP GET` request using `net/http` + `context.WithTimeout`.
    

---

Report back after each task. Ask for help if you hit a wall — but **try before asking**.

Now start with **Task 1**. When done, paste your idiomatic version of the `Circle` example.