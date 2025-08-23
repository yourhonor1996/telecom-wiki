A **WebSocket** is a communication protocol that enables **full-duplex (two-way)** communication between a client (typically a web browser) and a server over a **single, long-lived TCP connection**.

### 🔄 How It Works

- The client initiates a **WebSocket handshake** using an HTTP request with an `Upgrade` header.
    
- If the server supports WebSockets, it responds with an HTTP 101 response to switch the protocol.
    
- Once established, the connection remains open and both sides can **send/receive messages anytime**.
    

### ✅ Key Features

- **Bi-directional**: Both client and server can push data independently.
    
- **Persistent connection**: Unlike HTTP, where each request opens a new connection, WebSockets maintain one open connection.
    
- **Low overhead**: After the handshake, there's very little communication overhead.
    

### 🔧 Use Cases

- **Chat applications**
    
- **Real-time notifications**
    
- **Live feeds (stock prices, sports scores)**
    
- **Collaborative editing (Google Docs style)**
    
- **Online gaming**
    

### 🆚 WebSocket vs HTTP

|Feature|HTTP|WebSocket|
|---|---|---|
|Direction|Request/response|Full-duplex|
|Connection|Short-lived|Long-lived|
|Latency|Higher|Lower|
|Use case|Fetching web pages, APIs|Real-time updates|
## Queueing 
Yes, when one side (client or server) sends multiple WebSocket messages quickly, **those messages are typically queued and sent in order** by the underlying WebSocket implementation.

### 🔁 Message Queuing in WebSockets

- **Outbound messages (sent)**: If you're sending messages faster than the network can transmit them, the WebSocket library (like Go’s `gorilla/websocket`) will **buffer (queue) them in memory**.
    
- **Inbound messages (received)**: Incoming messages are queued by the WebSocket implementation or the OS's TCP stack until your application reads them.
    

### ⚠️ Important Considerations

1. **Buffer limits**:
    
    - Most WebSocket libraries let you configure **write buffer size** and **read buffer size**.
        
    - If the buffer gets full (because the other side is too slow to read), you may get an error or a dropped connection.
        
2. **Concurrency**:
    
    - In Go (e.g., using `gorilla/websocket`), you can **read and write from different goroutines**, but:
        
        - Only **one reader** and **one writer** goroutine at a time.
            
        - You need to synchronize writes to avoid concurrent access to the socket.
            
3. **Backpressure**:
    
    - If the receiver cannot keep up, and the sender keeps sending, you'll get backpressure: eventually the sender will block or fail.
        

---

### 🧠 Example in Go

If you spawn a goroutine to read and another to write:

```go
go func() {
    for {
        messageType, msg, err := conn.ReadMessage()
        // handle msg...
    }
}()

go func() {
    for {
        err := conn.WriteMessage(websocket.TextMessage, []byte("hello"))
        // handle err...
    }
}()
```

Just make sure writes are synchronized (or only one goroutine writes).

Would you like a full Go example showing queuing and how to avoid write conflicts?