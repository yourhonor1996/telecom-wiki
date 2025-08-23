🔥 **Day 6 – Web Server & Middleware (in Go)** 🔥
Today you'll learn how to build a basic HTTP server, route requests, and write middleware—essential building blocks of web development in Go.

---

# ✅ **Learning Objectives**

By the end of today, you should be able to:

1. Spin up an HTTP server with custom routes using `net/http`.

2. Understand request/response flow: `http.Request` & `http.ResponseWriter`.

3. Parse query params, form data, and JSON.

4. Write idiomatic **middleware**.

5. Chain middleware functions together.

---

### 🧩 **Sections & Tasks**

---

### 🔹 **Section 1: Build Your First HTTP Server**

**🔧 Concepts:**

- `http.HandleFunc()`

- `http.ListenAndServe()`

- `http.ResponseWriter` and `http.Request`

**🛠️ Task:**
Create a Go program that:

- Serves on `localhost:8080`

- Has two routes:

    - `/hello` → responds with `"Hello, Web!"`

    - `/time` → responds with the current server time.

Don't move on until both routes work in your browser or with `curl`.

---

### 🔹 **Section 2: Handling Request Data**

**🔧 Concepts:**

- `r.URL.Query()`

- `r.FormValue()`

- `json.NewDecoder().Decode()`

**🛠️ Task:**
Add a route `/greet?name=Ali` that greets the user with their name.

Then, add:

- A `/submit` route that handles POST requests with a form (`name`, `email`) and returns a confirmation.

- A `/json` route that accepts a JSON body like `{"name": "Ali", "age": 25}` and echoes it back.

---

### 🔹 **Section 3: Middleware**

**🔧 Concepts:**

- Functions that wrap `http.Handler`

- Logging, Authentication, CORS

**🛠️ Task:**
Write a middleware that:

- Logs each incoming request (method, URL, time)

- Apply it globally to all routes

Then, add another middleware that blocks any request **not** using `GET`.

---

### 🔹 **Section 4: Chain Middleware**

**🛠️ Task:**
Chain both middlewares so they run in order:

1. Logger

2. Method Check

Apply the chain to your `/hello` and `/greet` endpoints.

---

### 🌟 **Stretch Goals**

- ✅ Serve static files (e.g., an HTML file)

- ✅ Create a simple login form with POST and session handling

- ✅ Add support for graceful shutdown on `Ctrl+C` (`os.Signal`)

---

# Comprehensive Guide 

## Section 1
### 🧠 **Mechanism In Go: How HTTP Servers Work**

Go’s `net/http` package is all you need to build a simple web server.

At its core:
```go
http.ListenAndServe(addr string, handler http.Handler) error
```
- This starts an HTTP server on the given address (e.g., `":8080"`).

- The `handler` is typically nil (which uses the **default multiplexer** `http.DefaultServeMux`).

- You register handlers using:
```go
http.HandleFunc("/path", handlerFunc)
```
Where `handlerFunc` must have this signature:
```go
func(w http.ResponseWriter, r *http.Request)
```
- `w http.ResponseWriter`: Used to write the response.

- `r *http.Request`: Contains info about the request (URL, headers, body, etc.).

---

### 🛠️ Your Task:

Build a basic web server in Go with the following:

1. Listens on `localhost:8080`

2. Has two routes:

    - `/hello` → returns `"Hello, Web!"`

    - `/time` → returns the current time (use `time.Now()`)

**Answer**
```go
package main  
  
import (  
    "io"  
    "log"    "net/http"    "time")  
  
func sayHelloEndpoint(w http.ResponseWriter, req *http.Request) {  
    io.WriteString(w, "Hello my friend!")  
}  
  
func mainPage(w http.ResponseWriter, req *http.Request) {  
    io.WriteString(w, "This is the main page!")  
}  
  
func timePage(w http.ResponseWriter, req *http.Request) {  
    io.WriteString(w, time.Now().String())  
}  
  
func main() {  
    http.HandleFunc("/hello", sayHelloEndpoint)  
    http.HandleFunc("/", mainPage)  
    http.HandleFunc("/time", timePage)  
  
    log.Fatal(http.ListenAndServe(":8080", nil))  
  
}
```


## Section 2
### 🧠 **Mechanisms: How to Access Request Data in Go**

Go’s `http.Request` gives you multiple ways to access incoming request data:

#### ✅ 1. **Query Parameters (GET):**

URL: `/greet?name=Ali`

```go
r.URL.Query().Get("name")
```

**Answer**
```go
func Greet(w http.ResponseWriter, r *http.Request) {  
    name := r.URL.Query().Get("name")  
    io.WriteString(w, fmt.Sprintf("Hi, your name is %s ! Welcome!", name))  
}
```

---

#### ✅ 2. **Form Data (POST `application/x-www-form-urlencoded`):**

```go
r.ParseForm()
name := r.FormValue("name")
email := r.FormValue("email")
```

---

#### ✅ 3. **JSON Body (POST `application/json`):**

```go
type Payload struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

var p Payload
err := json.NewDecoder(r.Body).Decode(&p)
```

---

### 🛠️ Your Tasks:

Add the following 3 routes to your server:

---

#### ✅ Route 1: `/greet?name=Ali`

- Responds with: `Hello, Ali!`
    

---

#### ✅ Route 2: `/submit` (POST form)

- Expects fields: `name`, `email`
    
- Response: `Received form submission from <name> (<email>)`
    

**Answer**
```go
func ParseFormRaw(w http.ResponseWriter, r *http.Request) {  
    e := r.ParseForm()  
    if e != nil {  
       log.Fatal("Couldn't parse form!")  
       return  
    }  
  
    name := r.FormValue("name")  
    email := r.FormValue("email")  
  
    fmt.Printf("Values: %v %v\n", name, email)  
  
}
```

---

#### ✅ Route 3: `/json` (POST JSON)

- Expects: `{"name": "Ali", "age": 25}`
    
- Responds: `Hello Ali, you are 25 years old.`
    
Use `curl` or Postman to test the POST routes.

**Answer**
```go
func ParseFormJson(w http.ResponseWriter, r *http.Request) {  
    type Payload struct {  
       Name string  
       Age  int  
    }  
    var p Payload  
    err := json.NewDecoder(r.Body).Decode(&p)  
    if err != nil {  
       log.Fatal("Couldn't parse json !")  
    }  
    fmt.Printf("%v\n", p)  
}
```


## Section 3
### 🧠 **Mechanism: How Middleware Works in Go**

Go doesn’t have built-in middleware like frameworks do, but you can build it **elegantly** by wrapping `http.Handler`.

Conceptually, middleware is a function that takes a handler and returns another handler:

```go
func middleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Do something before
        next.ServeHTTP(w, r)
        // Do something after
    })
}
```

You can apply this wrapping to any route:

```go
http.Handle("/hello", loggingMiddleware(http.HandlerFunc(helloHandler)))
```

Or write a helper to chain them.

---

### 🛠️ Your Tasks:

#### ✅ Task 1: Logger Middleware

Write a middleware `logRequests` that logs:

- HTTP method
    
- URL path
    
- Current time
    

Use `log.Printf` for clean logs.

Apply it to **all routes**.


**Answer**
```go
func LogRequests(next http.Handler) http.Handler {  
    return http.HandlerFunc(  
       func(w http.ResponseWriter, r *http.Request) {  
          fmt.Println("Time before request: ", time.Now())  
          next.ServeHTTP(w, r)  
  
          fmt.Println("Time after request: ", time.Now())  
          fmt.Println(fmt.Sprintf("Method: %s | URL: %s", r.Method, r.URL))  
       },  
    )  
}
```

---

#### ✅ Task 2: Method Blocker Middleware

Create middleware `allowOnlyGET` that blocks non-`GET` methods by returning:

```go
http.Error(w, "Method Not Allowed", http.StatusMethodNotAllowed)
return
```

Apply it only to `/hello` and `/greet`.

**Answer**
```go
func BlockPost(next http.Handler) http.Handler {  
    return http.HandlerFunc(  
       func(w http.ResponseWriter, r *http.Request) {  
          if r.Method == http.MethodPost {  
             http.Error(  
                w,  
                fmt.Sprintf("You can't call a post request from endpoint (%s)", r.URL),  
                http.StatusMethodNotAllowed)  
             return  
          }  
          next.ServeHTTP(w, r)  
       },  
    )  
}
```


## Section 4
### 🧠 **Mechanism: Chaining Middleware in Go**

You can compose multiple middlewares by wrapping them **from outside in**, like function decorators:

```go
final := middleware1(middleware2(handler))
```

Each middleware wraps the previous one, so the order matters:

- The first middleware you **wrap with** is the **last to execute** before the handler.
    

You can also write a helper like this:

```go
func chain(h http.Handler, mws ...func(http.Handler) http.Handler) http.Handler {
    for i := len(mws) - 1; i >= 0; i-- {
        h = mws[i](h)
    }
    return h
}
```

Then apply like:

```go
http.Handle("/hello", chain(http.HandlerFunc(helloHandler), logRequests, allowOnlyGET))
```

---

### 🛠️ Your Final Task for Day 6:

- Use the `chain()` function above (or your own).
    
- Apply **both**:
    
    - `logRequests`
        
    - `allowOnlyGET`
        
- To: `/hello` and `/greet`
    

Make sure it logs and rejects non-GETs.

```go
//method 2
http.Handle("/chained", middle.Chain(http.HandlerFunc(endpoints.Greet), middle.LogRequests, middle.BlockPost))  

//method 1
chained := middle.BlockPost(middle.LogRequests(http.HandlerFunc(endpoints.ParseFormRaw)))  
http.Handle("chained2", chained)
```

---

Let me know once it’s done — I’ll give you optional stretch tasks or we can proceed to **Day 7 – Final Project & Deployment (with Docker)**.