Here’s a **compact and comprehensive list** of key components in Go’s `net/http` package with **one-liner explanations**:

---

## 🌐 `net/http` — HTTP Client and Server

---

### 🧱 **Core Types**

- `type Request` — Represents an HTTP request.
    
- `type Response` — Represents an HTTP response.
    
- `type Client` — Performs HTTP requests.
    
- `type Server` — Configures and runs an HTTP server.
    
- `type Handler` — Interface for handling HTTP requests (`ServeHTTP` method).
    
- `type ServeMux` — HTTP request multiplexer (router).
    
- `type ResponseWriter` — Interface used to construct an HTTP response.
    

---

### 📡 **Client-Side (Making Requests)**

- `Get(url string) (*Response, error)` — Sends an HTTP GET request.
    
- `Post(url, contentType string, body io.Reader) (*Response, error)` — Sends an HTTP POST request.
    
- `PostForm(url string, data url.Values) (*Response, error)` — Sends form-encoded POST request.
    
- `Head(url string) (*Response, error)` — Sends HTTP HEAD request.
    
- `NewRequest(method, url string, body io.Reader) (*Request, error)` — Creates a new HTTP request.
    
- `DefaultClient.Do(req *Request) (*Response, error)` — Sends a custom request.
    

---

### 📥 **Handling Responses**

- `resp.Body.Read([]byte)` — Reads response body (don’t forget to `Close()`).
    
- `io.ReadAll(resp.Body)` — Reads the entire response body.
    
- `resp.StatusCode` — HTTP status code of the response.
    
- `resp.Header` — Response headers.
    

---

### 🖥️ **Server-Side (Handling Requests)**

- `ListenAndServe(addr string, handler Handler) error` — Starts an HTTP server.
    
- `Handle(pattern string, handler Handler)` — Registers a handler for a route.
    
- `HandleFunc(pattern string, handler func(ResponseWriter, *Request))` — Shorthand for simple handlers.
    
- `ServeHTTP(w ResponseWriter, r *Request)` — Core handler method.
    
- `DefaultServeMux` — Default multiplexer used by `Handle`/`HandleFunc`.
    

---

### 🧰 **Server Utilities**

- `ListenAndServeTLS(addr, certFile, keyFile string, handler Handler) error` — Starts HTTPS server.
    
- `Serve(l net.Listener, handler Handler) error` — Serves HTTP over custom listener.
    
- `FileServer(fs http.FileSystem) Handler` — Serves static files.
    
- `StripPrefix(prefix string, h Handler) Handler` — Removes prefix from requests before passing to handler.
    
- `Redirect(w ResponseWriter, r *Request, url string, code int)` — Sends a redirect response.
    
- `Error(w ResponseWriter, msg string, code int)` — Sends an HTTP error response.
    

---

### 🗃️ **Request Helpers**

- `r.URL` — Parsed URL of the request.
    
- `r.Method` — HTTP method ("GET", "POST", etc.).
    
- `r.Header` — Access headers in the request.
    
- `r.Body` — Access request body (read then close).
    
- `r.Form` / `r.PostForm` — Form and POST parameters (use `ParseForm()` first).
    
- `r.Context()` — Returns the request context.
    

---

### 🧪 **Other Useful Bits**

- `type Cookie` — Represents an HTTP cookie.
    
- `SetCookie(w ResponseWriter, cookie *Cookie)` — Sends a `Set-Cookie` header.
    
- `ReadRequest(b *bufio.Reader) (*Request, error)` — Parses an HTTP request from a buffered reader.
    
- `MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64) io.ReadCloser` — Limits body size.
    


# Cheat Sheet


## ⚡️ HTTP Server: Minimal Example

```go
package main

import (
	"fmt"
	"net/http"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "Hello, World!")
}

func main() {
	http.HandleFunc("/", helloHandler)              // Register handler
	http.ListenAndServe(":8080", nil)               // Start server
}
```

---

## 🗂 Route Multiplexing with `ServeMux`

```go
mux := http.NewServeMux()
mux.HandleFunc("/hello", helloHandler)
mux.HandleFunc("/goodbye", goodbyeHandler)
http.ListenAndServe(":8080", mux)
```

---

## 📁 Serve Static Files

```go
fs := http.FileServer(http.Dir("./static"))
http.Handle("/static/", http.StripPrefix("/static/", fs))
```

---

## 🧾 Read Query & Form Params

```go
func handler(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()
	name := r.FormValue("name")              // Works for both GET & POST
	age := r.URL.Query().Get("age")          // Just GET params
}
```

---

## 📨 Read Request Body

```go
body, err := io.ReadAll(r.Body)
defer r.Body.Close()
fmt.Println(string(body))
```

---

## 🍪 Set and Read Cookies

```go
http.SetCookie(w, &http.Cookie{Name: "token", Value: "abc123"})
cookie, err := r.Cookie("token")
```

---

## 🚦 Redirect and Error

```go
http.Redirect(w, r, "/new-url", http.StatusFound)
http.Error(w, "Forbidden", http.StatusForbidden)
```

---

## 🔒 HTTPS Server

```go
http.ListenAndServeTLS(":443", "cert.pem", "key.pem", nil)
```

---

## 🧪 Test Handlers (using `httptest`)

```go
req := httptest.NewRequest("GET", "/hello", nil)
w := httptest.NewRecorder()

helloHandler(w, req)

resp := w.Result()
body, _ := io.ReadAll(resp.Body)
```

---

## 🌐 HTTP Client (GET, POST)

```go
resp, err := http.Get("https://api.example.com")
defer resp.Body.Close()
body, _ := io.ReadAll(resp.Body)
```

```go
data := strings.NewReader("name=John")
resp, err := http.Post("https://api.example.com", "application/x-www-form-urlencoded", data)
```

---

## 📦 Custom HTTP Request

```go
client := &http.Client{}
req, _ := http.NewRequest("DELETE", "https://api.com/item/1", nil)
req.Header.Set("Authorization", "Bearer token123")
resp, err := client.Do(req)
```

---

## ⏱ Set Timeout on HTTP Client

```go
client := &http.Client{Timeout: 5 * time.Second}
```

---

Let me know if you want:

- Middleware patterns
    
- JSON API examples
    
- Graceful shutdown example
    
- Integration with `gorilla/mux` or `chi` router