# Pointers

A pointer holds the memory address of a value.

The type `*T` **is a pointer to** a `T` value. **Its zero value is** `nil`.

`var p *int`

The **`&` operator generates a pointer to its operand**.

```go
i := 42
p = &i
```

The **\* operator denotes the pointer's underlying value**.

```go
fmt.Println(*p) // read i through the pointer p
*p = 21         // set i through the pointer p
```

This is known as **"dereferencing" or "indirecting"**.

```go
func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer p
	*p = 21         // set i through the pointer p
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
}
```

# Structs

A `struct` is a **collection of fields**.

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

Struct fields are **accessed using a dot**.

```go
func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

## Pointers to structs

**Struct fields can be accessed by pointers to structs**.

To access the field X of a struct when we have the struct pointer p we could write `(*p).X`.

However, that notation is cumbersome, so the **language permits us instead to write just `p.X`, without the explicit dereference**.

```go
func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9 // not `(*p).X = 1e9`
	fmt.Println(v)
}
```

## Struct Literals

A struct literal denotes a newly allocated struct value by listing the values of its fields.

**You can list just a subset of fields** by using the `Name: syntax`. (And the **order of named fields is irrelevant**.)

The special prefix `&` returns a pointer to the struct value.

```go
var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)
```

# Arrays

The type `[n]T` is an **array of `n` values of type `T`**.

The expression `var a [10]int` declares a variable `a` as an _array of ten integers_.

An array's length is part of its type, so **arrays cannot be resized**.

```go
func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes)
}
```

# Slices

An array has a fixed size.

A slice, on the other hand, is a **dynamically-sized**.

**The type `[]T` is a _slice with elements of type `T`_**.

A slice is formed by specifying two indices, a low and high bound, separated by a colon:

`a[low : high]` - includes the first, excludes the last.

```go
func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s)
}
```

## Slices are references to arrays

Slice does not store data, it describes section of array.

**Changing the elements of a slice modifies the elements of and array and other slices with same underlying array will see those changes.**

```go
func main() {
	names := [4]string{
		"John",
		"Paul",
		"George",
		"Ringo",
	}
	fmt.Println(names)

	a := names[0:2]
	b := names[1:3]
	fmt.Println(a, b)

	b[0] = "XXX"
	fmt.Println(a, b)
	fmt.Println(names)
}
```

## Slice literals

A **slice literal is like an array literal without the length**.

This is an array literal: `[3]bool{true, true, false}`

And this **creates the same array as above, then builds a slice that references it**:

`[]bool{true, true, false}`

```go
func main() {
	q := []int{2, 3, 5, 7, 11, 13}
	fmt.Println(q)

	r := []bool{true, false, true, true, false, true}
	fmt.Println(r)

	s := []struct {
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
		{7, true},
		{11, false},
		{13, true},
	}
	fmt.Println(s)
}
```

## Slice defaults

When slicing, **you may omit the high or low bounds to use their defaults** instead.

The default is **zero for the low** bound and the **length of the slice for the high** bound.

For the array `var a [10]int` these slice expressions are equivalent:

a[0:10]
a[:10]
a[0:]
a[:]

```go
func main() {
	s := []int{2, 3, 5, 7, 11, 13}

	s = s[1:4]
	fmt.Println(s)

	s = s[:2]
	fmt.Println(s)

	s = s[1:]
	fmt.Println(s)
}
```

## Slice length and capacity

A **slice has both a length and a capacity**.

The **length of a slice is the number of elements it contains**.

The **capacity is the number of elements in the underlying array, counting from first element in slice**.

The length and capacity of a slice s can be obtained using the expressions `len(s)` and `cap(s)`.

**You can extend a slice's length** by _re-slicing_ it **if it has sufficient capacity**.

```go
func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)

	// Extend its length.
	s = s[:4]
	printSlice(s)

	// Drop its first two values.
	s = s[2:]
	printSlice(s)
}
```

## Nil slices

The **zero value of a slice is `nil`**.

A `nil` slice has a **length and capacity of 0** and has **no underlying array**.

```go
func main() {
	var s []int
	fmt.Println(s, len(s), cap(s))
	if s == nil {
		fmt.Println("nil!")
	}
}
```

## Creating a slice with make

This is how you create dynamically-sized arrays.
The `make` function allocates a **zeroed array** and returns a **slice that refers to that array**:

```go
a := make([]int, 5)    // len(a)=5
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)]         // len(b)=5, cap(b)=5
b = b[1:]              // len(b)=4, cap(b)=4
```

## Slices of slices

Slices can contain any type

```go
board := [][]string{
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
}
```

## Appending to a slice

`func append(s []T, vs ...T) []T`

First parameter `s` of append is a slice of type `T`, and the rest are `T` values to append to the slice.

The resulting value of append is a slice containing all the elements of the original slice plus the provided values.

**If backing array too small, bigger array allocated. Returned slice point to newly array**.

```go
func main() {
	var s []int            // len=0 cap=0 []
	s = append(s, 0)       // len=1 cap=2 [0]
	s = append(s, 1)       // len=2 cap=2 [0 1]
	s = append(s, 2, 3, 4) // len=5 cap=8 [0 1 2 3 4]
}
```

# Range

`range` is `for` loop over a `slice` or `map`.

```go
var pow = []int{1, 2, 4, 8, 16, 32}
for i, v := range pow {}
```

## Range continued

You can skip the index or value by assigning to `_`.

```go
for i, _ := range pow
for _, value := range pow
```

If you only want the index, you can omit the second variable.

`for i := range pow`

## Exercise: Slices

```go
package main

import "golang.org/x/tour/pic"

func Pic(dx, dy int) [][]uint8 {
	s := make([][]uint8, dy)

	for i := range s {
		s[i] = make([]uint8, dx)
		for j := range s[i] {
			s[i][j] = uint8(i + j)
		}
	}

	return s
}

func main() {
	pic.Show(Pic)
}
```

# Maps

A `map` **maps keys to values**.

```go
type Vertex struct {
	Lat, Long float64
}

// Zero value of a map is nil. A nil map has no keys, nor can keys be added
var m map[string]Vertex

func main() {
	// The make function returns a map of the given type, initialized and ready for use.
	m = make(map[string]Vertex)

	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}

	fmt.Println(m["Bell Labs"]) // {40.68433, -74.39967}
}
```

## Map literals

Map literals are like struct literals, but the **keys are required**.

```go
type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
```

If the top-level type is just a type name, you can omit it from the elements of the literal.

```go
var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
```

## Mutating Maps

Insert or update an element in map m: `m[key] = elem`

Retrieve an element: `elem = m[key]`

Delete an element: `delete(m, key)`

Test that a key is present with a two-value assignment: `elem, ok = m[key]`

If `key` is in `m`, `ok` is `true`. If not, `ok` is `false`.

If `key` is not in the `map`, then `elem` is the zero value for the map's element type.

You could use a short declaration form: `elem, ok := m[key]`

```go
func main() {
	m := make(map[string]int)
	// m["Answer"] is 0

	m["Answer"] = 42
	// m["Answer"] is 42

	m["Answer"] = 48
	// m["Answer"] is 48

	delete(m, "Answer")
	// m["Answer"] is 0

	v, ok := m["Answer"]
	// v is 0, ok is false
}
```

## Exercise: Maps

Implement `WordCount`.

It should return a `map` of the counts of each “word” in the string `s`.

The `wc.Test` function runs a test suite against the provided function and prints success or failure.

```go
package main

import (
	"strings"
	"golang.org/x/tour/wc"
)

func WordCount(s string) map[string]int {
	result := make(map[string]int)

	for _, word := range strings.Fields(s) {
		count, duplicate := result[word]
		var newCount int
		if duplicate {
			newCount = count + 1
		} else {
			result[word] = 1
		}
		result[word] = newCount
	}

	return result
}

func main() {
	wc.Test(WordCount)
}
```

# Function values

Functions are values too. **They can be passed around** just like other values.

Function values **may be used as function arguments and return values**.

```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}

	fmt.Println(hypot(5, 12))      // math.Sqrt(169) = 13
	fmt.Println(compute(hypot))    // math.Sqrt(25)  = 5
	fmt.Println(compute(math.Pow)) // math.Pow(3, 4) = 81
}
```

## Function closures

Go functions may be closures.

A **closure is a function that references variables from outside its body**.

The function may access and assign to the referenced variables; in this sense the **function is "bound" to the variables**.

For example, the adder function returns a closure. Each closure is bound to its own sum variable.

```go
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```

## Exercise: Fibonacci closure

```go
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	prevA := 0
	prevB := 1
	current := 0
	return func() int {
		current = prevA + prevB
		prevA = prevB
		prevB = current

		return current
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```
