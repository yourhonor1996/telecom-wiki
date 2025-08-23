Awesome! Now that we have the folder structure and Go modules ready, let's build the foundation.

---

### 🧭 **Step 2 – Minimal WebSocket Echo Server**

#### 🎯 Goal:

Create a basic WebSocket server that listens on a port and echoes back any message it receives from a client.

---

#### 🧠 Go Mechanisms Involved:

- **HTTP server** using `net/http`
    
- **Upgrading HTTP to WebSocket** with `github.com/gorilla/websocket`
    
- **Goroutines** to handle each client independently
    
- **Graceful error handling**
    

---

#### 📦 Required Dependency:

Install Gorilla WebSocket:

```bash
go get github.com/gorilla/websocket
```

---

### ✅ Task:

1. In your `server/` folder:
    
    - Create a file `main.go`.
        
2. Inside `main.go`, do the following:
    
    - Set up a `http.HandleFunc("/ws", handler)`
        
    - In the `handler`, upgrade the connection to WebSocket using `websocket.Upgrader`
        
    - Create a goroutine to:
        
        - Read messages from client
            
        - Echo them back
            

---

### 🧠 Before You Code: Think!

- Where should the WebSocket handler go (modular-wise)? Will you later want to extract it into its own package?
    
- How do you want to structure incoming client connections?
    
- Should each connection run in its own goroutine?
    

---

Once you're done writing the `main.go` with echo logic, share it with me here and I’ll review it before we move on to broadcasting to **multiple clients**.