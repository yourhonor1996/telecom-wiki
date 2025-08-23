Here's a complete list of commonly used functions in Go's `strconv` package, along with short descriptions for each:

---

### 🔢 Integer Conversion

|Function|Description|
|---|---|
|`Atoi(s string) (int, error)`|Converts a decimal string to an `int`.|
|`Itoa(i int) string`|Converts an `int` to a decimal string.|
|`ParseInt(s string, base int, bitSize int) (int64, error)`|Parses a string as a signed integer in the given base and bit size (0–64).|
|`ParseUint(s string, base int, bitSize int) (uint64, error)`|Parses a string as an unsigned integer.|
|`FormatInt(i int64, base int) string`|Converts an `int64` to a string in the given base.|
|`FormatUint(i uint64, base int) string`|Converts a `uint64` to a string in the given base.|
|`AppendInt(dst []byte, i int64, base int) []byte`|Appends the base-`n` string of `i` to `dst`.|
|`AppendUint(dst []byte, i uint64, base int) []byte`|Like `AppendInt`, but for unsigned ints.|

---

### 🧮 Float Conversion

|Function|Description|
|---|---|
|`ParseFloat(s string, bitSize int) (float64, error)`|Parses a string as a float of the given bit size (32 or 64).|
|`FormatFloat(f float64, fmt byte, prec, bitSize int) string`|Converts a float to a string with specified format (e.g., 'f', 'e').|
|`AppendFloat(dst []byte, f float64, fmt byte, prec, bitSize int) []byte`|Like `FormatFloat`, but appends result to a byte slice.|

---

### ✅ Boolean Conversion

|Function|Description|
|---|---|
|`ParseBool(s string) (bool, error)`|Parses a string as a boolean. Accepts: `"1"`, `"t"`, `"true"`, etc.|
|`FormatBool(b bool) string`|Converts a bool to `"true"` or `"false"`.|
|`AppendBool(dst []byte, b bool) []byte`|Appends `"true"` or `"false"` to a byte slice.|

---

### 🔤 Quote and Unquote

|Function|Description|
|---|---|
|`Quote(s string) string`|Returns a double-quoted Go string literal.|
|`QuoteToASCII(s string) string`|Same as `Quote`, but escapes non-ASCII.|
|`QuoteRune(r rune) string`|Returns a single-quoted Go character literal.|
|`QuoteRuneToASCII(r rune) string`|Like `QuoteRune`, but escapes non-ASCII.|
|`Unquote(s string) (string, error)`|Removes surrounding quotes and unescapes.|
|`UnquoteChar(s string, quote byte) (value rune, multibyte bool, tail string, err error)`|Unquotes a single character from a quoted string.|

---

### 🧪 Miscellaneous

|Function|Description|
|---|---|
|`CanBackquote(s string) bool`|Reports whether the string can be safely backquoted (no backticks or control characters).|

---

Let me know if you'd like examples or a printable cheat sheet version!