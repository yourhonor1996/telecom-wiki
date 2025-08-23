🔥 **Welcome to Day 7: Final Project & Deployment (with Docker)** 🔥  
This is your _boss fight_. Today we’ll build and deploy a real Go web app using everything you’ve learned.

---

### ✅ **Today's Learning Objectives**

By the end of today, you will:

- Build a minimal Go web service with a RESTful API
    
- Structure a production-ready project layout
    
- Containerize the Go app using Docker
    
- Run the container locally and test it with `curl` or Postman
    
- (Stretch) Deploy it with Docker Hub and run it anywhere
    

---

### 🧱 Project: "Task Manager API"

You’ll build a minimal backend for a task manager where users can:

- Create a task
    
- View all tasks
    
- Mark a task as done
    

---

## 📦 **Section 1: Project Setup**

### 🧠 Mechanism

In Go, structuring your codebase into packages and organizing by functionality is critical. For production projects, we usually follow this layout:

```
taskmanager/
├── cmd/
│   └── server/       # main.go entry point
├── internal/
│   ├── task/         # business logic
│   └── http/         # HTTP handlers
├── go.mod
└── Dockerfile
```

This separates `main()` from your actual logic (making it more testable and clean).

---

### 🧪 Task

1. **Create folder structure** as shown above.
    
2. In `cmd/server/main.go`, create the entry point:
    

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	log.Println("Starting server on :8080")
	http.ListenAndServe(":8080", nil)
}
```

3. Create an `internal/task/task.go`:
    

```go
package task

type Task struct {
	ID   int    `json:"id"`
	Name string `json:"name"`
	Done bool   `json:"done"`
}
```

4. Add `go.mod` in root:
    

```bash
go mod init github.com/yourusername/taskmanager
```

---

Yes — your folder structure and `go.mod` file look great. ✅

To confirm:

- ✅ Root folder: `taskmanager` inside `go_rest`
    
- ✅ Subfolders: `cmd/`, `internal/`
    
- ✅ Module name: `github.com/yourhonor1996/go_rest/taskmanager` (matches your structure)
    
- ✅ Go version: `1.23` — valid
    

You’re good to go.

---

## 🚀 Section 2: Business Logic and Handlers

#### 🧠 Mechanism

We’ll build:

- A simple in-memory store of tasks
    
- HTTP handlers using the `net/http` package
    

---

### 🧪 Task

#### 1. Create `internal/task/store.go`:

This will manage your task list.

```go
package task

import (
	"sync"
)

type Store struct {
	tasks []Task
	mu    sync.Mutex
	nextID int
}

func NewStore() *Store {
	return &Store{nextID: 1}
}

func (s *Store) Add(name string) Task {
	s.mu.Lock()
	defer s.mu.Unlock()
	t := Task{
		ID:   s.nextID,
		Name: name,
		Done: false,
	}
	s.nextID++
	s.tasks = append(s.tasks, t)
	return t
}

func (s *Store) All() []Task {
	s.mu.Lock()
	defer s.mu.Unlock()
	return append([]Task(nil), s.tasks...)
}

func (s *Store) MarkDone(id int) bool {
	s.mu.Lock()
	defer s.mu.Unlock()
	for i := range s.tasks {
		if s.tasks[i].ID == id {
			s.tasks[i].Done = true
			return true
		}
	}
	return false
}
```

---

#### 2. Create `internal/http/handler.go`:

```go
package http

import (
	"encoding/json"
	"net/http"
	"strconv"

	"github.com/yourhonor1996/go_rest/taskmanager/internal/task"
)

type Handler struct {
	Store *task.Store
}

func NewHandler(store *task.Store) *Handler {
	return &Handler{Store: store}
}

func (h *Handler) CreateTask(w http.ResponseWriter, r *http.Request) {
	var body struct {
		Name string `json:"name"`
	}
	if err := json.NewDecoder(r.Body).Decode(&body); err != nil {
		http.Error(w, "bad request", http.StatusBadRequest)
		return
	}
	t := h.Store.Add(body.Name)
	json.NewEncoder(w).Encode(t)
}

func (h *Handler) ListTasks(w http.ResponseWriter, r *http.Request) {
	json.NewEncoder(w).Encode(h.Store.All())
}

func (h *Handler) MarkDone(w http.ResponseWriter, r *http.Request) {
	idStr := r.URL.Query().Get("id")
	id, err := strconv.Atoi(idStr)
	if err != nil {
		http.Error(w, "invalid id", http.StatusBadRequest)
		return
	}
	if ok := h.Store.MarkDone(id); !ok {
		http.Error(w, "not found", http.StatusNotFound)
		return
	}
	w.WriteHeader(http.StatusNoContent)
}
```

---

#### 3. Update `cmd/server/main.go`:

```go
package main

import (
	"log"
	"net/http"

	tmhttp "github.com/yourhonor1996/go_rest/taskmanager/internal/http"
	"github.com/yourhonor1996/go_rest/taskmanager/internal/task"
)

func main() {
	store := task.NewStore()
	h := tmhttp.NewHandler(store)

	http.HandleFunc("/tasks", func(w http.ResponseWriter, r *http.Request) {
		switch r.Method {
		case http.MethodGet:
			h.ListTasks(w, r)
		case http.MethodPost:
			h.CreateTask(w, r)
		default:
			http.Error(w, "method not allowed", http.StatusMethodNotAllowed)
		}
	})

	http.HandleFunc("/done", h.MarkDone)

	log.Println("Listening on :8080")
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

---

✅ When you finish this part and successfully run `go run ./cmd/server`, test:

```bash
curl -X POST http://localhost:8080/tasks -d '{"name":"Write Go code"}' -H "Content-Type: application/json"
curl http://localhost:8080/tasks
curl -X POST http://localhost:8080/done?id=1
```

When done, reply:  
👉 `ok, finished section 2`, and I’ll take you to **Section 3: Dockerization**.

## 🚀 **Section 3: Dockerization — Running Your Go App in a Container**

This is where you package your Go app into a container that runs **anywhere** — no Go installation needed. Think of it as turning your app into a portable executable with its environment baked in.

---

### 🧠 Mechanism: How Go + Docker works

- Go produces a **single static binary**, perfect for containers.
    
- Docker allows you to define everything via a `Dockerfile`.
    
- We’ll use **multi-stage builds** for optimal image size:
    
    - One stage to build the binary
        
    - One stage to run it (in a minimal container)
        

---

### 🧪 Task

#### 1. In your root (`taskmanager/`), create a `Dockerfile`:

```Dockerfile
# ---- Build Stage ----
FROM golang:1.23 as builder

WORKDIR /app
COPY . .
RUN go build -o taskserver ./cmd/server

# ---- Run Stage ----
FROM debian:bookworm-slim

WORKDIR /app
COPY --from=builder /app/taskserver .

EXPOSE 8080

CMD ["./taskserver"]
```

This gives you:

- Small final image
    
- No need to include Go compiler in final image
    
- Runs only the binary you compiled
    

---

#### 2. Add `.dockerignore` file (important!):

```
.git
*.md
*.log
*.env
*.test
/tmp
vendor
```

This avoids sending unnecessary files into the Docker context.

---

#### 3. Build and run:

If you're in Windows (with Docker Desktop installed), run in your terminal:

```bash
docker build -t taskmanager .
```

Then:

```bash
docker run -p 8080:8080 taskmanager
```

Test it from another terminal or Postman:

```bash
curl -X POST http://localhost:8080/tasks -d '{"name":"docker test"}' -H "Content-Type: application/json"
```

---

✅ Once you confirm this is working, I’ll guide you through the **optional stretch goal**: pushing to Docker Hub and running it from anywhere.

Just say:  
👉 `Docker works`, or  
👉 `Give me the stretch goal`