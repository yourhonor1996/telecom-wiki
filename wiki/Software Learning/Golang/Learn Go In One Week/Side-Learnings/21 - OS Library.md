Here’s a **compact and comprehensive list** of Go’s `os` and `io` packages with **one-liner explanations** for each important function/type:

---

## 📁 `os` package (Operating System Interface)

### 🧱 **Core Types**

- `type File` — Represents an open file descriptor.
    
- `type FileInfo` — Provides metadata about a file (name, size, mode, etc.).
    
- `type DirEntry` — Provides lightweight info from directory reading.
    

---

### 📂 **File Operations**

- `Open(name string) (*File, error)` — Opens a file for reading.
    
- `OpenFile(name string, flag int, perm FileMode) (*File, error)` — Opens a file with flags (read/write/etc).
    
- `Create(name string) (*File, error)` — Creates/truncates a file for writing.
    
- `Remove(name string) error` — Deletes a file or (empty) directory.
    
- `Rename(oldpath, newpath string) error` — Renames (moves) a file or directory.
    
- `Mkdir(name string, perm FileMode) error` — Creates a new directory.
    
- `MkdirAll(path string, perm FileMode) error` — Creates a directory and parents as needed.
    
- `Stat(name string) (FileInfo, error)` — Returns metadata about a file.
    
- `Lstat(name string) (FileInfo, error)` — Like `Stat`, but doesn't follow symlinks.
    
- `ReadDir(name string) ([]DirEntry, error)` — Reads directory entries.
    

---

### 🧭 **Path & Execution**

- `Getwd() (dir string, err error)` — Gets current working directory.
    
- `Chdir(dir string) error` — Changes the current working directory.
    
- `Executable() (string, error)` — Returns the current executable’s path.
    

---

### 💻 **Env & Process**

- `Getenv(key string) string` — Gets value of environment variable.
    
- `Setenv(key, value string) error` — Sets an environment variable.
    
- `Unsetenv(key string) error` — Removes an environment variable.
    
- `Environ() []string` — Returns all env variables as `KEY=VALUE` strings.
    
- `Exit(code int)` — Exits program with the given code.
    
- `Args []string` — Command-line arguments to the program.
    
- `Hostname() (string, error)` — Gets the host name.
    

---

### ⚙️ **File Methods**

- `f.Read([]byte)` / `f.Write([]byte)` — Read from / write to the file.
    
- `f.Seek(offset int64, whence int)` — Sets file offset.
    
- `f.Close()` — Closes the file.
    
- `f.Sync()` — Flushes file to stable storage.
    
- `f.Name()` — Returns file name.
    

---
