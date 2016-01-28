---
layout: post
title: C++ Primer(5th Edition) Notes
permalink: /cpp-primer-notes-ch2.html
category: Tech
tag: C++, C++ Primer
---

# C++ Primer(5th Edition) Notes

*I am a beginner in C++ with programming experience in Java and python. This is my reading notes of the **C++ Primer 5E by Lippman, Lajoie, Moo** book.  I did not include every detail in this note but readers who have a solid background in any general-purpose languages and OOP should find no difficulty reading this.*

*The structure of this note will strictly follow the book. I will try to make this note a brief summary of the book without losing any critical information.*

## Chapter 2 Variables and Basic Types

### 2.1 Primitive Built-in Types

#### 2.1.1 Arithmetic Types

Integral Types

* `bool` - True/False
* `char` - 8 bits, can be either signed char or unsigned char by default
* `char16_t`,` char32_t` - intended for Unicode characters
* `short`, `int`, `long`, `long long` - store integers, `long long` was introduced by *C++11*

Floating-point types

* `float` - single-precision floating-point, >= 6 significant digits
* `double` - double-precision floating-point, >= 10 significant digits
* `long double` - extended-precision floating-point - for special purpose floating-point hardware

**Rule of Thumb**

* Use unsigned type when dealing with non-negative values
* Use `int`for integer arithmetic, use `long long` when `int` is not enough
* Specify `signed char` or `unsigned char` when you need tiny integers
* Use `double` for floating-point computations

#### 2.1.2 Type Conversions

* non`bool` -> `bool` - False if 0, True otherwise
* `bool` -> non`bool` - 1 if True, 0 if False
* floating-point type -> integral type - only integral parts are saved (e.g. 3.14 -> 3.0)
* out-of-range - > unsigned integral type - remainder of the value modulo the number of values the target type can hold(e.g. assign -1 to 8-bit unsigned char -> 255)
* out-of-range -> signed type - undefined. Anything could happen.

**Do not mix signed and unsigned types**
Signed type will be converted to unsigned type automatically if they appear in a same expression. 
```c++
int a = -42; 
unsigned int b = 10;
a + b; // a will be converted to unsigned int before the addition is done
       // result is 4294967264
```

#### 2.1.3 Literals

A value is a literal if its value is self-evident. Every values has a type which is determined by its form and value.

Integer literals can be decimal, octal, or hexadecimal notation. By default, a decimal literal has the smallest signed type(`int`, `long`, `long long`) that it fits in; Octal and hexadecimal literals can have either signed or unsigned types, it should be explicitly specified.
```cpp
20   // decimal integer
024  // start with 0, octal integer
0x14 // hexadecimal integer
```
Floating-point literals are always positive. Even if there is a minus sign, it is a minus operator. By default, floating-point literals have type double.
```cpp
3.14159   // floating-point
3.14159e0 // floating-point, scientific notation
```
A character literal can be a plain character (e.g., `'x'`), an escape sequence (e.g., `'\t'`), or a universal character (e.g., `'\u02C0'`). String literal has type `char[]` with a `'\0'` appended. Two strings literals that are separated by spaces, tabs or newlines, are concatenated into a single literal. 

In general escape sequence, `\x` is followed by one or more hexadecimal digits and `\` is followed by at most three octal digits. The value represents the numerical values of the character.
```cpp
L'a'           // character literal, forcing wchar_t type
"Hello World!" // String literal
u8"hi!"        // String literal, char[] type, utf-8 encoding
std::cout << "Hi \x4dO\115!\n"; // prints Hi MOM! followed by a newline (in ASCII)
```
Specifying the Type of a Literal

|Character and String Literals|||
|-------|--------|--------------|
|Prefix|Meaning|Type|
|u|Unicode 16 character|char16_t|
|U|Unicode 32 character|char32_t|
|L|wide character|whar_t|
|u8|utf-8 (String literal only)|char|

|Integer Literals||Floating-Point Literals||
|----------------|---|---|---|
|Suffix|Minimum Type|Suffix|Type| 
|u or U (e.g.`18u)`|unsigned|f or F|float|
|l or L (use L to prevent mistaking from 1)|long|l or L|long double|
|ll or LL|long long|

### 2.2 Variables

#### 2.2.1 Variable Definitions

A variable provides us with named storage that out programs cam manipulate. The size and layout of the storage is determined by the variable's type.

**What is an object?**
In this note, an object is a region of memory that has a type. We will freely use the term *object* regardless of whether of e object has built-in or class type, is named or unnamed,or can be read or written.

An object that is initialized gets the specified value at the moment it is created. A very important concept here is that **initialization and assignment are different operations** in C++. Initialization happens when a variable is given a value when it is created whereas assignment obliterates an object's current value and replaces that value with a new one.

```cpp
int a = 0;	// list initialization, *C++11* feature
int a = {0};
int a{0};   // list initialization, *C++11* feature
int a(0);

double salePrice = applyDiscount(price, discount); // okay, initialize with arbitrary complicated expressions

long double ld = 3.14159265;
int m{ld}, n = {ld}; // compiler error.
int p(ld), q = ld; // okay but truncated value
```
All of above are valid ways to define an `int` variable named `a`. When used with variables of built-in type, the compiler will not allow list initialize if the initializer may lead to the loss of information.

**Default initialization** (variables defined without an initializer)
|Where Variables Are Defined| | Default Value|
|---|---|---|
|out of any function body| | zero |
|inside a function| built-in type | undefined - will cause undetermined run-time problem (TODO: one exception on p. 205)  |
| | non built-in type | determined by the class (class may require an initialization, checked by compiler) |

#### 2.2.2 Variable Declarations and Definitions

A variable declaration specifies the type and name of a variable. A declaration makes a name known to the program. A file that wants to use a name defined elsewhere includes a declaration for that name. 

A variable definition is a declaration, and it also allocates storage and may provide the variable with an initial value. A definition creates the associated entity.

Variables must be defined once but can be declared many times. To use the same variable in multiple files, we must define that variable in exactly one file. Other files that use that variable must declare that variable.

```cpp
extern int i; 	// decalres but does not define i
int j; 			// declares and defines j

extern double pi = 3.14  // definition, this overrides "extern"
```

#### 2.2.3 Identifiers

* Must begin with either a letter or an underscore
* Cannot use reversed keywords
* Cannot contain two consecutive underscores
* Cannot begin with an underscore followed immediately by an upper-case letter
* Identifiers defined outside a function may not begin with an underscore

#### 2.2.4 Scope of a Name

```cpp
int reused = 42;
int main()
{
	int unique = 0;
	std::cout << reused << " " << unique << std::endl;    // print 42 0
	int reused = 0;
	std::cout << reused << " " << unique << std::endl;    // print 0 0
	std::cout << ::reused << " " << unique << std::endl;  // print 42 0
	return 0;
}
```
Note the scope operator in the third cout statement. The global scope has no name, thus, ::reused fetches the name from the global scope.

### 2.3 Compound Types

#### 2.3.1 References (lvalue references)

* A reference is not a object. Instead, it is another name for an already existing object. 
* A reference may be only bound to an object, not to a literal or an expression or another reference.
* Reference cannot be re-bound, it must be initialized and remains bound to its initial object. 

```cpp
int i = 1024;
int &r = i; 	// r is a reference bound to i

int &r1 = 1024; // error: initializer must be an object
double d = 3.14;
int &r2 = d; 	// error: initializer must be an int object
```

#### 2.3.2 Pointers

```cpp
int *a, *b; 	// the * in declarator must be repeated for each pointer variable
int ival = 42;
int *p = &ival; // & is an address-of operator in an expression
int *p2 = p;
cout << *p; 	// * is a dereference operator in an expression
*p = 0;
cout << *p; 	// prints 0
```

* A pointer is an object in its own right
* May not define a pointer to a reference as a reference is not an object
* The type of the pointer and the object to which it points must match except (TODO p.62, 601
* void* pointer is a special pointer type that can hold address of any object

A pointer can have value of:

* the location of an object
* the location immediately pass the end of an object
* null pointer
	* `int *p1 = nullptr; // nullptr is a literal can be converted to any pointer type, C++11 feature`
	* `int *p2 = 0;`
	* `int *p3 = NULL; // need #include cstdlib`
	* when appears in a condition, null pointer is false, everything else is true
* everything else is invalid (causing undefined behaviour)
	* Recommendation: **initialize all pointers** to avoid run-time problems

Given two valid pointers of the same time, == and != operators can be applied:

* True if both are null or they hold the same address
* False otherwise

#### 2.3.3 Understanding Compound Type Declarations

```cpp
// i is an int; p is a pointer to int; r is a reference to int
int i = 1024, *p = &i, &r = i;

// p1 is a pointer to int, p2 is an int
// this is misleading but note * is a part of declarator and only
// modifies the type of p here
int * p1, p2; 
```
```cpp
int ival = 1024;
int *pi = &ival;
int **ppi = &pi;
std::cout << ival << *pi << **pi << endl; // print the 1024 three times
```
```cpp
int i = 42;
int *p; 	 // p is a pointer to int
int *&r = p; // r is a reference to the pointer p
			 // tips: read from right to left here
			 // &r makes r a reference
			 // * &r makes r is a reference of a pointer
			 // int *&r says r is a reference refers to a pointer points to int
			 
r = &i;		 // p points to i
*r = 0;		 // dereferencing yields i, changes i to 0
```

### 2.4 const Qualifier

```cpp
const int bufSize = 512;
```
`Const` quantifier makes a variable unchangeable. Since we cannot change the value of a const variable, it must be initialized. Const variable are usually replaced by their values by the compiler during the compilation. If we want to use a const variable in multiple file, you must use keyword `extern` on both of its declaration and definition.

```cpp
extern const int bufSize = fcn(); // in .cpp file
extern const int bufSize();		  // in .h file
```

#### 2.4.1 Reference to const

```cpp
const int ci = 1024;
const int &r1 = ci;
r1 = 5; 		// error: r1 is a reference to const
int r2 = &ci; 	// error: non-const reference to a const object
```
```cpp
int i = 42;
int &r1 = i;
const int &r2 = i;
r1 = 0;
std::cout << r2; // print 0
```
Reference to `const` is a reference what refers to a `const` type. A reference to `const` cannot be used to change the object to which the reference is bound. Note that whether a reference refers to a const or non-const type affects what we can do with that reference, not whether we can later the binding of the reference it self.

```cpp
double dval = 3.14;
const int &ri = dval; // ri will bind to an temporary int object with value of 3
const double &ri = dval * 2;  // binding reference to an expression
```

Although we usually only allow binding references to objects with matching types, references to const can bind to nonconst objects, literals or expressions, if the literal or expression can be converted to the type of the reference to const. 

#### 2.4.2 Pointers and const

| Pointers to const |`const double *cptr = &pi;`| |
|-------------------|-|-|
| | points to objects | points to const objects |
| pointer | allowed | not allowed |
| pointer to const | allowed, but cannot change the object through cptr | allowed |

**const Pointer** 
const Pointers are `const` objects. a const pointer must be initialized and its value cannot be changed after initialization. The object to which the `const` pointer points can be changed through that pointer.
```cpp
int errNumb = 0;
int *const curErr = &errNumb; // const pointer always points to errNumb
const double pi = 3.14;
const double *const pip = &pi; // const pointer to a const points to pi
```

#### 2.4.3 Top-Level const
Top-level `const` can appear in any object type. It means the object itself is `const`.
Low-level `const` appears in compound types. References to const and pointer to const are low-level `const`.

When we copy an object, top-level consts are ignored whereas low-level are not.
```cpp
int i = 0;
const int ci = 42;   // top-level const
const int *p2 = &ci; //low-level const
const int *const p3 = p2; // top-level and low-level const
const int &r = ci; 	 // low-level const

i = ci; // okay: top-level consts are ignored
int *p = p2 // error: p is not a low-level const
```

#### 2.4.4 `constexpr` and Constant Expressions

A constant expression is an expression whose value cannot change and that can be *evaluated at compile time*. A literal is a constant expression. A const object that is initialized from a constant expression is a constant expression.

constexpr Variables *(C++11 feature)*

```cpp
constexpr int mf = 20;
constexpr const int *p = &mf; // constexpr pointer points to a const int
```
 
Variables declared as `constexpr` are implicitly `const` (top-level) and must be initialized by constant expressions. This will be checked by the compiler. C++11 lets us define certain functions as `constexpr` if it is simple enough to be evaluated at compile time. It is suggested to use `constexpr` when you intend to use constant expression.

Be careful that we can only points a `constexpr` pointer to nullptr or a fixed address since the dynamic address cannot be carried out by compiler. The address of an object defined outside of any function is a constant expression. (TODO: there is another type on p.205)

### 2.5 Dealing with Types

#### 2.5.1 Type Aliases

A type alias is a name that is a synonym for anothe type. We are using type alias trying to simplify code or  emphasize the purpose for which a type is used.

```cpp
typedef double wages;	// wages is a synonym for double
typedef wages base, *p; // base is a synonym for double, p for double*

using SI = Sales_item;  // SI is a synonym for Sales_item, C++11 feature
SI item; // declare a Sales_item object

typedef char *pstring;
const pstring sctr = 0; // sctr is a constant pointer(top-level) to char
						// const char *cstr = 0; wrong interpretation
const pstring *ps;  	// ps is a pointer to const pointer to char
```
#### 2.5.2 The `auto` Type Specifier *(C++11 feature)*

`auto` tells the compiler to deduce the type from the initializer. By implication, a variable that uses auto as its type specifier must have an initializer. One declaration can involve a single base type, note that initializers for variables in declaration must have types that are consistent.

```cpp
auto i = 0, *p = &i, &r = i; // i is int, p is int*, r is a reference
auto a = r; // a is an int (deducting from reference)
```

`auto` specifier ignores top-level const and keep low-level const.

```cpp
const int ci = i;
auto b = ci;  // b is int
auto e = &ci; // e is const int* (& of a const object is low-level const)
const auto f = ci; // f is const int
auto &g = ci; // g is a const int&
			  // top-level const is not ignored for reference
auto &n = i, *p2 = &ci; // error: int and const int are not consistent
```

#### 2.5.3 The decltype Type Specifier *(C++11 feature)*

`decltype` returns the type of its operand. The compiler analyses the type of its operand without evaluating.
```cpp
decltype(f()) sum = x; // sum has whatever type f returns
```
```cpp
const int ci = 0, &cj = ci;
decltype(ci) x = 0; // x is a const int, top-level const isn't ignored
decltype(cj) y = x; // y has type const int&
					// note that cj is treated as a reference here
decltype(cj + 0) b; // b is a const int
```
`decltype` returns a reference type for *expression* that yield objects that can stand on the left-hand side of the assignment. Variables are not expressions.
```cpp
int i = 42, *p = &i;
decltype(*p) c;  // error: c is int& and must be initialized
decltype((i)) d; // error: d is int& and must be initialized
				 // it treats (i) as an expression
				 // decltype((variable)) is always a reference type
```

### 2.6 Defining Our Own Data Structures

#### 2.6.1 Defining the Sales_data Type

```cpp
struct Sales_data {
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

struct Sales_data { /*...*/ } accum, trans *salesptr;
// equivalent but better way to define these objects:
struct Sales_data { /*...*/ };
Sales_data accum, trans *salesptr;
```

The class body defines the member of the class. Each Sales_data object will have these three data members. Data members that are not initialized in-class are default initialized. Initializing with parenthesis are not allowed for in-class initializer.

#### 2.6.2 Using Sales_data Class

```cpp
double price = 0;
Sales_data data;
std::cin >> data.bookNo >> data.unit_sold >> price;
```

#### 2.6.3 Writing Our Own Header Files

Class are usually defined in header files in order to ensure that the class definition is the same in each file. Typically, the name of header file is the same as the name of the class (e.g. Sales_data.h). 

Sometime a header may be included multiple times. For example, `#include <string>` appears in both Sales_data.h and main.cc files, the `string` header is included once by main.cc and once by side effect of including Sales_data.h.

In C++, when a preprocessor sees a #include, it replaces the #include with the contents of the specified header. We can use preprocessor to define header guards. `#define` directive defines a preprocessor variable. `#ifdef` is true if the variable is defined, and `#ifndef` is true if the variable has not been defined. If the test is true, the following will be executed until `#endif`.

```cpp
#ifndef SALES_DATA_H
#define DALES_DATA_H

#include <string>

struct Sales_data {
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};
#endif
```

The test will succeed once when the Sales_data.h is first included. Note that preprocessor variable must be unique throughout the program. It is suggested to always write header guard in header files.









 
> End of Chapter 2
> Written with [StackEdit](https://stackedit.io/).
