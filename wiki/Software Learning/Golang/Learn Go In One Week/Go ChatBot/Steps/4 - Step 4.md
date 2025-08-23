Awesome — let’s move on. You’re now ready to **connect the dots** between the Hub and Client — this is where the WebSocket server becomes a **real-time chat server**.

---

### 🧭 **Step 4 – Hooking Clients into the Hub**

#### 🎯 Goal:

Update your `handler` to:

- Register a new `Client` with the Hub
    
- Launch:
    
    - A **reader goroutine**: reads messages from WebSocket → `hub.broadcast`
        
    - A **writer goroutine**: writes messages from `client.send` → WebSocket
        
- Clean up on disconnect
    

---

### 🧠 Go Concepts Involved:

- **Backpressure handling**: writing to `client.send` with select
    
- **Graceful disconnection**
    
- **Goroutine coordination**
    

---

### ✅ Task:

In `server/main.go` or your handler module:

#### 1. Create `Client` and register it:

```go
client := &core.Client{
	conn: conn,
	send: make(chan []byte),
}
hub.AddClient(client)
```

#### 2. Launch writer goroutine:

```go
go func() {
	for msg := range client.send {
		if err := client.conn.WriteMessage(websocket.TextMessage, msg); err != nil {
			break
		}
	}
}()
```

#### 3. Launch reader loop (synchronous in handler):

```go
for {
	_, msg, err := client.conn.ReadMessage()
	if err != nil {
		break
	}
	hub.Broadcast(msg)
}
```

> You may need to expose `hub.Broadcast(msg)` as a method that just does `hub.broadcast <- msg`.

#### 4. Handle cleanup:

- Close `client.send` (this terminates writer goroutine)
    
- Remove client from hub (`hub.RemoveClient(client)` — you should implement this)
    

---

### 🧱 Structure Tip:

You could define:

```go
func (h *Hub) RemoveClient(c *Client)
func (h *Hub) Broadcast(msg []byte)
```

to encapsulate logic and avoid accessing internal fields directly.

---

Would you like to try writing your full `handler()` now using the real Hub, or want help structuring the code before you write?