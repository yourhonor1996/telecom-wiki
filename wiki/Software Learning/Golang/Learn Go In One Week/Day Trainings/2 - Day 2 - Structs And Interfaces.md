Great — we're picking up from **Day 1** of your 7-day Go learning sprint. You covered **Go Basics (Syntax & Tooling)**. Now it's time for **Day 2: Structs, Interfaces & Error Handling**.

---

## **Day 2: Learning Objectives**

By the end of today, you should be able to:

1. Define and use **structs** to create custom types.
    
2. Use **interfaces** to decouple code and achieve polymorphism.
    
3. Handle **errors** idiomatically in Go using the built-in `error` type.
    
4. Understand Go’s approach to **type composition** and **interface satisfaction**.
    

---

## **🚀 Day 2 Tasks**

### 1. **Structs: Custom Data Types**

**Mechanism**:  
In Go, `struct` types allow you to group related data together. They're like `class` with just data, no inheritance. Methods can be attached separately.

#### ✅ Task 1: Define and Use Structs

Create a file `main.go` and:

1. Define a `User` struct with fields: `Name string`, `Age int`, `Email string`.
    
2. Create a value of type `User` and initialize it.
    
3. Write a method on `User` called `IsAdult() bool`.
    

⏱ Expected Output: `true` for age ≥ 18, `false` otherwise.

**Answer**
```go
package main  
  
import "fmt"  
  
type User struct {  
    Name  string  
    Age   int  
    Email string  
}  
  
func (u *User) IsAdult() bool {  
    return u.Age >= 18  
}  
  
func (u *User) Greet() {  
    fmt.Println("Hello to you Mr/Mrs <", u.Name, ">")  
}  
  
func main() {  
    var user1 User  
    user1.Name = "Mohammad"  
    user1.Age = 29  
    user1.Email = "some@gmail.com"  
  
    user2 := User{"Rasul", 65, "rasul@gmail.com"}  
  
    user3 := User{Name: "Majid", Age: 35, Email: "majid@gmail.com"}  
  
    fmt.Println("User2 is ", user2.IsAdult())  
    fmt.Println("User3 is ", user3.IsAdult())  
    user3.Greet()  
}
```

---

### 2. **Interfaces: Behavior Contracts**

**Mechanism**:  
Interfaces define method sets. Any type that implements those methods implicitly satisfies the interface — no `implements` keyword required.

#### ✅ Task 2: Define and Use an Interface

1. Define an interface `Notifier` with a method `Notify() string`.
    
2. Make your `User` type satisfy `Notifier` by implementing `Notify`.
    
3. Write a function `SendNotification(n Notifier)` that prints `n.Notify()`.
    

**Answer**

```go
package main  
  
import (  
    "fmt"  
)  
  
type Person struct {  
    firstName string  
    lastName  string  
}  
  
func (p *Person) Notify() string {  
    return "This is a notification for: " + p.firstName + " " + p.lastName  
}  
  
func (p *Person) SaySomethingInteresting() string {  
    return "Something interesing for: " + p.firstName + " " + p.lastName  
}  
  
type Notifier interface {  
    Notify() string  
    SaySomethingInteresting() string  
}  
  
func main() {  
  
    p1 := Person{"Mohammad", "Alavi"}  
    fmt.Println("First name isi: ", p1.firstName)  
  
    fmt.Println(p1.Notify())  
    fmt.Println(p1.SaySomethingInteresting())  
}
```

⏱ Expected Output: Something like: `"Sending email to john@example.com"`

---

### 3. **Error Handling: Idiomatic Go**

**Mechanism**:  
Go handles errors explicitly using the built-in `error` interface. No exceptions. You return errors as the second value from functions.

#### ✅ Task 3: Return and Handle Errors

1. Write a function `CreateUser(name string, age int, email string) (User, error)`:
    
    - Return an error if `age < 0` or `email == ""`.
        
2. Use this function and handle the error properly with an `if err != nil` check.
    

⏱ Expected Output:

- On valid input: print user info
    
- On invalid input: print the error message
    

**Answer**

```go
package main  
  
import (  
    "fmt"  
)  
  
type NotificationError struct {  
}  
  
func (n NotificationError) Error() string {  
    return "A notification error has occurred"  
}  
  
type Person struct {  
    firstName string  
    lastName  string  
}  
  ok
func (p *Person) Notify() (string, error) {  
    if len(p.firstName) < 10 {  
       return "This is a notification for: " + p.firstName + " " + p.lastName, nil  
    } else {  
       return "", NotificationError{}  
    }  
}  
  
func (p *Person) SaySomethingInteresting() (string, error) {  
    if len(p.firstName) < 10 {  
       return "This is a notification for: " + p.firstName + " " + p.lastName, nil  
    } else {  
       return "", NotificationError{}  
    }  
}  
  
type Notifier interface {  
    Notify() (string, error)  
    SaySomethingInteresting() (string, error)  
}  
  
func main() {  
  
    p1 := Person{"Mohammad Ebne Mousabiu", "Alavi"}  
    fmt.Println("First name isi: ", p1.firstName)  
  
    notify, e := p1.Notify()  
    if e == nil {  
       fmt.Println(notify)  
    } else {  
       println("There was an error:", e.Error())  
    }  
    notify, e = p1.SaySomethingInteresting()  
  
    if e == nil {  
       fmt.Println(notify)  
    } else {  
       println("There was an error:", e.Error())  
    }  
  
}
```

## 🧠 Stretch Tasks (Optional)

- Define another struct `Admin` and have it implement `Notifier`.
    
- Create a slice of `Notifier` values and call `SendNotification` on each.
    
- Define a custom error type `InvalidUserError` and return that instead of a generic error.
    

---

Let me know once you've done the tasks or if you want a checkpoint review or code check. When you're ready, I'll guide you through interfaces with composition and type assertions next.