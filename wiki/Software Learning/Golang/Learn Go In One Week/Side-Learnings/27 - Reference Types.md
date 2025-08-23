In Go, **reference types** are types where the variable holds a _reference_ (i.e., pointer) to the underlying data rather than the actual data itself. This means when you pass a reference type to a function or assign it to another variable, you're passing or assigning a reference to the same data in memory — not a copy.

### Main Reference Types in Go

Here are the main built-in reference types in Go:

---

### 1. **Slices**

- A slice is a descriptor that includes a pointer to an array, a length, and a capacity.
    
- When you assign or pass a slice, the underlying array is _shared_.
    

```go
a := []int{1, 2, 3}
b := a
b[0] = 99
fmt.Println(a) // [99 2 3] — changed!
```

---

### 2. **Maps**

- A map is a hash table and is always passed by reference.
    
- Changes through one reference are visible to others.
    

```go
m1 := map[string]int{"x": 1}
m2 := m1
m2["x"] = 42
fmt.Println(m1["x"]) // 42
```

---

### 3. **Channels**

- Channels are references to internal data structures used for communication.
    
- Sending and receiving on a channel affects the same underlying object.
    

```go
ch := make(chan int)
go func() { ch <- 5 }()
fmt.Println(<-ch) // 5
```

---

### 4. **Functions**

- Functions are also reference types.
    
- Assigning one function to another variable does not copy the function body, just the reference.
    

```go
f := func(x int) int { return x + 1 }
g := f
fmt.Println(g(2)) // 3
```

---

### 5. **Pointers**

- A pointer holds the memory address of a value.
    
- This is the most explicit form of reference.
    

```go
x := 10
p := &x
*p = 20
fmt.Println(x) // 20
```

---

### Summary Table

|Type|Reference?|Notes|
|---|---|---|
|Slice|✅|Backed by shared underlying array|
|Map|✅|Internally shared|
|Channel|✅|Shared communication object|
|Function|✅|References code/data|
|Pointer|✅|Direct memory address|
|Array|❌|Passed by value (copied)|
|Struct|❌|Passed by value (unless pointer)|
|Interface|✅|Internally a reference to value|

---

Let me know if you'd like a visual memory diagram or to explore how each behaves when passed into a function.