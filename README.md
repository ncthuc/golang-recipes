# Golang Recipes
[Golang Recipes](https://github.com/ncthuc/golang-recipes)

[TOC]

# Basic syntax

## Data types

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32 represents a Unicode code point

float32 float64

complex64 complex128
```

The `int`, `uint`, and `uintptr` types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems.

## Formatted I/O - package `fmt`

### Print using default formaters
- The bery basic 
```go
import "fmt"
func main() {
	fmt.Println("Hello, Golang")
}
```

- `Sprint` & `Sprintln` - print list of operands to a string using default formats. With `Sprint`, spaces are added between operands when neither is a string. In case of `Sprintln`, spaces are always added between operands and a newline is appended.

```go
const name, age = "Kim", 22
s := fmt.Sprint(name, " is ", age, " years old.\n")
s2 := fmt.Sprintln(name, "is", age, "years old.")
fmt.Print(s)
fmt.Print(s2)
```
Output:
```shell
Kim is 22 years old.
Kim is 22 years old.  

```
### Print using format specfiers
- Format specifier verbs
    - `%v` - the value in a default format when printing structs, the plus flag (`%+v`) adds field names
    - `%T` - a Go-syntax representation of the type of the value
    - `%d`, `%4d`  (right justified), `%-4d` (left justified), `%04d` - integer
    - `%s` - string
    - `%f`, `%.2f`, `9.2%f` - float
    - `%t` - bool
    - `%q` - a double-quoted string safely escaped with Go syntax


- `Sprintf` - print to a string using a format specifier
```go
const name, age = "Kim", 22
s := fmt.Sprintf("%s is %d years old.\n", name, age)
```

- `Printf` - print with format specifier 

## Variables
### Declare
Using `var` statement, can be at package or function level.
```go
var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

### Decalare with initializers
The type can be omitted.
```go
var i, j int = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

### Short variable declarations inside function with `:=`
```go
func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

## Conditional statements
### The basic `if-else` statement
The condition expression need not be surrounded by parentheses ( ) but the braces { } are required.
```go
func sqrt(x float64) string {
    if x < 0 {
        return sqrt(-x) + "i"
    } else {
        return fmt.Sprint(math.Sqrt(x))
    }
}
```

### `if` statement with short initialization statement
Variables declared by the statement are only in scope until the end of the if.
Go's switch cases need NOT be constants, and the values involved need NOT be integers. 

In Go, `break` statement is provided AUTOMATICALLY at the end of each case.

```go
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}
```

### switch statement - the basic
Switch cases evaluate cases from top to bottom, stopping when a case succeeds.
```go
fmt.Print("Go runs on ")
switch os := runtime.GOOS; os {
    case "darwin":
        fmt.Println("OS X.")
    case "linux":
        fmt.Println("Linux.")
    default:
        // freebsd, openbsd,
        // plan9, windows...
        fmt.Printf("%s.\n", os)
}
```

### switch with no condition
```go
t := time.Now()
switch {
    case t.Hour() < 12:
        fmt.Println("Good morning!")
    case t.Hour() < 17:
        fmt.Println("Good afternoon.")
    default:
        fmt.Println("Good evening.")
}
```

## Go loop

### The basic
Unlike other languages like C, Java, or JavaScript there are no parentheses surrounding the three components of the for statement and the braces { } are always required.

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
fmt.Println(sum)

```

The init and post statements are optional.

```go
func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}

```

### `for` used as `while`
```go
sum := 1
for sum < 1000 {
    sum += sum
}
fmt.Println(sum)
```

Forever

```go
for {
}
```

### `for-each` range loop
Looping over elements in slices, arrays, maps, channels or strings is often better done with a range loop.

```go
strs := []string{"hello", "world"}
for i, s := range strs {
    fmt.Println(i, s)
}
```

Ignore indices:
```go
strs := []string{"hello", "world"}
for _, s := range strs {
    fmt.Println(s)
}
```

Ignore values:
```go
strs := []string{"hello", "world"}
for i := range strs {
    fmt.Println(i)
}
```

# Go functions
## The basic
```go
func add(x, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

## Return multiple results 
```go
func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

## Named return values
```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

## Variadic Functions
```go
func sum(nums ...int) int {
	total := 0
	for _, num := range nums {
		total += num
	}
	return total
}

func max(nums ...int) int {
	vmax := nums[0]
	for _, num := range nums {
		if num > vmax {
			vmax = num
		}
	}
	return vmax
}

func min(nums ...int) int {
	vmin := nums[0]
	for _, num := range nums {
		if num < vmin {
			vmin = num
		}
	}
	return vmin
}

func main() {
	fmt.Println(sum(2, 3, 5))
	fmt.Println(max(2, 3, 5))
	a := []int{2, 3, 5, 8}
	fmt.Println(sum(a...))
	fmt.Println(max([]int{3, 5, 8, 2}...))
	fmt.Println(min([]int{3, 5, 8, 2}...))
}
```

## defer

Defer is used to ensure that a function call is performed later in a program’s execution, usually for purposes of cleanup.

```go
func main() {
    f := createFile("/tmp/defer.txt")
    defer closeFile(f)
    writeFile(f)
}
func createFile(p string) *os.File {
    fmt.Println("creating")
    f, err := os.Create(p)
    if err != nil {
        panic(err)
    }
    return f
}
func writeFile(f *os.File) {
    fmt.Println("writing")
    fmt.Fprintln(f, "data")
}

func closeFile(f *os.File) {
    fmt.Println("closing")
    err := f.Close()
    if err != nil {
        fmt.Fprintf(os.Stderr, "error: %v\n", err)
        os.Exit(1)
    }
}

```

## Stacking defer
Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

```go
func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
```

Output:
```shell 
done
9
8
7
6
5
4
3
2
1
0
```

## Closures

A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the `adder` function returns a closure. Each closure is bound to its own `sum` variable.

```go 
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 5; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```

Ouput:

```
0 0
1 -2
3 -6
6 -12
10 -20
```

Below is a `fibonacci` function that returns a function (a closure) that returns successive fibonacci numbers (0, 1, 1, 2, 3, 5, ...).

```go 
package main

import "fmt"

func fibonacci() func() int {
	f1 := 0
	f2 := 1
	return func() int {
		f2 = f1 + f2
		f1 = f2 - f1
		return f1
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```

## Useful functions

### Min Max Sum

```go
func sum(nums ...int) int {
	total := 0
	for _, num := range nums {
		total += num
	}
	return total
}

func max(nums ...int) int {
	vmax := nums[0]
	for _, num := range nums {
		if num > vmax {
			vmax = num
		}
	}
	return vmax
}

func min(nums ...int) int {
	vmin := nums[0]
	for _, num := range nums {
		if num < vmin {
			vmin = num
		}
	}
	return vmin
}

// Usage:
func main() {
	fmt.Println(sum(2, 3, 5))
	fmt.Println(max(2, 3, 5))
	a := []int{2, 3, 5, 8}
	fmt.Println(sum(a...))
	fmt.Println(max([]int{3, 5, 8, 2}...))
	fmt.Println(min([]int{3, 5, 8, 2}...))
}
```

### GCD

```go
func Gcd(a, b int) int {
    for b != 0 {
        a, b = b, a % b
    }
    return a
}
```

### Number of combinations

```go
func Combination(n, k int) int {
	r := 1
	for i := n-k+1; i<=n; i++ {
		r *= i
	}

	for i := 2; i<=k; i++ {
		r /= i
	}

	return r
}
```

### Prime check

```go
func IsPrime(n int) bool {
	if n < 2 { return false }
	for i := 2; i * i <= n; i++ {
		if n % i == 0 {return false}
	}
	return true
}
```

### Fibonacci

```go
func fibonacci(n int) int {
	f1 := 1
	f2 := 1
	for i := 1; i < n; i++ {
		f1, f2 = f2, f1 + f2
	}
	return f1
}
```

### Reverse string

```go
func Reverse(s string) string {
	r := ""
	for _, c := range s {
		r = string(c) + r
	}
	return r
}
```

### Reverse array/slice

```go
func ReverseInline(a []int) []int {
	l := 0
	r := len(a)-1
	for l < r {
		a[l], a[r] = a[r], a[l]
		l++
		r--
	}
	return a
}

func Reverse(a []int) []int {
	n := len(a)
	r := make([]int, n)
	for i, v := range a {
		r[n-1-i] = v
	}
	return r
}
```

# Go strings

## Strings, bytes, runes and characters
In Go, a string is in effect a read-only slice of bytes. 

```go
const World = "world"
var hello = "hello"

// Concatenate strings.
var helloWorld = hello + " " + World
helloWorld += "!"
fmt.Println(helloWorld) // hello world!

// slice
fmt.Println(helloWorld[3:8]) // lo wo

// Compare strings.
fmt.Println(hello == "hello")   // true
fmt.Println(hello > helloWorld) // false
```

## String funtions
The standard library’s `strings` package provides many useful string-related functions.

Samples:

```go	
import (
    "fmt"
    s "strings"
)

var p = fmt.Println
func main() {
	p("Contains:  ", s.Contains("test", "es"))
	p("Count:     ", s.Count("test", "t"))
	p("HasPrefix: ", s.HasPrefix("test", "te"))
	p("HasSuffix: ", s.HasSuffix("test", "st"))
	p("Index:     ", s.Index("teest", "e"))
	p("Last index:", s.LastIndex("teest", "e"))
	p("Count:     ", s.Count("teest", "e"))
	p("Contains:  ", s.Contains("teest", "ee"))
	p("Join:      ", s.Join([]string{"a", "b"}, "-"))
	p("Split:     ", s.Split("a-b-c-d-e", "-"))
	p("Repeat:    ", s.Repeat("a", 5))
	p("Replace:   ", s.Replace("foo", "o", "0", 1))
	p("Replace:   ", s.Replace("foo", "o", "0", -1))
	p("ToLower:   ", s.ToLower("TEST"))
	p("ToUpper:   ", s.ToUpper("test"))
	p("TrimSpace: ", s.TrimSpace(" \n\n\n\n  test  "))
	p("Len: ", len("hello"))
	p("Char:", "hello"[1])
}
```

Output:

```shell
Contains:   true
Count:      2
HasPrefix:  true
HasSuffix:  true
Index:      1
Last index: 2
Count:      2
Contains:   true
Join:       a-b
Split:      [a b c d e]
Repeat:     aaaaa
Replace:    f0o
Replace:    f00
ToLower:    test
ToUpper:    TEST
TrimSpace:  test
Len:  5
Char: 101
```

# Data type conversion

## Between basic types

```go
var m int = 123
var n int64 = int64(m)
s := "string"
fmt.Println(n)                    // 123
fmt.Println(len(string(s[0])))    // 1
```

## int/int64 to string

Using `strconv.Itoa`:

```go
import (
	"fmt"
	"strconv"
)

func main() {
	fmt.Println(strconv.Itoa(97))  // "97"
}
```

Using `strconv.FormatInt`:
```go
package main

import (
	"fmt"
	"reflect"
	"strconv"
)

func main() {
	var i int64 = 1234
	s := strconv.FormatInt(i, 10)
	fmt.Println(s)  //1234
	fmt.Println(strconv.FormatInt(i, 2))  //10011010010
	fmt.Println(reflect.TypeOf(s)) //string
}
```

## string to int/int64

Using `strconv.Atoi`:

```go
s := "97"
n, _ := strconv.Atoi(s)
fmt.Println(n+1)  // 98
```

With error handling:

```go
s := "97"
if n, err := strconv.Atoi(s); err == nil {
    fmt.Println(n+1)
} else {
    fmt.Println(s, "is not an integer.")
}
// Output: 98
```

**Warning**: `string(97)` return `"a"`

Using `strconv.ParseInt`:

```go
package main

import (
	"fmt"
	"reflect"
	"strconv"
)

func main() {
	var s string = "1234"
	i, err := strconv.ParseInt(s,10,64)
	if err != nil {
		panic(err)
	}
	fmt.Println(i) //1234
	fmt.Println(reflect.TypeOf(i)) //int64
}
```

# Regular Expressions

[Regular Expressions](https://gobyexample.com/regular-expressions)
TBD

# Arrays and slices

## Arrays

```go
var a [5]int
fmt.Println("emp:", a)

a[4] = 100
fmt.Println("set:", a)
fmt.Println("get:", a[4])

fmt.Println("len:", len(a))

// declare and initialize
b := [5]int{1, 2, 3, 4, 5}
fmt.Println("dcl:", b)

// Have the compiler count the array elements
c := [...]string{"Penn", "Teller"}
fmt.Printf("%v %T", c, c)  // [Penn Teller] [2]string

d := [...]string{
    "Penn", 
    "Teller",        // trailing comma is required here
}
fmt.Printf("%v %T", d, d)  

// multi-dimensional
var twoD [2][3]int
for i := 0; i < 2; i++ {
    for j := 0; j < 3; j++ {
        twoD[i][j] = i + j
    }
}
fmt.Println("2d: ", twoD)
```

Output

```shell
emp: [0 0 0 0 0]
set: [0 0 0 0 100]
get: 100
len: 5
dcl: [1 2 3 4 5]
2d:  [[0 1 2] [1 2 3]]
```

## Slices

```go
// A slice literal is declared just like an array literal, except you leave out the element count:
letters := []string{"a", "b", "c", "d"}
fmt.Printf("%v %T", letters, letters)  // [a b c d] []string

s := make([]string, 3)
fmt.Println("emp:", s)

s[0] = "a"
s[1] = "b"
s[2] = "c"
fmt.Println("set:", s)
fmt.Println("get:", s[2])

fmt.Println("len:", len(s))

s = append(s, "d")
s = append(s, "e", "f")
fmt.Println("apd:", s)

c := make([]string, len(s))
copy(c, s)
fmt.Println("cpy:", c)

l := s[2:5]
fmt.Println("sl1:", l)

l = s[:5]
fmt.Println("sl2:", l)

l = s[2:]
fmt.Println("sl3:", l)

// declare and initialize 
t := []string{"g", "h", "i"}
fmt.Println("dcl:", t)

// multi-dimensional slice, the length of the inner slices can vary
twoD := make([][]int, 3)
for i := 0; i < 3; i++ {
    innerLen := i + 1
    twoD[i] = make([]int, innerLen)
    for j := 0; j < innerLen; j++ {
        twoD[i][j] = i + j
    }
}
fmt.Println("2d: ", twoD)
```

Output:

```shell
$ go run slices.go
emp: [  ]
set: [a b c]
get: c
len: 3
apd: [a b c d e f]
cpy: [a b c d e f]
sl1: [c d e]
sl2: [a b c d e]
sl3: [c d e f]
dcl: [g h i]
2d:  [[0] [1 2] [2 3 4]]
```

## Slide tricks

https://github.com/golang/go/wiki/SliceTricks

### Append a vector
```go
a = append(a, b...)
```

### Copy
```go
b = make([]T, len(a))
copy(b, a)
// or
b = append([]T(nil), a...)
// or
b = append(a[:0:0], a...)  // See https://github.com/go101/go101/wiki
```

### Cut
```go
a = append(a[:i], a[j:]...)
```

To avoid memory leak:
```go
copy(a[i:], a[j:])
for k, n := len(a)-j+i, len(a); k < n; k++ {
	a[k] = nil // or the zero value of T
}
a = a[:len(a)-j+i]
```

### Delete
```go
a = append(a[:i], a[i+1:]...)
// or
a = a[:i+copy(a[i:], a[i+1:])]
```

To avoid memory leak:
```go
copy(a[i:], a[i+1:])
a[len(a)-1] = nil // or the zero value of T
a = a[:len(a)-1]
```

### Expand
```go 
a = append(a[:i], append(make([]T, j), a[i:]...)...)
```

### Extend
```go
a = append(a, make([]T, j)...)
```

### Filter (in place)
```go
n := 0
for _, x := range a {
	if keep(x) {
		a[n] = x
		n++
	}
}
a = a[:n]
```

### Insert
```go
a = append(a[:i], append([]T{x}, a[i:]...)...)
```

or:

```go
s = append(s, 0 /* use the zero value of the element type */)
copy(s[i+1:], s[i:])
s[i] = x
```

### Insert a vector

```go
a = append(a[:i], append(b, a[i:]...)...)
```

### Push
```go
a = append(a, x)
```

### Pop
```go
x, a = a[len(a)-1], a[:len(a)-1]
```

### Push front / unshift
```go
a = append([]T{x}, a...)
```

### Pop front/Shift
```go
x, a = a[0], a[1:]
```

### Reversing
```go
for left, right := 0, len(a)-1; left < right; left, right = left+1, right-1 {
	a[left], a[right] = a[right], a[left]
}
```

Or:
```go
for i := len(a)/2-1; i >= 0; i-- {
	opp := len(a)-1-i
	a[i], a[opp] = a[opp], a[i]
}
```

### Batching with minimal allocation
```go
actions := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
batchSize := 3
batches := make([][]int, 0, (len(actions) + batchSize - 1) / batchSize)

for batchSize < len(actions) {
    actions, batches = actions[batchSize:], append(batches, actions[0:batchSize:batchSize])
}
batches = append(batches, actions)
```

Yields the following:
```shell
[[0 1 2] [3 4 5] [6 7 8] [9]]
```

## Sorting & Searching

Using `sort` package.

### Sort a slice of ints, float64s or strings

Using:

- `sort.Ints`
- `sort.Float64s`
- `sort.Strings`

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	s := []int{4, 2, 3, 1}
	sort.Ints(s)
	fmt.Println(s) // [1 2 3 4]
}
```

### Check if a slice is sorted

- `sort.IntsAreSorted(a []int) bool`
- `sort.Float64sAreSorted(a []float64) bool`
- `sort.StringsAreSorted(a []string) bool`

### Custom comparator

- `sort.Slice(slice interface{}, less func(i, j int) bool)`
- `sort.SliceStable(slice interface{}, less func(i, j int) bool)`
- `sort.SliceIsSorted(slice interface{}, less func(i, j int) bool) bool`

```go
package main

import (
	"fmt"
	"sort"
)

func main() {

	people := []struct {
		Name string
		Age  int
	}{
		{"Alice", 25},
		{"Elizabeth", 75},
		{"Alice", 75},
		{"Bob", 75},
		{"Alice", 75},
		{"Bob", 25},
		{"Colin", 25},
		{"Elizabeth", 25},
	}

	// Sort by name, preserving original order
	sort.SliceStable(people, func(i, j int) bool { return people[i].Name < people[j].Name })
	fmt.Println("By name:", people)

	// Sort by age preserving name order
	sort.SliceStable(people, func(i, j int) bool { return people[i].Age < people[j].Age })
	fmt.Println("By age,name:", people)

}
```

### Generic type
Type `sort.Interface`:

```go
type Interface interface {
    // Len is the number of elements in the collection.
    Len() int
    // Less reports whether the element with
    // index i should sort before the element with index j.
    Less(i, j int) bool
    // Swap swaps the elements with indexes i and j.
    Swap(i, j int)
}
```

- `sort.Sort(data Interface)`: Sort sorts data. It makes one call to data.Len to determine n, and O(n*log(n)) calls to data.Less and data.Swap. The sort is not guaranteed to be stable.
- `sort.Stable(data Interface)`: Stable sorts data while keeping the original order of equal elements.
- `sort.IsSorted(data Interface) bool`: reports whether data is sorted.

### Searching 

TBD https://golang.org/pkg/sort/#SearchInts

# Maps

```go
package main

import "fmt"

func main() {
	// create an empty map
	m := make(map[string]int)

	// set key
	m["k1"] = 7
	m["k2"] = 13

	fmt.Println("map:", m)
	// get key
	v1 := m["k1"]
	fmt.Println("v1: ", v1)
	// len: number of key-value pair
	fmt.Println("len:", len(m))
	// delete a key
	delete(m, "k2")
	fmt.Println("map:", m)
	// check if a key is present
	_, ok := m["k2"]
	fmt.Println("present:", ok)
	// declare and initialize at the same time
	n := map[string]int{"foo": 1, "bar": 2}
	fmt.Println("map:", n)
}
```

# Struct & pointer

## Pointer
Go has pointers. A pointer holds the memory address of a value.

The type `*T` is a pointer to a `T` value. Its zero value is `nil`.

The `&` operator generates a pointer to its operand.

The `*` operator denotes the pointer's underlying value.

```go
func zeroval(ival int) {
	ival = 0
}

func zeroptr(iptr *int) {
	*iptr = 0
}
func main() {
	i := 1
	fmt.Println("initial:", i)
	zeroval(i)
	fmt.Println("zeroval:", i)
	zeroptr(&i)
	fmt.Println("zeroptr:", i)
	fmt.Println("pointer:", &i)
}
```

Output:

```shell
$ go run pointers.go
initial: 1
zeroval: 1
zeroptr: 0
pointer: 0x42131100
```

## Structs
Go’s structs are typed collections of fields. 

You can safely return a pointer to local variable as a local variable will survive the scope of the function.

You can name the fields when initializing a struct. Omitted fields will be zero-valued.

Access struct fields with a dot. You can also use dots with struct pointers - the pointers are automatically dereferenced.

```go
type person struct {
	name string
	age  int
}

func newPerson(name string) *person {
	p := person{name: name}
	p.age = 42
	return &p
}

func main() {
	fmt.Println(person{"Bob", 20})
	fmt.Println(person{name: "Alice", age: 30})
	fmt.Println(person{name: "Fred"})
	fmt.Println(&person{name: "Ann", age: 40})
	fmt.Println(newPerson("Jon"))

	s := person{name: "Sean", age: 50}
	fmt.Println(s.name)

	sp := &s
	fmt.Println(sp.age)

	sp.age = 51
	fmt.Println(sp.age)
}
```

```shell
$ go run test.go 
{Bob 20}
{Alice 30}
{Fred 0}
&{Ann 40}
&{Jon 42}
Sean
50
51
```

## Methods

Go supports *methods* defined on struct types.

This `area` method has a *receiver* type of `*rect`.

Methods can be defined for either pointer or value receiver types. Go automatically handles conversion between values and pointers for method calls. You may want to use a pointer receiver type to **avoid copying** on method calls or to allow the method to **mutate** the receiving struct.

```go
package main

import "fmt"

type rect struct {
    width, height int
}

func (r *rect) area() int {
    return r.width * r.height
}

func (r rect) perim() int {
    return 2*r.width + 2*r.height
}

func main() {
    r := rect{width: 10, height: 5}

    fmt.Println("area: ", r.area())
    fmt.Println("perim:", r.perim())

    rp := &r
    fmt.Println("area: ", rp.area())
    fmt.Println("perim:", rp.perim())
}
```

```shell
$ go run methods.go 
area:  50
perim: 30
area:  50
perim: 30
```

## Interfaces

Interfaces are named collections of method signatures.

To implement an interface in Go, we just need to **implement all the methods** in the interface. 

If a variable has an interface type, then we can call methods that are in the named interface. 

```go
package main

import (
    "fmt"
    "math"
)

type geometry interface {
    area() float64
    perim() float64
}

type rect struct {
    width, height float64
}
type circle struct {
    radius float64
}

func (r rect) area() float64 {
    return r.width * r.height
}
func (r rect) perim() float64 {
    return 2*r.width + 2*r.height
}

func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}
func (c circle) perim() float64 {
    return 2 * math.Pi * c.radius
}

func measure(g geometry) {
    fmt.Println(g)
    fmt.Println(g.area())
    fmt.Println(g.perim())
}

func main() {
    r := rect{width: 3, height: 4}
    c := circle{radius: 5}

    measure(r)
    measure(c)
}
```

```shell
$ go run interfaces.go
{3 4}
12
14
{5}
78.53981633974483
31.41592653589793
```

### Type assertions

TBD

# Reuse code in Go (composition vs inheritance)

TBD

https://www.jawahar.tech/blog/golang-inheritance-vs-composition

# Goroutines

## The basic

A goroutine is a lightweight thread managed by the Go runtime.

```go
package main

import (
	"fmt"
	"time"
)

func f(from string) {
	for i := 0; i < 3; i++ {
		fmt.Println(from, ":", i)
		time.Sleep(100 * time.Millisecond)
	}
}

func main() {
	// usual way, running synchronously
	f("direct")
	// invoke this function in a goroutine, f(s) will execute concurrently with the calling one
	go f("goroutine")
	// another function call. This will run asynchronously in separate goroutines with the above routine
	go func(msg string) {
		for i := 0; i < 3; i++ {
			fmt.Println(msg)
			time.Sleep(100 * time.Millisecond)
		}
	}("going")
	// Wait for them to finish (for a more robust approach, use a WaitGroup).
	time.Sleep(time.Second)
	fmt.Println("done")
}
```

Output:

```
direct : 0
direct : 1
direct : 2
going
goroutine : 0
goroutine : 1
going
going
goroutine : 2
done
```

## Channels


*Channels* are the pipes that connect concurrent goroutines. You can send values into channels from one goroutine and receive those values into another goroutine.

Create a channel:

```go
ch := make(chan int)
```

Send and receive values to/from channel:

```go
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and assign value to v.
```

Example: distributed sum of array:

```go
package main

import "fmt"

func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // send sum to c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)  // -5 17 12
}

```

## Buffered channel

Channels can be buffered:

```go
ch := make(chan int, 100)
```

Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.

Dead-lock example:

```go
func main() {
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	ch <- 3
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

Working:
```go
func main() {
	ch := make(chan int, 2)
	ch <- 1
	fmt.Println(<-ch)
	ch <- 2
	ch <- 3
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}

```

## Channel synchronization

We can use channels to synchronize execution across goroutines. Here’s an example of using a blocking receive to wait for a goroutine to finish.

```go
package main

import (
	"fmt"
	"time"
)

func worker(done chan bool) {
	fmt.Print("working...")
	time.Sleep(time.Second)
	fmt.Println("done")

	done <- true
}

func main() {

	done := make(chan bool, 1)
	go worker(done)

        // Block until we receive a notification 
        // from the worker on the channel.
        // without this, nothing will be printed out
	<-done
}
```

## Range & close a channel

A sender can close a channel to indicate that no more values will be sent.
The loop `for i := range c` receives values from the channel repeatedly until it is closed. 

Receivers can test whether a channel has been closed:

```go
v, ok := <-ch
```

Sample:

```go
package main

import (
	"fmt"
)

func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Print(i, " ")
	}
    
}

// Output: 0 1 1 2 3 5 8 13 21 34 

```

## Channel direction

```go
package main

import "fmt"

// pings is used for sending only
func ping(pings chan<- string, msg string) {  
    pings <- msg
}


// pings is used for receiving, pongs is for sending
func pong(pings <-chan string, pongs chan<- string) {
    msg := <-pings
    pongs <- msg
}

func main() {
    pings := make(chan string, 1)
    pongs := make(chan string, 1)
    ping(pings, "passed message")
    pong(pings, pongs)
    fmt.Println(<-pongs)
}
```

## Select 

The `select` statement lets a goroutine wait on multiple communication operations. 

A `select` **blocks** until **one** of its cases can run, then it executes that case. It chooses one at random if multiple are ready.

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Print(<-c, " ")
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}

// Output: 0 1 1 2 3 5 8 13 21 34 quit
```

The default case in a select is run if no other case is ready.

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
}
```

# GRPC 

## Go - GRPC
https://developers.google.com/protocol-buffers/docs/gotutorial

## GRPC

https://developers.google.com/protocol-buffers/docs/overview

https://developers.google.com/protocol-buffers/docs/proto3


# Awesome Go

### gocloud.dev
- [The Go Cloud Development Kit](https://gocloud.dev/) 

# Other tools

### [direnv](https://direnv.net/) 
Load and unload environment variables depending on the current directory (from `.envrc` file).

- Install:
```shell
curl -sfL https://direnv.net/install.sh | bash
```

- Hook into the `bash` shell (for more shells, see [here](https://direnv.net/docs/hook.html)): add the following line at the end of the `~/.bashrc` file:
```shell
eval "$(direnv hook bash)"
```

- Alllow direnv:

```shell
direnv allow
```