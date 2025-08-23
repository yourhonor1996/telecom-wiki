Here’s a **1-week Go (Golang) learning syllabus** tailored for an experienced C++/Python senior software engineer. The focus is on **syntax mapping, concurrency, and idiomatic Go**, assuming ~4–5 focused hours/day.

---

### 🔧 **Day 1 – Go Basics (Syntax & Tooling)**

- **Install & Setup**: `go install`, `$GOPATH`, `go mod`.
    
- **Hello, World**. Understand `package`, `func main()`.
    
- **Data Types**: `int`, `float64`, `string`, `bool`.
    
- **Variables**: `var`, `:=`, constants.
    
- **Control Flow**: `if`, `for`, `switch`, `defer`.
    
- **Functions**: return values, named returns, variadic.
    
- **Tooling**: `go run`, `go build`, `go fmt`, `go vet`, `go test`.
    

**Tasks**:  
✅ Write a CLI calculator.  
✅ Format and build it.

---

### 📦 **Day 2 – Structs, Interfaces & Error Handling**

- **Structs**: definition, embedding (like inheritance).
    
- **Methods**: value vs pointer receivers.
    
- **Interfaces**: Duck typing, empty interface.
    
- **Errors**: `error` type, idiomatic handling, `errors.New`, `fmt.Errorf`.
    

**Tasks**:  
✅ Model a file system with `File`, `Folder`, interface `Node`.  
✅ Add error-checking on operations.

---

### 🕸 **Day 3 – Packages, Modules & Testing**

- **Packages**: structure, visibility (`Capitalized` = public).
    
- **Modules**: `go mod init`, imports.
    
- **Testing**: `testing` package, table-driven tests, benchmarks.
    

**Tasks**:  
✅ Split previous code into packages.  
✅ Write unit tests with table-driven style.

---

### 🔄 **Day 4 – Concurrency (Go’s Superpower)**

- **Goroutines**: `go func()`.
    
- **Channels**: unbuffered, buffered, `select`.
    
- **sync package**: `WaitGroup`, `Mutex`.
    
- **Context**: cancellation, timeouts.
    

**Tasks**:  
✅ Build a parallel URL fetcher.  
✅ Add timeout via `context.Context`.

---

### 📁 **Day 5 – Go Idioms & Standard Library**

- **Idioms**: error-first return, zero values, short declarations, `defer close`.
    
- **Common packages**: `os`, `io`, `fmt`, `net/http`, `encoding/json`, `bufio`.
    
- **Slicing & maps**: usage, initialization, range.
    

**Tasks**:  
✅ Build a JSON REST API client.  
✅ Read from file, process line-by-line.

---

### 🌐 **Day 6 – Web Server & Middleware**

- **net/http**: `HandleFunc`, `ServeMux`, `ListenAndServe`.
    
- **Middleware pattern**.
    
- **Serve JSON**: `json.Marshal`, set headers.
    
- **Router**: use `gorilla/mux` or `chi`.
    

**Tasks**:  
✅ Build a small REST API with routes and middleware (e.g. logger).

---

### 🚀 **Day 7 – Project Day & Deployment**

- **Pick a project**: REST API, CLI tool, scraper.
    
- **Dockerize it**: minimal Dockerfile.
    
- **Build & deploy**: run it locally, try basic CI (e.g. GitHub Actions).
    

**Tasks**:  
✅ Complete project.  
✅ Push to GitHub with `go.mod`, Dockerfile.

---

Let me know if you want a _Go ⇄ C++/Python syntax cheatsheet_ or project suggestions.