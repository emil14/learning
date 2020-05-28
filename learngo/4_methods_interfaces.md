# Methods

You can **define methods on types**

Method is a **function with a receiver argument**

The receiver appears in its own argument list **between `func` keyword and method name**

```go
type Vertex struct {
	X, Y float64
}

// `Abs` has a receiver of type `Vertex` named `v`
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs()) // v has a .Abs() method
}
```

## Methods are functions

Remember: a **method is just a function with a receiver argument**.

Here's Abs written as a regular function with no change in functionality.

```go
type Vertex struct {
	X, Y float64
}

// no reciever
func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(Abs(v)) // v does not have .Abs method
}
```

## Methods continued

You can **declare a method on non-struct types**, too.

In this example we see a numeric type `MyFloat` with an `Abs` method.

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs()) // f is a float64 primitive, but it does has a .Abs() method
}
```

## Pointer receivers

You can declare methods with pointer receivers.

Receiver type has syntax `*T` for some type `T`.

Also, `T` cannot itself be a pointer

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	// `v` here is like `this` in JS
	return math.Sqrt(v.X * v.X + v.Y * v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs()) // sqrt((3*3)+(4*4)) = sqrt(9+16) = 5
	v.Scale(10)          // x := 3*10; y := 3*40
	fmt.Println(v.Abs()) // sqrt((30*30)+(40*40)) = sqrt((30*30)+(40*40)) = sqrt(2500) = 50
}
```

# Pointers and functions

Here `Abs` and `Scale` methods rewritten as functions.

```go
type Vertex struct {
	X, Y float64
}

func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func Scale(v *Vertex, f float64) {
	// this works too
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v)      // {3 4}
	Scale(&v, 10)
	fmt.Println(v)      // {30 40}
	fmt.Println(Abs(v)) // 50
}
```

## Methods and pointer indirection

_Functions with pointer argument_ must take pointer:

```go
var v Vertex
ScaleFunc(&v, 5) // OK
ScaleFunc(v, 5)  // Compile error!
```

_Methods with pointer receivers_ take either a value or a pointer:

```go
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
```

Go interprets statement `v.Scale(5)` as `(&v).Scale(5)` since the Scale method has a pointer receiver.

```go
type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func ScaleFunc(v *Vertex, f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(2) // works despite `v` is a value, not pointer
	ScaleFunc(&v, 10)

	p := &Vertex{4, 3}
	p.Scale(3) // for Scale method both v and p are pointers
	ScaleFunc(p, 8)

	fmt.Println(v, p)
}
```

## Methods and pointer indirection (2)

The equivalent thing happens in the reverse direction.

Functions that take a value argument must take a value of that specific type:

```go
var v Vertex
fmt.Println(AbsFunc(v))  // OK
fmt.Println(AbsFunc(&v)) // Compile error!
```

Methods with value receivers take either a value or a pointer:

```go
var v Vertex
fmt.Println(v.Abs()) // OK
p := &v
fmt.Println(p.Abs()) // OK
```

`p.Abs()` is interpreted as `(*p).Abs()`.

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func AbsFunc(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
	fmt.Println(AbsFunc(v))

	p := &Vertex{4, 3}
	fmt.Println(p.Abs())
	fmt.Println(AbsFunc(*p))
}
```

## Choosing a value or pointer receiver

2 reasons to use pointer receiver:

1. Method can modify the value that its receiver points to
2. Avoid copying the value on each method call

Here `Scale` and `Abs` are with receiver type `*Vertex`, even though the `Abs` method needn't modify its receiver

```go
type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := &Vertex{3, 4}
	fmt.Printf(v, v.Abs()) // sqrt(9 + 16) = 5
	v.Scale(5)             // v.X, v.Y = 15, 20
	fmt.Printf(v, v.Abs()) // sqrt(235 + 400) = 25
}
```

**All methods on a type should have either value or pointer receivers, but not a mixture**

# Interfaces

An **interface type** is defined as a **set of method signatures**.

A **value of interface type can hold any value that implements those methods**

```go
type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // MyFloat implements Abser
	a = &v // *Vertex implements Abser

	// v is a Vertex (not *Vertex)
	// and does NOT implement Abser
	a = v

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

## Interfaces are implemented implicitly

**Type implements an interface by implementing its methods**

Implicit interfaces decouple definition from implementation, which could then appear in any package without prearrangement

```go
type I interface {
	M()
}

type T struct {
	S string
}

// This method means type T implements the interface I,
// but we don't need to explicitly declare that it does so.
func (t T) M() {
	fmt.Println(t.S)
}

func main() {
	var i I = T{"hello"}
	i.M()
}
```

## Interface values

Under the hood, interface values can be thought of as a tuple of a value and a concrete type:

`(value, type)`

An **interface value holds a value of a specific underlying concrete type**.

**Calling a method on an interface value executes the method of the same name on its underlying type**.

```go
type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	fmt.Println(t.S)
}

type F float64

func (f F) M() {
	fmt.Println(f)
}

func main() {
	var i I

	i = &T{"Hello"}
	fmt.Printf("(%v, %T)\n", i, i) // (&{Hello}, *main.T)
	i.M()

	i = F(math.Pi)
	fmt.Printf("(%v, %T)\n", i, i) // (3.141592653589793, main.F)
	i.M()
}
```

## Interface values with nil underlying values

If concrete value inside the interface itself is `nil`, method will be called with a `nil` receiver

```go
type I interface {
	M()
}

type T struct {
	S string
}

// In Go it's common to write methods that handle being called with a `nil` receiver
func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I // i == nil

	var t *T // t == nil
	i = t
	describe(i)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

## Nil interface values

A `nil` interface value holds neither value nor concrete type

**Calling a method on a `nil` interface is a run-time error** because there is no type inside the interface tuple to indicate which concrete method to call

```go
type I interface {
	M()
}

func main() {
	var i I
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

## The empty interface

The interface type that specifies zero methods is known as the empty interface:

`interface{}`

An empty interface may hold values of any type. (Every type implements at least zero methods.)

Empty interfaces are used by code that handles values of unknown type. For example, fmt.Print takes any number of arguments of type `interface{}`.

```go
func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)

	i = "hello"
	describe(i)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

# Type assertions

A type assertion provides access to an interface value's underlying concrete value.

`t := i.(T)`

This statement asserts that the interface value i holds the concrete type T and assigns the underlying T value to the variable t.

If `i` does not hold a `T`, the statement will trigger a `panic`.

To _test_ whether an interface value holds a specific type, a type assertion can return two values: the underlying value and a boolean value that reports whether the assertion succeeded.

`t, ok := i.(T)`

If `i` holds a `T`, then `t` will be the underlying value and `ok` will be true.

If not, `ok` will be false and `t` will be the zero value of type `T`, and no panic occurs.

Note the similarity between this syntax and that of reading from a `map`.

```go
func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // panic
	fmt.Println(f)
}
```

## Type switches

A type `switch` is a construct that permits several type assertions in series.

A type switch is like a regular switch statement, but the cases in a type switch specify types (not values),
and those values are compared against the type of the value held by the given interface value.

```go
switch v := i.(type) {
case T:
    // here v has type T
case S:
    // here v has type S
default:
    // no match; here v has the same type as i
}
```

The declaration in a type switch has the same syntax as a type assertion `i.(T)`, but the specific type `T` is replaced with the keyword type.

This switch statement tests whether the interface value `i` holds a value of type `T` or `S`. In each of the `T` and `S` cases, the variable `v` will be of type `T` or `S` respectively and hold the value held by `i`. In the `default` case (where there is no match), the variable `v` is of the same interface type and value as `i`.

```go
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```

# Stringers

One of the most ubiquitous interfaces is `Stringer` defined by the `fmt` package.

```go
type Stringer interface {
    String() string
}
```

A `Stringer` is a type that can describe itself as a `string`. The `fmt` package (and many others) look for this interface to print values.

```go
type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```

## Exercise: Stringers

Make the `IPAddr` type implement `fmt.Stringer` to print the address as a dotted quad.

For instance, `IPAddr{1, 2, 3, 4}` should print as `"1.2.3.4"`.

```go
type IPAddr [4]byte

func (i IPAddr) String() string {
	str := ""
	for name, value := range i {
		str += fmt.Sprintf(".%v")
	}
	return str
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Printf("%v: %v\n", name, ip)
	}
}
```

# Errors

Go programs express _error state_ with _error values_.

The `error` type is a built-in interface similar to `fmt.Stringer`:

```go
type error interface {
    Error() string
}
```

(As with `fmt.Stringer`, the `fmt` package looks for the `error` interface when printing values.)

Functions often return an `error` value, and calling code should handle errors by testing whether the error equals `nil`.

```go
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```

A `nil` error denotes success; a **non-nil error denotes failure**.

```go
type MyError struct {
	When time.Time
	What string
}

func (e *MyError) Error() string {
	return fmt.Sprintf("at %v, %s",
		e.When, e.What)
}

func run() error {
	return &MyError{
		time.Now(),
		"it didn't work",
	}
}

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
	}
}
```

## Exercise: Errors

Create a new `type ErrNegativeSqrt float64` and make it an `error` by giving it a `func (e ErrNegativeSqrt) Error() string`

Method such that `ErrNegativeSqrt(-2).Error()` returns "cannot Sqrt negative number: -2".

```go
type ErrNegativeSqrt float64

func (e ErrNegativeSqrt) Error() string {
	return fmt.Sprint("cannot Sqrt negative number: ", float64(e))
}

func Sqrt(x float64) (float64, error) {
	if (x < 0) {
		return 0, ErrNegativeSqrt(x)
	}

	z := 1.0
	prev := 0.0
	for i := 0; i < 10 && z - prev >= 1; i++ {
		prev = z
		z -= (z*z - x) / (2*z)
		fmt.Println(prev, z)
	}

	return z, nil
}

func main() {
	fmt.Println(Sqrt(2))
	fmt.Println(Sqrt(-2))
}
```

# Readers

The `io` package specifies the `io.Reader` interface, which represents the read end of a stream of data.

The Go standard library contains many implementations of these interfaces, including files, network connections, compressors, ciphers, and others.

The `io.Reader` interface has a `Read` method:

`func (T) Read(b []byte) (n int, err error)`

`Read` populates the given byte slice with data and returns the number of bytes populated and an `error` value. It returns an `io.EOF` error when the stream ends.

The example code creates a `strings.Reader` and consumes its output 8 bytes at a time.

```go
func main() {
	r := strings.NewReader("Hello, Reader!")

	b := make([]byte, 8)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
}
```

## Exercise: Readers

Implement a Reader type that emits an infinite stream of the ASCII character 'A'.

```go
type MyReader struct{}

func (m MyReader) Read(b []byte) (int, error) {
	for i := range b {
		b[i] = 'A'
	}
	return len(b), nil
}

func main() {
	reader.Validate(MyReader{})
}
```

## Exercise: rot13Reader

A common pattern is an `io.Reader` that wraps another `io.Reader`,
modifying the stream in some way.

For example, `gzip.NewReader` function
takes `io.Reader` (a stream of compressed data)
and returns a `*gzip.Reader` that also implements `io.Reader` (a stream of the decompressed data).

Implement a `rot13Reader` that implements `io.Reader` and reads from an `io.Reader`, modifying the stream by applying the rot13 substitution cipher to all alphabetical characters.

```go
package main

import (
	"fmt"
	"io"
	"os"
	"strings"
)

type rot13Reader struct {
	r io.Reader
}

func rot13(p byte) byte {
	// ??? :(
	return p
}

func (r rot13Reader) Read(p []byte) (n int, err error) {
	fmt.Println(p)
	n, err = r.r.Read(p)
	for i := 0; i < n; i++ {
		p[i] = rot13(p[i])
	}
	return
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
```

# Images

Package `image` defines `Image` interface:

```go
package image

type Image interface {
    ColorModel() color.Model
    Bounds() Rectangle
    At(x, y int) color.Color
}
```

Note: the `Rectangle` return value of the `Bounds` method is actually an `image.Rectangle`, as the declaration is inside package `image`.

The `color.Color` and `color.Model` types are also interfaces, but we'll ignore that by using the predefined implementations `color.RGBA` and `color.RGBAModel`.
These interfaces and types are specified by the `image/color` package

```go
import (
	"fmt"
	"image"
)

func main() {
	m := image.NewRGBA(image.Rect(0, 0, 100, 100))
	fmt.Println(m.Bounds())
	fmt.Println(m.At(0, 0).RGBA())
}
```

## Exercise: Images

Remember the picture generator you wrote earlier? Let's write another one, but this time it will return an implementation of `image.Image` instead of a slice of data.

Define your own `Image` type, implement the necessary methods, and call `pic.ShowImage`.

`Bounds` should return an `image.Rectangle`, like `image.Rect(0, 0, w, h)`.

`ColorModel` should return `color.RGBAModel`.

`At` should return a color; the value `v` in the last picture generator corresponds to `color.RGBA{v, v, 255, 255}` in this one.

```go
package main

import (
	"golang.org/x/tour/pic"

	"image"
	"image/color"
)

type Image struct{}

func (i Image) ColorModel() color.Model {
	return color.RGBAModel
}
func (i Image) Bounds() image.Rectangle {
	return image.Rect(0, 0, 240, 240)
}
func (i Image) At(x, y int) color.Color {
	return color.RGBA{200, 150, 190, 80}
}


func main() {
	m := Image{}
	pic.ShowImage(m)
}
```
