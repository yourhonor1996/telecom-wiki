Great questions. Let's tackle them in order, starting with the **mechanism**, then the **Go-style OOP**, and finally the **conventions**.

---

### ❓ Does Go Have Classes and OOP?

**Short Answer:**  
Go does **not** have classes.  
But Go **does** support object-oriented programming (OOP) **without inheritance or classes**, using **structs + interfaces + composition**.

---

## ⚙️ Mechanism of OOP in Go

Go supports key OOP concepts using **simpler, more explicit constructs**:

| OOP Concept    | Go Equivalent                          |
| -------------- | -------------------------------------- |
| Class          | `struct`                               |
| Field/Property | Fields in a `struct`                   |
| Method         | Function with receiver (`func (s *S)`) |
| Inheritance    | **Composition** (embedding structs)    |
| Interface      | Duck-typed `interface`                 |
| Polymorphism   | Satisfied via interface implementation |

#### ✅ Example: Basic Struct + Method

```go
type User struct {
    Name string
    Age  int
}

func (u *User) Greet() {
    fmt.Println("Hello,", u.Name)
}
```

#### ✅ Composition (Go's version of inheritance)

```go
type Admin struct {
    User      // Embedded struct: Admin *is-a* User
    Level int
}
```

#### ✅ Interface & Polymorphism

```go
type Greeter interface {
    Greet()
}

func SayHello(g Greeter) {
    g.Greet()
}
```

If `User` has `Greet()`, it satisfies `Greeter`.

---

## ✅ Go's OOP Philosophy

- Prefer **composition over inheritance**.
    
- Favor **interfaces** as contracts, but **define them where they are used**, not where they are implemented.
    
- Embrace **explicitness** over magical behavior.
    
- Don't force inheritance trees—**keep data and behavior modular**.
    

---