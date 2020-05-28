# Packages

**Package name is same as last element of import path**.

E.g, the `"math/rand"` package comprises files that begin with `package rand`.

## Imports

You can write multiple import statements:

```go
import "fmt"
import "math"
```

But it is **good style to use the factored import** statement.

```go
import (
	"fmt"
	"math"
)
```

## Exported names

**Name is exported if it begins with a capital letter**.

E.g, `Pizza` is exported, as is `Pi` (from the `math` package). `pizza` and `pi` don't start with capital letter, so they aren't exported.

# Functions

A function can take zero or more arguments.

```go
func add(x int, y int) int {
	return x + y
}
```

## Short signatrure

When two or more consecutive parameters share a type, **you can omit type from all but last**.

```go
func add(x, y int) int {
	return x + y
}
```

## Multiple results

A **function can return any number of results**.

```go
func swap(x, y string) (string, string) {
	return y, x
}

a, b := swap("hello", "world")
```

## Named return values

They treated as variables defined at the top of the function.

A **return statement without arguments returns named return values**. This is known as a _"naked"_ return.

**Should be used only in short functions**.

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

# Variables

`var` statement declares list of variables; as in function arguments, type is last.

```go
var c, python, java bool // can be at package

func main() {
	var i int            // or function level
	fmt.Println(i, c, python, java)
}
```

## Initializers

If initializer present, **type can be omitted**

```go
var c, python, java = true, false, "no!"
```

## Short declarations

**Inside function, short assignment `:=` statement can be used** instead of `var` with implicit types.

**Outside function, every statement begins with keyword** (`var, func`, and so on) and so the `:=` construct is not available.

```go
func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"
}
```

# Basic types

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

The example shows variables of several types, and also that **variable declarations may be "factored" into blocks**, as with import statements:

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)
```

The `int, uint, and uintptr` types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems.

When you need an integer value **you should use `int` unless you have a specific reason** to use a sized or unsigned integer.

## Zero values

Variables declared without an explicit initial value are given their zero value.

```go
func main() {
	var i int
	var f float64
	var b bool
	var s string
}
```

The zero value is:

```
    0 for numeric types,
    false for the boolean type, and
    "" (the empty string) for strings.
```

## Type conversions

The expression `T(v)` converts the value `v` to the type `T`.

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)

// Or simply:

i := 42
f := float64(i)
u := uint(f)
```

**Assignment between items of different type requires an explicit conversion**

## Type inference

When the right hand side of the declaration is typed, the new variable is of that same type

```go
var i int
j := i // j is an int
```

But **when the right hand side contains an untyped numeric constant, the new variable may be an int, float64, or complex128 depending on the precision of the constant**:

```go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

# Constants

Constants are **declared like variables, but with the const keyword**.

Constants **can be character, string, boolean, or numeric**.

Constants **cannot be declared using the `:=` syntax**.

```go
const Pi = 3.14

func main() {
	const World = "世界"
}
```

## Numeric Constants

Numeric constants are **high-precision values**.

An **untyped constant takes type needed by its context**.

```go
const (
	Big = 1 << 100    // binary number that is 1 followed by 100 zeroes.
	Small = Big >> 99 // shift it right again 99 places, so we end up with 1<<1, or 2.
)

func needInt(x int) int           { return x * 10 + 1 }
func needFloat(x float64) float64 { return x * 0.1 }

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
	fmt.Println(needInt(Big)) // won't work
}
```
