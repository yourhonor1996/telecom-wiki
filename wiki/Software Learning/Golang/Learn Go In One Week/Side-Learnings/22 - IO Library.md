
## 🔄 `io` package (Input/Output Primitives)

### 🔑 **Core Interfaces**

- `Reader` — Has `Read(p []byte) (n int, err error)` method.
    
- `Writer` — Has `Write(p []byte) (n int, err error)` method.
    
- `Closer` — Has `Close() error`.
    
- `ReadCloser`, `WriteCloser`, `ReadWriter`, `ReadWriteCloser` — Combinations of the above.
    
- `Seeker` — Allows seeking in stream (`Seek(offset, whence)`).
    

---

### 📚 **Utility Functions**

- `Copy(dst Writer, src Reader) (written int64, err error)` — Copies all data from `src` to `dst`.
    
- `CopyN(dst Writer, src Reader, n int64)` — Copies `n` bytes from `src` to `dst`.
    
- `ReadAll(r Reader) ([]byte, error)` — Reads all data from `r` into memory.
    
- `ReadFull(r Reader, buf []byte) (n int, err error)` — Fills `buf` completely from `r`.
    
- `WriteString(w Writer, s string) (n int, err error)` — Writes a string to a writer.
    

---

### 🛠️ **Adapters and Helpers**

- `LimitReader(r Reader, n int64) Reader` — Wraps `r`, returning only first `n` bytes.
    
- `TeeReader(r Reader, w Writer) Reader` — Reads from `r`, writes every byte also to `w`.
    
- `MultiReader(r1, r2...) Reader` — Combines multiple readers into one.
    
- `MultiWriter(w1, w2...) Writer` — Combines multiple writers into one.
    

---

Let me know if you want `io/ioutil` (deprecated but still widely used), or examples and use cases!