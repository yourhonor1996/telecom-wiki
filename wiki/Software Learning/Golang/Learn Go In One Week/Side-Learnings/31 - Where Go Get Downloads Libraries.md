## 📦 Where Is the Cache Stored?

By default, Go stores modules in:

```bash
$GOPATH/pkg/mod
```

Usually, `$GOPATH` is:

- **Linux/macOS**: `~/go/pkg/mod`
    
- **Windows**: `%USERPROFILE%\go\pkg\mod`
    

You can check with:

```bash
go env GOPATH
```

---

## 🧠 How It Works

1. When you run:
    
    ```bash
    go get github.com/some/module@v1.2.3
    ```
    
2. Go:
    
    - Downloads the module.
        
    - Caches it in `$GOPATH/pkg/mod`.
        
    - Records the version in your `go.mod` and `go.sum`.
        
3. Later, even if you are **offline**, Go can:
    
    - Build the project.
        
    - Run it.
        
    - Use the cached module.
        

---

## 💡 Check the Cache

To see which modules are cached:

```bash
ls $(go env GOPATH)/pkg/mod
```

Or search for a specific one:

```bash
find $(go env GOPATH)/pkg/mod -name "*module_name*"
```

---

## 🚫 Clear the Cache

If needed, you can clear the module cache:

```bash
go clean -modcache
```

---

## 📎 Summary

|Feature|Explanation|
|---|---|
|Cache location|`$GOPATH/pkg/mod`|
|Works offline|✅ Yes|
|Safe to delete|✅ Go will redownload if needed|
|Includes source|✅ Full source of dependencies|

---

Let me know if you want a demo project using offline mode!