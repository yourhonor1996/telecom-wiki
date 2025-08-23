## What Are Struct Tags?
In Go, **struct tags** are annotations attached to struct fields that give additional meta-information. They're most commonly used to control encoding and decoding (e.g., for `json`, `xml`, `yaml`, etc.) or to guide libraries like database ORMs or validators.

Struct tags are defined as **string literals** after a field declaration, using the format:

```go
type User struct {
	Name string `json:"name"`
	Age  int    `json:"age,omitempty"`
}
```

---

### 📌 General Syntax

```go
`key1:"value1" key2:"value2"`
```

Each tag is a **key-value pair**, separated by colons and enclosed in backticks (`` ` ``). Multiple tags can be separated by spaces.

---

### 🔧 Common Struct Tags in Go

Here are the most commonly used struct tag namespaces:

|Tag Name|Purpose|
|---|---|
|`json`|JSON encoding/decoding (e.g., with `encoding/json`)|
|`xml`|XML encoding/decoding (with `encoding/xml`)|
|`yaml`|YAML encoding/decoding (e.g., `gopkg.in/yaml.v2`)|
|`db`|Used by database libraries like GORM/SQLX|
|`validate`|Validation rules (e.g., `go-playground/validator`)|
|`form`|For parsing form values in web frameworks|
|`bson`|BSON encoding for MongoDB (`go.mongodb.org/mongo-driver`)|
|`protobuf`|Tags for Protocol Buffers|
|`mapstructure`|For decoding into structs (e.g., `github.com/mitchellh/mapstructure`)|

---

### ✅ JSON Tag Examples

```go
type Product struct {
	ID    int    `json:"id"`
	Name  string `json:"name,omitempty"` // omits if empty
	Price int    `json:"-"`              // skips this field
}
```

- `omitempty`: Skip the field if it has a zero value.
    
- `-`: Do not marshal/unmarshal this field.
    

---

### ✅ Database Tags (`gorm`, `sqlx`, etc.)

```go
type User struct {
	ID   int    `db:"id"`           // used in SQLX
	Name string `gorm:"column:name;primaryKey"` // GORM
}
```

---

### ✅ Validation Tags

```go
type RegisterForm struct {
	Email string `validate:"required,email"`
	Age   int    `validate:"gte=18"`
}
```

---

### 🛠 Accessing Struct Tags

You can read tags using reflection:

```go
t := reflect.TypeOf(User{})
field, _ := t.FieldByName("Name")
tag := field.Tag.Get("json")
fmt.Println(tag) // "name"
```

---

## Go Struct Tags Cheat Sheet

## ✅ `json` (encoding/json)

|Tag|Description|
|---|---|
|`json:"name"`|Rename field to `"name"` in JSON|
|`json:"name,omitempty"`|Omit if field is zero value|
|`json:"-"`|Completely ignore this field|
|`json:",omitempty"`|Keep original name, omit if empty|

📝 Example:

```go
type Product struct {
	ID    int    `json:"id"`
	Name  string `json:"name,omitempty"`
	Price int    `json:"-"`
}
```

---

## 🛢 `gorm` (ORM for SQL databases)

|Tag|Description|
|---|---|
|`gorm:"column:col_name"`|Maps to database column `col_name`|
|`gorm:"primaryKey"`|Marks field as primary key|
|`gorm:"autoIncrement"`|Auto-increment field|
|`gorm:"unique"`|Unique constraint|
|`gorm:"not null"`|NOT NULL constraint|
|`gorm:"default:0"`|Default value|
|`gorm:"type:varchar(100)"`|Column type override|
|`gorm:"-"`|Ignore this field in GORM|

📝 Example:

```go
type User struct {
	ID    int    `gorm:"primaryKey;autoIncrement"`
	Name  string `gorm:"column:username;type:varchar(50)"`
	Email string `gorm:"unique;not null"`
}
```

---

## 🧪 `validate` (github.com/go-playground/validator)

|Tag|Description|
|---|---|
|`validate:"required"`|Field must be non-zero|
|`validate:"email"`|Must be a valid email address|
|`validate:"min=10"`|Minimum numeric/string length|
|`validate:"max=100"`|Maximum numeric/string length|
|`validate:"gte=18"`|Greater than or equal to|
|`validate:"lte=99"`|Less than or equal to|
|`validate:"oneof=a b c"`|Value must be one of a, b, or c|

📝 Example:

```go
type RegisterForm struct {
	Email string `validate:"required,email"`
	Age   int    `validate:"gte=18,lte=99"`
}
```

---

## 🌐 `form` (web form parsing)

|Tag|Description|
|---|---|
|`form:"fieldname"`|Bind form value to struct field|

📝 Example:

```go
type LoginForm struct {
	Username string `form:"username"`
	Password string `form:"password"`
}
```

---

## 🟨 `bson` (MongoDB encoding)

|Tag|Description|
|---|---|
|`bson:"fieldname"`|Maps field to BSON document field|
|`bson:"omitempty"`|Skip field if empty|
|`bson:"-"`|Exclude field from BSON|

📝 Example:

```go
type Doc struct {
	ID   string `bson:"_id"`
	Name string `bson:"name,omitempty"`
}
```

---

## 🧩 `mapstructure` (decode from map[string]interface{})

|Tag|Description|
|---|---|
|`mapstructure:"name"`|Maps field to a map key|
|`mapstructure:",squash"`|Flattens embedded struct|

📝 Example:

```go
type Config struct {
	Port int `mapstructure:"port"`
}
```

---

Would you like this exported as a `.md` (Markdown) or `.txt` file too?