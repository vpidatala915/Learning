package main
import "fmt" <<-- packages need to be package type
	fmt is used to create 
import "string"

// STRINGS
Strings are immutable in Go -- compiler will reject any attempt to modify a string
at the very base level a string unlike a rune aka character which is an integer, is instead 
a byte stream. 
// In go, there is a massive difference between 'a' and "a"
// like C and all  other languages except Python
// Character aka Rune, technically an integer - 'a' 
// and String "a" - a read only slice of bytes, it is a sequence of data
// var tmp rune = "h", this is 4 bytes of data
// Like C in Go, string is sequence of bytes' 
// strings package
// A rune can have add mathematical operation on top of it
strings.Contains(s, sub)
strings.HasPrefix(s, pre)
strings.Index(s, sub)
strings.ReplaceAll(s, 0, n)
strings.Split(s, sep)
strings.TrimSpace(s)
strings.Join(slice, sep)
strings.ToLower/ToUpper
tmp := s1 + s2

// STRING  BUILDER
var sb strings.Builder
for (i:=0; i < 100; i++) {
	sb.WriteString("data ")
}
result := sb.String()

// for single character string types --- we use direct int()

//String coversion types - multi character string
import "strconv"
strconv.Atoi("123")
strconv.Itoa(123)
//String to byte slice:
[]byte("hello")
//byte slice to string
tmp := string(mbytes)


len()
:= - this is only possible to used inside a function for dyanmic assignment
var on other hand is useful when you want to create a variable outside a function
	such that it can be used by other functions
	More on this? -- var can group create and assign variables
	If you want to create a variable such that it should not be assigned a value -- you needvar

func --> use func as main
followed by function name

Concurrency with Goroutines and Channels: Golang simplifies concurrent programming by utilizing lightweight goroutines 
and channels for communication, resulting 
in scalable and efficient code.

Inbuilt concurrency support: lightweight processes (via go routines), channels, select statement.


var age int;

var string s = 'HelloWord'
fmt.Println(s[0:2])
fmt.Println(s[2:])
fmt.Println(s[:])
fmt.Println(s[:3])

// sprintf is the name as C, sprint is used to add the message in go
msg := fmt.Sprintf("Hello Kartik, I am freasking old - age: %d", age)

if () {

} else {

}

for [condition | (init; condition; increment) | Range] {
	statement(s);
}
goto label;


Memory management, it is important to understand here --
when you write a code and is compiled, and the binary is executed and loaded onto to the memory
what is happening is -- the process memory layout is broken into 
```
TEXT : RO - WHERE THE CODE LIES (INSTRUCTIONS)
BSS
HEAP
STACK
```
The Code reading onto the text section basically gathers and reads the code we compiled
the Go compiler is different from C,C++ and other languages from the fact the compiler decides
which section to keep the code in heap or stack -- based on the need and use -- 
this is a significant departure from other languages where it is essentially the developer
telling heap using new or malloc

the decision made by the go compiler is as follows:
if a variable needs to scope out the function -- keep the variable in the heap
otherwise put it in the stack


Derived types
They include (a) Pointer types, (b) Array types, (c) Structure types, (d) Union types and (e) Function types f) Slice types g) Interface types h) Map types i) Channel Types

when is the heap exactly used in go, in other languages like C we use malloc to tell dynamic memory allocation, in C++, C# and Java we use new keyword to basically tell dynamic memory allocation
In Go, the boundary between the Stack and the Heap is intentionally "invisible" to the developer. Unlike C where you must explicitly call malloc, or Java where new almost always guarantees a heap allocation, Go uses a process called Escape Analysis.
The compiler, not the programmer, decides where the memory lives based on the life cycle of the variable.

1. The Core Rule: Escape Analysis
During compilation, Go looks at your code and asks: "Does this variable need to outlive the function it was created in?"
If No: It stays on the Stack. This is incredibly fast and "cleans itself up" when the function returns.
If Yes: It "Escapes to the Heap." The runtime allocates memory that persists even after the function is gone, and the Garbage Collector (GC) will eventually have to clean it up.


// SLICE
// THE 24 BYTE LOGIC
// This is a header pointing to startnig_address, len_elements, len_capacity
// A slice is a view into to byte array, but different from strings they are actually
// mutable -- they support a feature/function called append which basically allow to grow
// a slice -- the append operation is expensive if the num elements is equal to capacity
// basically the compiler need to find a new array with another capacity

// Rune - 4 bytes - Value
// String - 16 bytes - (2*8 bytes pointers -- starting address + length)
// Slice - 24 bytes - (3*8 - starting address + len_index + len_capacity)
// how do you do a slice it is as smple as s[2:5]. here s can be a string, byte array etc


// Comparision between go and C
// C is memory effieicnt but demands more time complexity - 
// strlen() function in C requiring traversing each byte till you get /0
// on ther hand, Go offer metadata


// Why GO is used?
// it has low memory foot print that Python and Java per Image but still offering more
// memory safety than Go


/*
2. Why it isn't on the Stack
In C, if you did char s[] = "Hello world";, the compiler would actually generate code to copy those bytes onto the stack every time the function runs.

Go doesn't do that because strings are immutable. Since you can’t change "Hello world", 
there is no reason to waste CPU cycles copying it to the stack. 
Every function in your program that uses that same string literal 
will have a local header pointing to the exact same spot in the read-only section.
*/

// Go Struct -- is also constructing data in linear version
// type Stats struct {
//	A int32
//  B int32
//  }
// there is no METADATA OR PADDNG
/*
Struct Definition,Memory Map (64-bit),Total Size
type Perfect struct { a int64; b int64 },[AAAA AAAA][BBBB BBBB],16 bytes
type Messy struct { a bool; b int64 },[Axxx xxxx][BBBB BBBB],16 bytes
/*


// GO Methods not function, they are functions but tied to a struct
// type Stats struct {
	A int32 // 4 bytes
	B int32 // 4 bytes
}
// s := &Stats{A: 10}
// s := Stats{A: 10}
// func (s Stats) Total() int32 {
	return s.A + s.B
}
// GO DOES NOT SUPPORT EXTENDING -- INTERFACE
// type Base struct {
	ID int
}
// type Child struct {
	BASE
	Name string	
}

By omitting the name, Go performs Field Promotion. 
The fields of the inner struct "rise" to the surface of the outer struct.

 Exported vs. Unexported (The First Letter Rule)
 Go handles visibility (public/private) at the package level
  using capitalization.type User struct { Name string } $\rightarrow$ Exported. Other packages can see and use the Name field.type User struct { name string } $\rightarrow$ 
 Unexported. Only code inside the same package can touch the name field.

 Component,Example,Visibility
Struct Name,type User struct,Exported: Other packages can create a User.
,type hidden struct,Unexported: Other packages don't even know this struct exists.
Struct Field,Name string,"Exported: If someone has a User, they can read/write .Name."
,age int,Unexported: Other packages cannot see .age even if they own the struct.
Function,func Calculate(),Exported: Can be called via mypackage.Calculate().
Method,func (u User) Save(),Exported: Can be called by anyone with a User instance.

type User struct {
    FirstName string `json:"first_name" db:"f_name"`
    Age       int    `json:"age"`
}

Receiver Type,Syntax,What happens?,Use Case
Value Receiver,func (u User) Move(),Go copies the entire struct.,Small structs or when you don't want to modify the original.
Pointer Receiver,func (u *User) Move(),Go passes the 8-byte address.,Large structs or when you need to modify the original fields.


The Pointer to Empty Struct Mystery
We mentioned that struct{} occupies 0 bytes. But what happens if you take the address of two different empty structs?

Go
a := &struct{}{}
b := &struct{}{}
fmt.Println(a == b) 
The Logic: In the current Go runtime, the address of any 0-byte allocation is often the same. Go has a special global variable called zerobase. 
All zero-width types point to this same address in memory to avoid wasting heap space.

Struct Comparability
In Java, == compares references (addresses). 
In Go, == can compare values, but only if the struct allows it.

When it works: If a struct contains only "comparable" types 
(ints, strings, bools, pointers, arrays), you can do structA == structB. 
Go will perform a field-by-field bitwise comparison.

When it fails: If your struct contains a Slice, a Map, or a Function, 
it becomes "non-comparable." The compiler will throw an error if you try to use ==.
Why? Slices and Maps are headers pointing to dynamic memory. 
Comparing them is ambiguous—do you compare the 24-byte header or the 1GB of data it points to? Go forces you to be explicit to avoid "hidden" expensive operations.


1. CPU Cache Friendliness
Because a Go struct has no header and can be laid out contiguously in an array, it is "Cache Friendly."
The Logic: When the CPU fetches one struct from RAM, it actually grabs a 64-byte "Cache Line."
The Go Advantage: Because your structs are packed tightly together, that one 64-byte fetch might bring 4 or 5 full structs into the CPU cache at once.
The Java/Python Failure: Since those languages store objects as pointers to other locations, the CPU has to "jump" to a new memory address for every single item. This causes "Cache Misses," which are 10x–100x slower than reading from the cache.

2. The "Nil" Pointer on a Struct
In C++, calling a method on a NULL pointer is an immediate crash. In Go, it’s actually legal—if the method is designed for it.

Go
type Node struct {
    Value int
}

func (n *Node) IsValid() bool {
    if n == nil {
        return false
    }
    return true
}

var n *Node
fmt.Println(n.IsValid()) // This prints "false" instead of crashing!
Why? Because a method is just a function. Go passes the nil pointer as the first argument. As long as the function doesn't try to "dereference" (look inside) the struct, it won't crash. This allows for very clean, defensive code without constant null checks in the calling logic.

3. The "Self-Referential" Struct
You cannot put a struct inside itself because the compiler
 wouldn't know the size (it would be infinite).
Illegal: type Node struct { Next Node }
Legal: type Node struct { Next *Node }
By using a pointer, the size of the field becomes exactly 
8 bytes (on a 64-bit system), regardless of how big the Node is. 
This is the foundation of every linked list and tree structure in the language.

can you slice a struct -- NO!!!!

import "unsafe"

u := User{Name: "Alice"}
// Convert the struct's memory address into a byte slice
size := unsafe.Sizeof(u)
structAsBytes := unsafe.Slice((*byte)(unsafe.Pointer(&u)), size)

Once we cover these, there is nothing left to know.

1. The "Zero-Width" Field at the End
This is a very specific "bare bone" quirk. If you have a struct where the last field has a size of zero (like an empty struct struct{} or a zero-length array [0]int), the compiler will add padding even if it doesn't need to.

Go
type Problem struct {
    A int64
    B struct{} // 0 bytes, but at the end!
}
The Logic: If B is at the very end, its address would technically be the same as the address of the next object in memory. To prevent two different variables from having the same memory address (which confuses the Garbage Collector), Go adds 1-8 bytes of padding to ensure B has its own unique "spot."
The Fix: If you use empty structs for signals, never put them as the last field in a larger struct if you want to save every byte.

Scenario,Memory Behavior
Empty Struct (struct{}),0 bytes. Points to zerobase.
Embedded Struct,Flattened. Fields are placed at the top of the parent memory block.
Method on Pointer,Address pass. The 8-byte pointer is passed to the function.
Method on Value,Bulk copy. The entire struct is copied onto the stack.
Interface Wrap,Boxing. Struct is copied to the heap; 16-byte header is created.
Last-field Padding,Waste. 1-8 bytes added if the last field is 0-width.
Field Reordering,Optimization. Small types at the end reduce total padding.

2. Struct Inlining (The Compiler's Secret)
When you have a small struct that is only used inside one function, the Go compiler performs Inlining.

The Bare Metal: It doesn't actually create a struct in memory. Instead, it "explodes" the struct into individual registers.

Result: If you have type Point struct { X, Y int }, the compiler might just put X in register RAX and Y in register RBX. The "struct" ceases to exist as a physical object and becomes pure CPU speed.


// SORTING

// SETS

// HASHTABLE

// MATH OPERATIONS

// INTERFACE

// GO ROUTINES

// STRING BUILDER