In Go, the `errors` package (in the standard library) provides functionality for creating and working with errors. Starting with **Go 1.13**, the package introduced **error wrapping** and **unwrapping**, which allows you to add context to errors and retrieve the original cause.

---

### ✅ Basic `errors` usage

```go
import "errors"

err := errors.New("something went wrong")
```

Creates a simple error of type `error`.

---

### ✅ Wrapping errors

You can **wrap** an error to add context while preserving the original cause.

#### Using `fmt.Errorf` with `%w` (Go 1.13+):

```go
import (
	"errors"
	"fmt"
)

func doSomething() error {
	originalErr := errors.New("disk full")
	return fmt.Errorf("saving file failed: %w", originalErr)
}
```

- `%w` tells `fmt.Errorf` to wrap the original error.
    
- Now you can pass a more meaningful error **with the original embedded**.
    

---

### ✅ Unwrapping errors

You can **unwrap** an error to retrieve the original error underneath.

```go
err := doSomething()

// Get the original error
unwrapped := errors.Unwrap(err)
fmt.Println(unwrapped) // "disk full"
```

You can also **check** or **match** wrapped errors:

#### Using `errors.Is`:

```go
if errors.Is(err, originalErr) {
	fmt.Println("it's a disk full error")
}
```

#### Using `errors.As` (type assertion):

```go
var pathErr *os.PathError
if errors.As(err, &pathErr) {
	fmt.Println("got a PathError:", pathErr.Path)
}
```

---

### 💡 Why use wrapping?

- **Add context** without losing the original cause
    
- **Preserve call stack** logic
    
- **Allow higher-level code to handle specific underlying errors**
    

---

### Summary:

|Function|Purpose|
|---|---|
|`errors.New`|Create a basic error|
|`fmt.Errorf("%w", err)`|Wrap an error with context|
|`errors.Unwrap(err)`|Extract the wrapped error|
|`errors.Is(err, target)`|Check if error or wrapped one matches|
|`errors.As(err, &target)`|Check if any error is of a given type|

Let me know if you want an example with a custom error type or multiple layers of wrapping.