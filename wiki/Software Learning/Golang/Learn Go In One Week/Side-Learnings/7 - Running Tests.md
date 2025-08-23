Here's a **short and sharp tutorial** on writing and running tests in Go — covering both **basic function tests** and **table-driven tests**, plus key rules to follow.

---

## ✅ **1. Test File Rules**

|Rule|Explanation|
|---|---|
|File name|Must end with `_test.go` (e.g. `mathops_test.go`)|
|Location|Must be in the **same package** as the code under test|
|Function signature|Must be `func TestXxx(t *testing.T)`|
|Function name|Must start with `Test` and be **capitalized**|

---

## 🧪 **2. Basic Function Test**

```go
package mathops

import "testing"

func TestAdd(t *testing.T) {
    got := Add(2, 3)
    want := 5
    if got != want {
        t.Errorf("Add(2, 3) = %d; want %d", got, want)
    }
}
```

---

## 📋 **3. Table-Driven Test (Go idiom)**

```go
func TestAddTable(t *testing.T) {
    tests := []struct {
        a, b int
        want int
    }{
        {1, 2, 3},
        {2, 3, 5},
        {10, 15, 25},
    }

    for _, tt := range tests {
        got := Add(tt.a, tt.b)
        if got != tt.want {
            t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, got, tt.want)
        }
    }
}
```

> ✅ Table tests are more **scalable** and cleaner than writing many `TestXxx` functions.

---

## 🚀 **4. Running Tests**

From module root (`myapp/`), run:

|Command|What it does|
|---|---|
|`go test ./...`|Run all tests recursively|
|`go test ./mathops`|Run tests only in `mathops/`|
|`go test -v`|Verbose output|
|`go test -cover`|Show code coverage|

---

## 🔎 **5. Common Mistakes**

- ❌ `Testadd` → not capitalized = won't run
    
- ❌ `mathops_test.go` in wrong folder
    
- ❌ test in `main` package instead of actual package
    
- ❌ not using `*_test.go` → test won't be picked up
    
- ❌ missing `import "testing"`
    

---

Let me know if you want to add benchmarks (`func BenchmarkXxx`) or want me to check your `mathops_test.go`.