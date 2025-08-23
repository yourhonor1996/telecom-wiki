Here’s a **compact and comprehensive list** of the main functions, types, and constants in Go’s `time` package with **one-liner explanations**:

---

### ⏱️ **Basic Types**

- `type Time` — Represents an instant in time.
    
- `type Duration` — Represents the elapsed time between two `Time` values.
    

---

### 📅 **Time Creation and Parsing**

- `Now() Time` — Returns the current local time.
    
- `Date(y, m, d, h, min, s, ns int, loc *Location) Time` — Constructs a `Time` with specified components.
    
- `Parse(layout, value string) (Time, error)` — Parses a time string using a layout.
    
- `ParseInLocation(layout, value string, loc *Location) (Time, error)` — Like `Parse` but uses given location.
    
- `Unix(sec, nsec int64) Time` — Returns time from Unix epoch seconds and nanoseconds.
    
- `UnixMilli(ms int64) Time` — Returns time from milliseconds since Unix epoch.
    
- `UnixMicro(us int64) Time` — Returns time from microseconds since Unix epoch.
    

---

### 🧮 **Time Formatting**

- `Format(layout string) string` — Formats a `Time` using the provided layout.
    
- `AppendFormat(b []byte, layout string) []byte` — Appends formatted time to byte slice.
    

---

### 🕰️ **Time Arithmetic**

- `Add(d Duration) Time` — Returns time plus duration `d`.
    
- `Sub(u Time) Duration` — Returns the duration between two times.
    
- `AddDate(years, months, days int) Time` — Adds calendar years, months, days.
    

---

### 🧭 **Comparison & Inspection**

- `Before(u Time) bool` — Returns true if time is before `u`.
    
- `After(u Time) bool` — Returns true if time is after `u`.
    
- `Equal(u Time) bool` — Checks if times are equal.
    
- `IsZero() bool` — Checks if time is the zero time.
    
- `Truncate(d Duration) Time` — Rounds down to nearest multiple of `d`.
    
- `Round(d Duration) Time` — Rounds to nearest multiple of `d`.
    

---

### ⌛ **Durations**

- `ParseDuration(s string) (Duration, error)` — Parses duration like `"2h45m"`.
    
- `Since(t Time) Duration` — Returns time elapsed since `t`.
    
- `Until(t Time) Duration` — Returns time until `t`.
    
- `Sleep(d Duration)` — Pauses the current goroutine for duration `d`.
    

---

### 🔁 **Timers and Tickers**

- `type Timer` — Fires once after a duration.
    
- `NewTimer(d Duration) *Timer` — Creates a new `Timer`.
    
- `After(d Duration) <-chan Time` — Returns a channel that receives time after `d`.
    
- `type Ticker` — Fires repeatedly at intervals.
    
- `NewTicker(d Duration) *Ticker` — Creates a new repeating `Ticker`.
    

---

### 🌍 **Time Zones**

- `type Location` — Represents a time zone.
    
- `LoadLocation(name string) (*Location, error)` — Loads a time zone by name (e.g., `"Asia/Tehran"`).
    
- `FixedZone(name string, offset int) *Location` — Returns a fixed-offset time zone.
    
- `UTC` / `Local` — Predefined time zones.
    

---

### 🧾 **Predefined Constants**

- `const` layout constants like `time.RFC3339`, `time.Kitchen`, `time.ANSIC`, etc. — Common format layouts.
    
- `const` duration units like `time.Nanosecond`, `time.Microsecond`, `time.Millisecond`, `time.Second`, `time.Minute`, `time.Hour`.
    

---

Let me know if you want an **example-based cheat sheet** or side-by-side comparisons (e.g., `Parse` vs `Format`).