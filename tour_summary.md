# Golang cheat sheet

Summarizes some of the concepts and some of the code from the go-tour (https://tour.golang.org/welcome/1), along with some of my own additions.

## Variables 
```
var ret string
var ret string = ?foo?
ret := ?foo?
```
## If-else if-else
```
if x == 50 {
    ret = "Germany"
} else if x == 100 {
    ret = "Japan"
} else {
    ret = "Canada"
}
```
* ?else? needs to be on the same line as previous closing curly bracket

## switch 
```
package main

import (
    "fmt"
    "runtime"
)

func main() {
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
}
```

* switch <=> variable to pose condition on
* case <=>  if/if-else
* default <=> else

## defer

* defer statements run after the current functions has finished
* defer statements are placed in a stack, so they are executed in a First-in-last-out manner

## pointers

func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
}

## structs

```
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

## slices 

```
package main

import "fmt"

func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s)
}
```

* An array has a fixed size. A slice, on the other hand, is a dynamically-sized, flexible view into the elements of an array. In practice, slices are much more common than arrays
* A slice does not store any data, it just describes a section of an underlying array.
* Changing the elements of a slice modifies the corresponding elements of its underlying array.
* Other slices that share the same underlying array will see those changes.

### Slice literals 

```
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

* The length of a slice is the number of elements it contains.

* The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

* The length and capacity of a slice s can be obtained using the expressions len(s) and cap(s).


### Dynamically sized arrays

```
func main() {
	a := make([]int, 5)
	printSlice("a", a)

	b := make([]int, 0, 5)
	printSlice("b", b)

	c := b[:2]
	printSlice("c", c)

	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

* Slices can be created with the built-in make function; this is how you create dynamically-sized arrays.

* The make function allocates a zeroed array and returns a slice that refers to that array:

``` a := make([]int, 5)  // len(a)=5 ```

* To specify a capacity, pass a third argument to make:

```
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)] // len(b)=5, cap(b)=5
b = b[1:]      // len(b)=4, cap(b)=4
```

### Appending to a slice

```
func main() {
	var s []int
	printSlice(s)

	// append works on nil slices.
	s = append(s, 0)
	printSlice(s)

	// The slice grows as needed.
	s = append(s, 1)
	printSlice(s)

	// We can add more than one element at a time.
	s = append(s, 2, 3, 4)
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

* The resulting value of append is a slice containing all the elements of the original slice plus the provided values.

* If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

* https://blog.golang.org/go-slices-usage-and-internals

### Range
```
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
```

* When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a copy of the element at that index.

* If you only want the index, you can omit the second variable.

``` for i := range pow ```

### Slices Exercise
```
package main

import(
	"golang.org/x/tour/pic"
)

func multSum(x,y,a int) uint8 {
	return uint8(a*(x+y))
}

func Pic(dx, dy int) [][]uint8 {

	var ret [][]uint8
	var a int
	var xy uint8
	a = 2
	
	for i:=0 ;i < dy; i++ {
		var yi []uint8
		for j:=0 ;j< dx; j++ {
			xy = multSum(i,j,a)
			
			yi = append(yi, xy)
		}
		ret = append(ret, yi)
	}
	
//	for _,v := range ret {
//		fmt.Println(v)
//	}
	
	return ret
	
}

func main() {
	pic.Show(Pic)
}
```

## Maps

```
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

* The zero value of a map is nil. A nil map has no keys, nor can keys be added.

* The make function returns a map of the given type, initialized and ready for use.

```
func main() {
	m := make(map[string]int)

	m["Answer"] = 42
	fmt.Println("The value:", m["Answer"])

	m["Answer"] = 48
	fmt.Println("The value:", m["Answer"])

	delete(m, "Answer")
	fmt.Println("The value:", m["Answer"])

	v, ok := m["Answer"]
	fmt.Println("The value:", v, "Present?", ok)
}
```

