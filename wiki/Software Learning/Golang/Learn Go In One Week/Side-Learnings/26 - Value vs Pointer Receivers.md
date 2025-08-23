In Go, if a method has a **value receiver**, then **a copy of the receiver** is made when the method is called.

### 🔍 What does that mean?

Suppose you have:

```go
type Point struct {
    X, Y int
}

func (p Point) Move(dx, dy int) {
    p.X += dx
    p.Y += dy
}
```

Then:

```go
pt := Point{1, 2}
pt.Move(3, 4)
fmt.Println(pt) // Output: {1 2}, not {4 6}
```

The `Move` method is defined with a **value receiver** `(p Point)`, so it **receives a copy** of `pt`. Any changes happen to the copy, not to `pt` itself.

### 🧠 Why is this important?

- For **large structs**, copying can be expensive.
    
- For **mutating** operations, value receivers won't change the original.
    

### 📌 Comparison with pointer receiver:

```go
func (p *Point) Move(dx, dy int) {
    p.X += dx
    p.Y += dy
}
```

Now:

```go
pt := Point{1, 2}
pt.Move(3, 4)
fmt.Println(pt) // Output: {4 6}
```

---

### ✅ When to use value vs. pointer receivers:

|Use pointer receiver (`*T`)|Use value receiver (`T`)|
|---|---|
|You need to **modify** the receiver|You don't modify the receiver|
|You want to **avoid copying**|The type is small (e.g. `int`)|
|The type includes a **mutex or sync**|The type is immutable|

# Why the Consistency With Pointer Receivers

**Question: Why is it that when a struct has methods on both value and pointer receivers, Such usage is not recommended by the Go Documentation?**
Great question.  
**having both value and pointer receiver methods on the same struct** is legal in Go, but the Go documentation **discourages** this practice unless there's a good reason.

Here’s **why** 👇

## ⚠️ 1. **Confusion and Inconsistency**

When you define some methods with a **value receiver** and others with a **pointer receiver**, users of your type might get confused:

```go
type User struct {
	Name string
}

func (u User) SayHello()       {} // value receiver
func (u *User) ChangeName(s string) {} // pointer receiver
```

Now:

```go
u := User{"Ali"}
u.SayHello()        // works ✅
u.ChangeName("Bob") // works ✅ — Go implicitly takes &u
```

But:

```go
var i interface{} = u // this is a value
i.(User).SayHello()        // ✅ works
i.(User).ChangeName("Bob") // ❌ compile error: method not in method set
```

The **method set of `User`** does _not_ include pointer receiver methods.  
So now things break when used through interfaces or reflection.

## 🧠 2. **Different Method Sets**

In Go:

|Type|Method set includes|
|---|---|
|`T`|Only methods with **value receivers**|
|`*T`|Methods with **both** value **and** pointer receivers|

This means:

- If someone uses a value (`T`), they can’t access pointer-only methods.
    
- But if they use a pointer (`*T`), they get everything.
    

That can lead to bugs that **only happen in specific usage patterns**.


## 📉 3. **Silent Copying**

Value receivers copy the struct, which can **silently break logic** if your method is intended to mutate the struct:

```go
func (u User) Update() {
    u.Name = "Changed" // no effect outside
}
```

So even though the code compiles and runs, it doesn’t work the way you might expect.


## ✅ Recommendation from Go Docs

If any of your methods **need a pointer**, then make **all** your methods use pointer receivers. This gives you:

- Consistency.
    
- Clear semantics.
    
- Better performance (no unnecessary copies).
    
- Full method set when using `*T`.
    

## ✅ TL;DR:

> If a method needs a pointer receiver, make **all** methods use pointer receivers — even the ones that don’t modify the struct.

# Why Use Value Receivers At All?
Excellent thinking — and this is one of the most common questions in Go design.  
You're absolutely right that **pointer receivers** are:

- More flexible (can mutate)
    
- More performant (avoid copies for large structs)
    
- And usable everywhere (`*T` has the full method set)
    

So why use **value receivers** at all?

---

## ✅ Short Answer:

Use **value receivers** when:

- The method doesn’t need to modify the struct
    
- The struct is **small and cheap to copy**
    
- You want to emphasize **immutability or statelessness**
    

---

## 🧠 Value receivers make sense when:

### 1. **The type is small and simple**

```go
type Point struct {
	X, Y int
}
```

Copying `Point` is just copying two integers — very cheap.

### 2. **The method is conceptually read-only**

You want to communicate:

> “This method does not and **should not** change the struct.”

```go
func (p Point) DistanceTo(o Point) float64 {
	// pure function
}
```

This helps with clarity and correctness — especially for math-like or stateless types.

### 3. **You want value semantics**

You’re okay with (or even want) the receiver to be **copied**.

This might apply in functional-style APIs or when designing immutable objects.

---

## ❌ But avoid value receivers when:

- The struct is **large** (copying is expensive)
    
- You have **mutexes, channels**, or embedded data you don’t want to copy
    
- You ever need to **mutate state**
    
- You want consistent method sets for interfaces or external packages
    

---

## 📌 Summary:

|Use value receiver when...|Use pointer receiver when...|
|---|---|
|Struct is small (e.g. 2–3 fields)|Struct is large or has sync primitives|
|Method doesn’t modify the receiver|You need to mutate the struct|
|You want to emphasize immutability|You want consistent behavior and efficiency|
|The method is a pure function / utility|You’re implementing an interface|

---

### 💡 Rule of thumb:

> If you’re in doubt, just use **pointer receivers**.  
> If all your methods are safe to run on **copies**, then you can switch to **value receivers** for cleaner semantics.

# A Rule of Thumb!
### ✅ Rule:

> **Always use pointer receivers unless you have a clear, specific reason to use value receivers.**

This rule is simple, safe, and helps avoid:

- Method set confusion
    
- Unintended copies
    
- Inconsistent receiver types
    

---

### 📚 Even Go’s official advice from [Effective Go](https://golang.org/doc/effective_go#methods) supports this:

> **If in doubt, use a pointer receiver.**

---

### 🔍 When might you _intentionally_ choose value receivers?

Only when:

- The method is **read-only** and
    
- The struct is **small and copyable** and
    
- You want to clearly show **no mutation** and
    
- You’re not worried about method set consistency
    

---

### 🧠 Why your rule is better in practice:

Real-world structs:

- Often grow in size
    
- Often mutate
    
- Often interact with interfaces
    

So unless you’re writing something like a mathematical vector or an iterator with stateless access, value receivers introduce more risk than benefit.

---

### 🔧 Final version of the rule:

> ✅ Use pointer receivers **by default**.  
> ❗ Use value receivers **only when**:
> 
> - Your struct is small and immutable
>     
> - You're sure copying is cheap
>     
> - You don’t need pointer-only features (mutation, mutexes, etc.)
>     

---

Let me know if you want to lint your Go code for inconsistent receivers — tools like `staticcheck` can help catch this automatically.