# For

**Go has only one looping construct**, the for loop.

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

The basic for loop has three components separated by semicolons:

- the init statement: executed **before the first iteration** `i := 0`
- the condition expression: evaluated **before every iteration** `i < 10`
- the post statement: executed **at the end of every iteration** `i++`

There are **no parentheses** surrounding the three components of the for statement and the braces **{ } are always required**

## Condition only

The **init and post statements are optional**.

```go
sum := 1
for ; sum < 1000; {
    sum += sum
}
```

**At that point you can drop the semicolons**: C's while is spelled for in Go

```go
sum := 1
for sum < 1000 { // this is `while`
    sum += sum
}
```

## Infinite loop

**If you omit the loop condition it loops forever**, so an infinite loop is compactly expressed.

```go
for {
}
```

# If

Go's if statements are like its for loops;

the **expression need not be surrounded by parentheses ( ) but the braces { } are required**.

```go
func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return math.Sqrt(x)
}
```

## If with a short statement

Like for, the **if statement can start with a short statement to execute before the condition**.

Variables declared by the statement are **only in scope until the end of the if**.

```go
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}
```

## If and else

Variables declared inside an if short statement are **also available inside any of the else** blocks.

```go
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	return lim // can't use v here, though
}
```

# Switch

Go **only runs the selected case**, not all the cases that follow.

Switch cases evaluate cases **from top to bottom, stopping when a case succeeds**.

```go
switch os := runtime.GOOS; os {
case "darwin":
    fmt.Println("OS X.")
case "linux":
    fmt.Println("Linux.")
default:
    fmt.Printf("%s.\n", os) // freebsd, openbsd,
}
```

Does not call `f` if `i==0`:

```go
switch i {
case 0:
case f():
}
```

## Switch with no condition

Switch without a condition is the **same as `switch true`**.

```go
switch {
case t.Hour() < 12:
    fmt.Println("Good morning!")
case t.Hour() < 17:
    fmt.Println("Good afternoon.")
default:
    fmt.Println("Good evening.")
}
```

# Defer

Defers the execution of a function until the surrounding function returns.

The deferred **call's arguments are evaluated immediately**, but the **function call is not executed until the surrounding function returns**.

```go
func main() {
	defer fmt.Println("world")
	fmt.Println("hello")
}
```

## Stacking defers

Deferred function calls are pushed onto a **stack**.

When a function returns, its deferred calls are executed in **last-in-first-out** order.

```go
// counting, done, 9...0
func main() {
	fmt.Println("counting")
	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}
	fmt.Println("done")
}
```
