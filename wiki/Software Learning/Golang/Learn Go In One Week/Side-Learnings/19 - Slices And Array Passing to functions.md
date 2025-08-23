Great question — Go's behavior for passing **arrays** and **slices** differs in an important way:

---

### ✅ Slices: **Passed by value, but the value is a reference to underlying array**

```go
func modifySlice(s []int) {
    s[0] = 99   // modifies original array
    s = append(s, 100) // new slice, original slice unchanged
}

func main() {
    s := []int{1, 2, 3}
    modifySlice(s)
    fmt.Println(s) // Output: [99 2 3] → underlying array was modified
}
```

🔹 The **slice header** (length, capacity, pointer to array) is passed **by value**,  
but the **underlying array** is **shared**.

So:

- Changing elements (like `s[0] = x`) affects original.
    
- Appending (`append`) may allocate a new array → original untouched.
    

---

### ❌ Arrays: **Passed by value (copied entirely)**

```go
func modifyArray(a [3]int) {
    a[0] = 99 // modifies the copy, not the original
}

func main() {
    a := [3]int{1, 2, 3}
    modifyArray(a)
    fmt.Println(a) // Output: [1 2 3] → unchanged
}
```

If you want to modify the original array, pass a pointer:

```go
func modifyArray(a *[3]int) {
    a[0] = 99
}
```

---

### 🧠 Summary

|Type|Passed by|Modifies original?|Notes|
|---|---|---|---|
|Slice|Value (of header)|✅ (when modifying elements)|Underlying array shared|
|Array|Value (whole array)|❌|Pass pointer to modify original|

Let me know if you want a visual memory diagram — it's a helpful way to internalize this!