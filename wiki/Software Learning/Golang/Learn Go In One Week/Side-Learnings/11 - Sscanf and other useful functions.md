Below is a compact “field guide” to `fmt.Sscanf` and its close relatives—the _Scan family_ in Go’s standard `fmt` package—plus a quick look at other parsing helpers you’ll reach for when `fmt` is the wrong hammer.

---

## 1. `fmt.Sscanf`

```go
n, err := fmt.Sscanf(src, format, &a, &b, &c)
```

|Parameter|Meaning|
|---|---|
|`src string`|The **input string** you want to parse.|
|`format string`|A _Printf-style_ layout string. Only the verbs matter (`%d`, `%f`, `%s`, `%v`, etc.). Width/precision flags are **ignored**.|
|`&a, &b …`|**Pointers** to variables that receive the parsed values.|
|Return values|`n` = number of items successfully scanned, `err` = `nil`, `io.EOF`, or a descriptive error.|

Key points:

- Uses **whitespace** (space, tab, newline) as the default delimiter between verbs.
    
    - To pin data to a literal character, include that character in the format string, e.g. `"%d-%d-%d"` for `YYYY-MM-DD`.
        
- Stops at the **first verb it can’t match**; you often want to check `n` as well as `err`.
    
- `%s` stops at the next whitespace; use `%q` (Go string literal) or `%[^\n]` (a “scanset”) for more flexible grabs.
    

### Tiny example

```go
var user string
var id int
if _, err := fmt.Sscanf("alice:42", "%[^:]:%d", &user, &id); err == nil {
    fmt.Println(user, id) // → "alice 42"
}
```

---

## 2. The rest of the _Scan_ family

Think of **prefix** (destination) × **suffix** (whitespace handling):

|Function|Reads **from…**|Suffix|Effect of suffix|
|---|---|---|---|
|`fmt.Scan`|`os.Stdin`|_(none)_|Stops at **space or newline** like a shell.|
|`fmt.Scanf`|`os.Stdin`|`f`|Same but you supply a _format string_.|
|`fmt.Scanln`|`os.Stdin`|`ln`|Like `Scan` **but requires newline** at end; fails if line continues.|
|`fmt.Fscan`|Any `io.Reader` you pass|_(none)_|Space/newline delimited.|
|`fmt.Fscanf`|`io.Reader` + format string|`f`|Format-driven.|
|`fmt.Fscanln`|`io.Reader`, newline-aware|`ln`|Line-bounded.|
|`fmt.Sscan`|`string` (input text)|_(none)_|Space/newline delimited.|
|`fmt.Sscanf`|`string` + format string|`f`|**← today’s star**|
|`fmt.Sscanln`|`string`, newline-aware|`ln`|Line-bounded.|

Memory aid: **F** = _File/Reader_, **S** = _String_, **ln** = _line-ending required_, plain vs **f** = whether you supply a format.

---

## 3. Practical tips & pitfalls

|Gotcha|Why it happens|How to handle|
|---|---|---|
|**Must pass pointers.**|The scanners need a place to write.|`var x int; fmt.Sscan("99", &x)`|
|**Literal `%` in format.**|Same escaping rules as `Printf`.|Use `%%` inside `format`.|
|**Unexpected spaces consumed.**|Scanners treat any run of whitespace as one separator.|Include the separator explicitly: `"%d-%d"` not `"%d %d"`.|
|**Partial scans** (e.g. you expected 3 items, got 2).|Input shorter or mismatched.|Check `n` (`if n != 3`) and/or compare `err` to `io.EOF`.|
|**Binary data or locale-sensitive data** (e.g. `1,234.5`).|`fmt` is text-oriented and locale-agnostic.|Use `encoding/binary`, `strconv.ParseFloat`, or a proper CSV/JSON lib instead.|

---

## 4. When _not_ to use the Scan family

|Need|Better tool|
|---|---|
|**Single value conversion** (“string → int/float/bool”).|`strconv.Atoi`, `ParseInt`, `ParseFloat`, `ParseBool` – faster, no format string.|
|**CSV, TSV, JSON, XML, …**|`encoding/csv`, `encoding/json`, `encoding/xml`.|
|**Token-by-token stream processing** (log files, large input).|`bufio.Scanner` with a custom `Split` function.|
|**Binary or network protocols**|`encoding/binary`, `bytes.Buffer`, or generated code (e.g. protobuf).|
|**Regex-friendly patterns**|`regexp` package – especially when delimiters vary.|

---

## 5. Cheat-sheet reference

```go
// Classic whitespace split
var a int; var b string
n, err := fmt.Sscan("123 hello", &a, &b)

// With custom format
var y, m, d int
_, _ = fmt.Sscanf("2025-07-01", "%d-%d-%d", &y, &m, &d)

// Reading from any io.Reader
r := strings.NewReader("3.14pi")
var f float64; var s string
fmt.Fscan(r, &f, &s)

// One-liner for stdin (Ctrl-D to end on Unix)
var word string
fmt.Scan(&word)
```

---

### TL;DR

- **`fmt.Sscanf`**: parse a _string_ using `Printf` verbs; pass pointers; relies on whitespace unless you anchor literals.
    
- Same verbs and rules apply to the whole _Scan family_, which only differ in **input source** and **newline handling**.
    
- For simple conversions (`strconv`), structured data (`encoding/*`), token streams (`bufio.Scanner`), or binary formats, pick a more specialized package—they’ll be faster, clearer, and safer.