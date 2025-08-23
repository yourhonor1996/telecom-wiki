### 🧭 **Step 1 – Project Initialization & Folder Structure**

#### 🎯 Goal:

Set up the base folder structure and Go modules correctly. You already said you're using this path:

```
github.com/yourhonor1996/go_chatbot/chatbot/
```

We'll create two Go modules:

- One for the **server** (`chatbot/server`)
    
- One for the **client** (`chatbot/client`)
    

---

#### 🛠️ Go Mechanisms Involved:

- `go mod init`: initializes a new Go module
    
- Folder-based module structure
    
- Keeping module paths clean and reflecting import paths
    

---

#### ✅ Task:

In your `go_chatbot/chatbot/` folder, create this structure:

```
chatbot/
├── client/
└── server/
```

Then:

1. Open terminal in `chatbot/server`  
    Run:
    
    ```bash
    go mod init github.com/yourhonor1996/go_chatbot/chatbot/server
    ```
    
2. Open terminal in `chatbot/client`  
    Run:
    
    ```bash
    go mod init github.com/yourhonor1996/go_chatbot/chatbot/client
    ```
    

---

Once you've done that, let me know, and we'll move on to **Step 2: Writing a minimal WebSocket echo server**.