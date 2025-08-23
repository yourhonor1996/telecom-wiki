Here's a **compact and comprehensive list** of the key functions in Go's `strings` package with **one-liner explanations**:

---

### 🔤 **Basic Operations**

- `Compare(a, b string) int` — Lexicographically compares `a` and `b`; returns -1, 0, or 1.
    
- `Contains(s, substr string) bool` — Checks if `substr` is within `s`.
    
- `ContainsAny(s, chars string) bool` — Checks if any character in `chars` is in `s`.
    
- `ContainsRune(s string, r rune) bool` — Checks if rune `r` is in `s`.
    
- `Count(s, substr string) int` — Counts non-overlapping `substr` in `s`.
    
- `EqualFold(s, t string) bool` — Case-insensitive string comparison.
    

---

### 🔡 **Searching**

- `Index(s, substr string) int` — Returns index of first `substr`; -1 if not found.
    
- `IndexAny(s, chars string) int` — Index of first occurrence of any char from `chars`.
    
- `IndexByte(s string, c byte) int` — Index of first byte `c` in `s`.
    
- `IndexRune(s string, r rune) int` — Index of first rune `r` in `s`.
    
- `LastIndex(s, substr string) int` — Last index of `substr` in `s`.
    
- `LastIndexAny(s, chars string) int` — Last index of any char in `chars`.
    

---

### ✂️ **Slicing & Splitting**

- `Split(s, sep string) []string` — Splits `s` by `sep`.
    
- `SplitAfter(s, sep string) []string` — Like `Split` but keeps `sep` at end.
    
- `SplitN(s, sep string, n int) []string` — Splits into at most `n` parts.
    
- `SplitAfterN(s, sep string, n int) []string` — Like `SplitN` but keeps `sep`.
    
- `Fields(s string) []string` — Splits by whitespace.
    
- `FieldsFunc(s string, f func(rune) bool) []string` — Splits by custom delimiter.
    

---

### 🔁 **Replacing & Mapping**

- `Replace(s, old, new string, n int) string` — Replaces `n` occurrences of `old` with `new`.
    
- `ReplaceAll(s, old, new string) string` — Replaces all occurrences of `old` with `new`.
    
- `Map(mapping func(rune) rune, s string) string` — Applies function to each rune.
    

---

### 🧱 **Prefix/Suffix**

- `HasPrefix(s, prefix string) bool` — Checks if `s` starts with `prefix`.
    
- `HasSuffix(s, suffix string) bool` — Checks if `s` ends with `suffix`.
    
- `TrimPrefix(s, prefix string) string` — Removes `prefix` from `s` if present.
    
- `TrimSuffix(s, suffix string) string` — Removes `suffix` from `s` if present.
    

---

### ✨ **Trimming**

- `Trim(s, cutset string) string` — Removes all leading/trailing chars in `cutset`.
    
- `TrimSpace(s string) string` — Removes leading/trailing whitespace.
    
- `TrimLeft(s, cutset string) string` — Trims chars from left.
    
- `TrimRight(s, cutset string) string` — Trims chars from right.
    
- `TrimFunc(s string, f func(rune) bool) string` — Trims with custom condition.
    
- `TrimLeftFunc(s string, f func(rune) bool) string` — Trims left with function.
    
- `TrimRightFunc(s string, f func(rune) bool) string` — Trims right with function.
    

---

### 🔤 **Case & Formatting**

- `ToLower(s string) string` — Converts all characters to lowercase.
    
- `ToUpper(s string) string` — Converts all characters to uppercase.
    
- `ToTitle(s string) string` — Converts to title case.
    
- `ToLowerSpecial(c unicode.SpecialCase, s string) string` — Locale-aware lowercase.
    
- `ToUpperSpecial(c unicode.SpecialCase, s string) string` — Locale-aware uppercase.
    
- `ToTitleSpecial(c unicode.SpecialCase, s string) string` — Locale-aware title case.
    
- `Title(s string) string` — (Deprecated) Capitalizes first letter of each word.
    

---

### 🧩 **Builder & Repeating**

- `Repeat(s string, count int) string` — Repeats `s` `count` times.
    
- `NewReplacer(oldnew ...string) *Replacer` — Returns a Replacer for multiple replacements.
    
- `NewReader(s string) *Reader` — Returns a `Reader` for reading from `s`.
    
- `Builder` — Efficient way to build strings using `.Write*` methods.
    

---

Let me know if you want examples or a printable markdown version.