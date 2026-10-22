# C++ Module IV: Polymorphism (Complete Notes from Basics)

> Covers: Polymorphism • Types of polymorphism (compile time and runtime) • Function overloading • Operator overloading (unary and binary) • Polymorphism by parameter • Pointer to objects • `this` pointer • Virtual functions • Pure virtual functions
>
> 🎯 = a point examiners love
>
> Before you start: you should know basic classes and inheritance (Module 3), especially that a **base-class pointer can point to a derived-class object**.

---

## Table of Contents
1. [What is polymorphism?](#part-1-what-is-polymorphism)
2. [Function overloading](#part-2-function-overloading)
3. [Polymorphism by parameter](#part-3-polymorphism-by-parameter)
4. [Operator overloading: the basics](#part-4-operator-overloading-the-basics)
5. [Overloading unary operators](#part-5-overloading-unary-operators)
6. [Overloading binary operators](#part-6-overloading-binary-operators)
7. [Operator overloading using friend functions](#part-7-operator-overloading-using-friend-functions)
8. [Pointer to objects](#part-8-pointer-to-objects)
9. [The `this` pointer](#part-9-the-this-pointer)
10. [Base-class pointers to derived objects](#part-10-base-class-pointers-to-derived-objects)
11. [Virtual functions (runtime polymorphism)](#part-11-virtual-functions-runtime-polymorphism)
12. [Pure virtual functions and abstract classes](#part-12-pure-virtual-functions-and-abstract-classes)
13. [Compile time vs runtime: the full comparison](#part-13-compile-time-vs-runtime-the-full-comparison)
14. [How to write answers in the exam](#how-to-write-answers-in-the-exam)

---

## Part 1: What is polymorphism?

**Poly** means many and **morph** means form, so polymorphism means **"many forms"**.

### Real-life idea
You are one person, but you behave differently depending on the situation: a student in class, a son or daughter at home, a customer in a shop. Same person, many forms.

In C++, the **same name** (a function name or an operator) behaves **differently depending on the context**:
- `+` adds numbers: `2 + 3` gives 5
- `+` joins strings: `"Hi" + string(" there")` gives "Hi there"
- a function named `area()` calculates a circle's area for a Circle and a square's area for a Square

### Definition (write this in exams)
> **Polymorphism** is the ability of a function or operator to behave differently based on the context: the type or number of arguments, or the type of object it is called on. It means "one interface, multiple implementations."

### Types 🎯

```
                 Polymorphism
                /            \
     Compile-time             Run-time
      (Static)                (Dynamic)
      /       \                   |
 Function    Operator       Virtual functions
 overloading overloading    (+ pure virtual / abstract classes)
```

| | Compile-time | Run-time |
|---|---|---|
| Other names | static, early binding | dynamic, late binding |
| Decided by | the compiler | the program while running |
| Achieved by | function overloading, operator overloading | virtual functions |
| Speed | faster | slightly slower (it looks up a table at runtime) |

**Binding** means connecting a function *call* to the actual function *code* that runs.
- **Early binding:** the connection is made while compiling.
- **Late binding:** the connection is made while the program runs.

---

## Part 2: Function overloading

### The idea
Several functions share the **same name** but take **different parameters**. The compiler looks at the arguments you pass and picks the matching one.

Without overloading you'd need `addTwoInts()`, `addThreeInts()`, `addDoubles()` and so on. With overloading, it's all just `add()`.

```cpp
class Calculator {
public:
    int add(int a, int b)          { return a + b; }
    int add(int a, int b, int c)   { return a + b + c; }
    double add(double a, double b) { return a + b; }
};

int main() {
    Calculator c;
    cout << c.add(2, 4);        // 6     → add(int, int)
    cout << c.add(1, 2, 3);     // 6     → add(int, int, int)
    cout << c.add(2.5, 1.5);    // 4.0   → add(double, double)
}
```

### Definition
> **Function overloading** is a feature where two or more functions in the same scope have the **same name but different parameter lists**. It is a form of **compile-time polymorphism**.

### Rules: the parameters must differ in at least one of these
1. **The number** of parameters: `add(int, int)` vs `add(int, int, int)`
2. **The types** of parameters: `print(int)` vs `print(double)`
3. **The order** of the types: `show(int, char)` vs `show(char, int)`

🎯 **Return type alone is NOT enough:**
```cpp
int    getValue();
double getValue();   // ❌ ERROR: the parameters are the same, only the return type differs
```
Why? When you write `getValue();` the compiler has no way to know which one you mean.

### How the compiler picks a function (overload resolution)
It tries, in this order:
1. **Exact match:** `f(5)` matches `f(int)`
2. **Promotion:** `char` → `int`, `float` → `double`
3. **Standard conversion:** `int` → `double`, `double` → `int`, and so on
4. **User-defined conversion** (constructors, conversion operators)

### 🎯 Ambiguity in overloading
If two functions match equally well, you get a compile error:
```cpp
void f(int x);
void f(float x);
f(5.5);   // ❌ ambiguous! 5.5 is a double, and double→int and double→float are equally good
```
Default arguments can cause this too:
```cpp
void g(int a);
void g(int a, int b = 0);
g(10);    // ❌ ambiguous: both can take a single int
```

### Advantages
- The code is easier to read, because related tasks share one name.
- You don't have to remember many function names.
- It's an example of compile-time polymorphism.

---

## Part 3: Polymorphism by parameter

This syllabus term can mean two things, so know both:

### (a) Choosing behaviour by the parameters passed
This is the same idea as function overloading: the **parameters you pass decide which version runs**. The function name stays the same, and the parameter list picks the behaviour.
```cpp
void print(int i)         { cout << "Integer: " << i; }
void print(double d)      { cout << "Double: " << d; }
void print(const char* s) { cout << "String: " << s; }

print(10);       // Integer: 10
print(3.14);     // Double: 3.14
print("Hello");  // String: Hello
```

### (b) Parametric polymorphism: templates
In OOP theory, **parametric polymorphism** means writing **one** function or class that works for **any type**, where the type itself is a *parameter*. In C++ this is done with **templates**:
```cpp
template <typename T>
T getMax(T a, T b) {
    return (a > b) ? a : b;
}

getMax(3, 7);        // T = int    → 7
getMax(2.5, 1.5);    // T = double → 2.5
getMax('a', 'z');    // T = char   → 'z'
```
- **Overloading:** you write *many* functions, one per type.
- **Templates:** you write *one* function, and the compiler generates a version for each type you use.

Both are **compile-time** polymorphism.

🎯 In a viva, if asked "what is polymorphism by parameter?", say: *"The same function name behaves differently depending on the parameters passed: their number, type or order. That's function overloading. In its general form, the type itself can be a parameter, which is parametric polymorphism and is done with templates in C++."*

---

## Part 4: Operator overloading: the basics

### The problem
`+` works on `int`, `float` and so on. But suppose you make your own class:
```cpp
class Complex { int real, imag; };
Complex c1, c2, c3;
c3 = c1 + c2;     // ❌ ERROR: C++ doesn't know how to add two Complex objects
```
Operator overloading lets **you** teach C++ what `+` means for your class.

### Definition
> **Operator overloading** gives a special meaning to an existing C++ operator when it is applied to **user-defined types** (objects), without changing its original meaning for built-in types. It is a form of **compile-time polymorphism**.

### Syntax
```cpp
return_type operator symbol (arguments) {
    // body
}
```
For example: `Complex operator+(Complex obj)`, `void operator++()`, `bool operator>(Distance d)`.

`operator+` is just a **function with a special name**. When you write `c1 + c2`, C++ secretly calls `c1.operator+(c2)`.

### 🎯 How many arguments does the operator function take?

| | Unary (like `++`, `-`) | Binary (like `+`, `>`) |
|---|---|---|
| **Member function** | **0** arguments | **1** argument |
| **Friend function** | **1** argument | **2** arguments |

**Why?** A member function is called *on* an object, so that object (the left operand) is passed automatically through `this` (Part 9). It doesn't need to be in the parameter list. A friend function isn't called on an object, so every operand has to be passed explicitly.

```cpp
obj++;        // member: obj.operator++(0)        friend: operator++(obj, 0)
obj1 + obj2;  // member: obj1.operator+(obj2)     friend: operator+(obj1, obj2)
```

### 🎯 Rules of operator overloading
1. You can only overload **existing** operators. You can't invent new ones, like `**` or `@`.
2. You **can't change precedence**: `*` still happens before `+`.
3. You **can't change associativity**.
4. You **can't change the number of operands** (the arity): `+` stays binary and `!` stays unary.
5. You **can't change the meaning for built-in types**: `2 + 3` is always 5. At least one operand must be a user-defined type (an object).
6. Overloaded operators (except `=`) are **inherited** by derived classes.
7. Some operators **must be member functions**: `=`, `[]`, `()`, `->`. They can't be friends.

### 🎯 Operators that CANNOT be overloaded

| Operator | Name |
|---|---|
| `.` | member access (dot) |
| `.*` | pointer-to-member access |
| `::` | scope resolution |
| `?:` | ternary / conditional |
| `sizeof` | size-of |
| `typeid` | type information |

**Why these?** They work on names and types rather than values, and the language itself depends on them. If you could change what `.` does, you could never reliably access a member again.

---

## Part 5: Overloading unary operators

A unary operator works on **one** operand: `++`, `--`, `-` (negation), `!`.

### Example 1: Prefix `++`
```cpp
class Counter {
    int count;
public:
    Counter() { count = 0; }
    void operator++() { count++; }        // prefix ++ : no arguments
    void show() { cout << "Count: " << count << endl; }
};

int main() {
    Counter c1;
    c1.show();     // Count: 0
    ++c1;          // calls c1.operator++()
    c1.show();     // Count: 1
}
```

### Prefix vs postfix 🎯
`++c` and `c++` use the same symbol. How does C++ tell them apart? It uses a **dummy `int` parameter**:

| | Signature | Called as |
|---|---|---|
| Prefix `++c` | `operator++()` | `c.operator++()` |
| Postfix `c++` | `operator++(int)` | `c.operator++(0)` |

The `int` carries no real value. It exists only so the compiler can tell the two apart.

**Meaning:**
- **Prefix** (`++c`): increment first, then return the **new** value.
- **Postfix** (`c++`): return the **old** value, then increment.

### Example 2: Prefix and postfix done properly (returning objects)
Returning an object lets you write `c2 = ++c1;` or `c2 = c1++;`:
```cpp
class Counter {
    int count;
public:
    Counter(int c = 0) { count = c; }

    // PREFIX: increment, then return the updated object itself
    Counter& operator++() {
        ++count;
        return *this;            // *this = "the current object" (see Part 9)
    }

    // POSTFIX: save the old value, increment, return the OLD copy
    Counter operator++(int) {
        Counter temp = *this;    // copy of the old state
        ++count;
        return temp;
    }

    int get() { return count; }
};

int main() {
    Counter a(5), b, c;
    b = ++a;     // a = 6, b = 6
    c = a++;     // c = 6 (old value), then a = 7
    cout << a.get() << " " << b.get() << " " << c.get();   // 7 6 6
}
```
⚠️ If you define **only** the prefix version and write `c1++`, modern compilers give an **error** (old Turbo C++ only warned). Define both if you use both.

### Example 3: Unary minus (`-`)
This returns a **new** object with the negated value and leaves the original unchanged:
```cpp
class Number {
    int x;
public:
    Number(int v = 0) { x = v; }
    Number operator-() {          // unary minus: no arguments
        return Number(-x);        // a new temporary object
    }
    void show() { cout << x << endl; }
};

int main() {
    Number n1(5), n2;
    n2 = -n1;      // calls n1.operator-()
    n1.show();     // 5   (unchanged)
    n2.show();     // -5
}
```

---

## Part 6: Overloading binary operators

A binary operator works on **two** operands: `+`, `-`, `*`, `/`, `==`, `>`, `+=` and so on.

### How it works 🎯
```
obj1 + obj2     →     obj1.operator+(obj2)
 ↑        ↑
 calls    passed as the argument
 the function
```
The **left** object calls the function (so its members are accessed directly), and the **right** object is the argument.

### Example 1: Adding two complex numbers
```cpp
class Complex {
    int real, imag;
public:
    Complex(int r = 0, int i = 0) { real = r; imag = i; }

    Complex operator+(const Complex& obj) {
        Complex temp;
        temp.real = real + obj.real;    // 'real' here means this->real (the left object)
        temp.imag = imag + obj.imag;
        return temp;
    }

    void display() { cout << real << " + " << imag << "i" << endl; }
};

int main() {
    Complex c1(2, 5), c2(3, 4), c3;
    c3 = c1 + c2;        // c3 = c1.operator+(c2)
    c3.display();        // 5 + 9i
}
```
Why `const Complex&`? The `&` avoids making a copy (it's faster), and `const` promises not to change the argument.

### Example 2: Adding two distances
```cpp
class Distance {
    int feet;
    float inch;
public:
    Distance(int f = 0, float i = 0) { feet = f; inch = i; }

    Distance operator+(Distance d2) {
        int f = feet + d2.feet;
        float i = inch + d2.inch;
        if (i >= 12.0) {          // convert 12 inches into 1 foot
            i -= 12.0;
            f++;
        }
        return Distance(f, i);    // a temporary object made by the constructor
    }

    void show() { cout << feet << "'-" << inch << "\"" << endl; }
};

int main() {
    Distance d1(10, 8.5), d2(5, 6.0);
    Distance d3 = d1 + d2;
    d3.show();     // 16'-2.5"
}
```

### Example 3: Comparison operator (`>`) returning `bool`
```cpp
class Distance {
    int feet; float inch;
public:
    Distance(int f = 0, float i = 0) { feet = f; inch = i; }
    bool operator>(Distance d2) {
        float f1 = feet + inch / 12;
        float f2 = d2.feet + d2.inch / 12;
        return f1 > f2;
    }
};

int main() {
    Distance a(5, 6), b(4, 11);
    if (a > b) cout << "a is greater";    // a is greater
}
```

### Example 4: Shorthand assignment (`+=`)
`+=` **changes the left object itself** and returns a **reference** to it, so you can chain operations:
```cpp
Distance& operator+=(Distance d2) {
    feet += d2.feet;
    inch += d2.inch;
    if (inch >= 12) { inch -= 12; feet++; }
    return *this;          // return the current object itself
}

d1 += d2;    // d1 is updated in place
```
🎯 The difference between `+` and `+=`:
- `+` creates and returns a **new** object; neither operand changes.
- `+=` **modifies** the left object and returns a **reference** to it (`*this`).

---

## Part 7: Operator overloading using friend functions

### What's a friend function? (a quick recap)
A **friend function** is not a member of the class, but the class lets it access its private members:
```cpp
class A {
    int x;
    friend void show(A obj);    // "show is my friend"
};
void show(A obj) { cout << obj.x; }   // ✅ allowed to access private x
```
A friend is **not** called on an object, so it has **no `this` pointer**. That's why it needs **one more argument** than the member version.

### Unary operator as a friend (1 argument)
```cpp
class Counter {
    int count;
public:
    Counter() { count = 0; }
    friend void operator++(Counter& c);   // pass by reference so the change sticks
    void show() { cout << count; }
};

void operator++(Counter& c) {    // no Counter:: because it isn't a member
    c.count++;
}

int main() {
    Counter c1;
    ++c1;          // operator++(c1)
    c1.show();     // 1
}
```
⚠️ That is the **prefix** form. For postfix as a friend, add the dummy int: `operator++(Counter& c, int)`.

### Binary operator as a friend (2 arguments)
```cpp
class Complex {
    int real, imag;
public:
    Complex(int r = 0, int i = 0) { real = r; imag = i; }
    friend Complex operator+(Complex a, Complex b);
    void display() { cout << real << " + " << imag << "i\n"; }
};

Complex operator+(Complex a, Complex b) {
    return Complex(a.real + b.real, a.imag + b.imag);
}

int main() {
    Complex c1(2, 3), c2(4, 5);
    Complex c3 = c1 + c2;    // operator+(c1, c2)
    c3.display();            // 6 + 8i
}
```

### 🎯 Why use a friend instead of a member? The left-operand problem
With a member function, the **left operand must be an object of the class**. So:
```cpp
c2 = c1 + 5;    // ✅ member works: c1.operator+(5)
c2 = 5 + c1;    // ❌ member CAN'T work: 5 is an int, and an int has no operator+ member to call
```
A friend function takes both operands as arguments, so `5 + c1` becomes `operator+(5, c1)`, which works.

The most famous example is overloading `<<` for `cout`:
```cpp
class Point {
    int x, y;
public:
    Point(int a, int b) { x = a; y = b; }
    friend ostream& operator<<(ostream& out, const Point& p) {
        out << "(" << p.x << ", " << p.y << ")";
        return out;
    }
};

Point p(3, 4);
cout << p;    // (3, 4)
```
Here the left operand is `cout`, which is an `ostream`, not a Point. So this **must** be a friend (or another non-member function).

### Member vs friend: summary 🎯

| | Member function | Friend function |
|---|---|---|
| `this` pointer | yes | no |
| Arguments (unary / binary) | 0 / 1 | 1 / 2 |
| Left operand | must be an object of the class | can be anything (`5 + obj`, `cout << obj`) |
| Can overload `=`, `[]`, `()`, `->` | ✅ yes | ❌ no |

---

## Part 8: Pointer to objects

### Basics
You can make pointers to objects, just like pointers to `int`:
```cpp
class Student {
public:
    int roll;
    void show() { cout << "Roll: " << roll << endl; }
};

int main() {
    Student s;
    Student* ptr = &s;    // ptr holds the address of s

    ptr->roll = 10;       // use -> with pointers
    ptr->show();          // Roll: 10

    (*ptr).roll = 20;     // same as ptr->roll; the brackets are needed
    (*ptr).show();        // Roll: 20
}
```
🎯 **`.` vs `->`:** use `.` with an **object** (`s.show()`) and `->` with a **pointer** (`ptr->show()`). `ptr->x` is shorthand for `(*ptr).x`.

### Creating objects dynamically with `new`
```cpp
Student* p = new Student;   // object created at runtime (on the heap)
p->roll = 5;
p->show();
delete p;                   // you must free it yourself
```

### Array of objects with a pointer
```cpp
Student arr[3];
Student* p = arr;           // points to arr[0]
for (int i = 0; i < 3; i++) {
    p->roll = i + 1;
    p++;                    // moves to the next object (by sizeof(Student) bytes)
}
```

### Pointer type compatibility (normal rule)
```cpp
int i, *pi;   float f, *pf;
pi = &i;   // ✅
pf = &f;   // ✅
pf = &i;   // ❌ int* can't go into float*
pi = &f;   // ❌
```
The same applies to unrelated classes: an `A*` can't point to a `B` object. The **one exception is inheritance** (Part 10).

---

## Part 9: The `this` pointer

### The idea
When you call `s1.show()`, how does `show()` know it should print **s1's** data and not s2's? There's only one copy of the function's code, shared by all objects.

The answer: C++ **secretly passes the address of the calling object** to every member function. Inside the function, that address is available as **`this`**.

```cpp
s1.show();    // inside show(), this = &s1
s2.show();    // inside show(), this = &s2
```
So when you write `roll` inside a member function, C++ actually reads it as `this->roll`.

### Definition
> **`this`** is a special pointer automatically available inside every non-static member function. It holds the **address of the object that called the function**.

### Use 1: Name clash between a parameter and a member 🎯
```cpp
class Student {
    int roll;
public:
    void setRoll(int roll) {
        roll = roll;          // ❌ assigns the parameter to itself; the member doesn't change
        this->roll = roll;    // ✅ this->roll is the member, roll is the parameter
    }
};
```

### Use 2: Return the current object (`*this`), for example for chaining
`this` is a pointer, so `*this` is the object itself.
```cpp
class Counter {
    int c = 0;
public:
    Counter& inc() { c++; return *this; }
    void show() { cout << c; }
};

Counter x;
x.inc().inc().inc();   // chaining: each call returns the same object
x.show();              // 3
```
This is exactly why `+=` and prefix `++` return `*this` (Parts 5 and 6).

### Use 3: Checking self-assignment
```cpp
MyClass& operator=(const MyClass& other) {
    if (this == &other) return *this;   // don't copy an object onto itself
    // ... copy the data ...
    return *this;
}
```

### Rules 🎯
1. `this` exists only inside **non-static member functions**.
2. **Static member functions have no `this`**, because they belong to the class and are not tied to any object.
3. **Friend functions have no `this`**, because they aren't members.
4. You can't change `this` itself (`this = &other;` is an error). You *can* change the object it points to.

---

## Part 10: Base-class pointers to derived objects

### The special rule of inheritance 🎯
Normally a pointer can only point to its own type. **But a base-class pointer can point to a derived-class object**, because a derived object *is a* base object (IS-A).

```cpp
class Base    { public: int b; void show() { cout << "Base show\n"; } };
class Derived : public Base { public: int d; void show() { cout << "Derived show\n"; } };

Base    *bptr;
Derived *dptr;
Base    ob;
Derived od;

bptr = &ob;   // ✅ exact match
dptr = &od;   // ✅ exact match
bptr = &od;   // ✅ base pointer → derived object  (UPCASTING, automatic)
dptr = &ob;   // ❌ derived pointer → base object   (not allowed without a cast)
```

**Why is the last one not allowed?** A `Derived*` promises that the object has a `d`. A Base object has no `d`, so following the pointer would read memory that doesn't exist.

### What can you access through a base pointer?
**The compiler checks the type of the pointer, not the type of the object.**
```cpp
bptr = &od;
bptr->b = 200;    // ✅ b belongs to Base
bptr->d = 300;    // ❌ the compiler only knows bptr is a Base*, and Base has no d
bptr->show();     // prints "Base show"  ← (without virtual!)
```
Even though `bptr` points to a Derived object, `show()` runs **Base's** version. That's early binding: the compiler decided based on the pointer's type.

### Downcasting (an explicit cast)
To reach derived members through a base pointer, cast it:
```cpp
((Derived*)bptr)->d = 400;     // ✅
((Derived*)bptr)->show();      // Derived show
```
⚠️ Only do this if you're **sure** the object really is a Derived. The safer modern way is `dynamic_cast<Derived*>(bptr)`, which returns `nullptr` if the object isn't a Derived (it needs at least one virtual function in Base).

### ⚠️ Pointer arithmetic trap 🎯
```cpp
Derived arr[5];
Base* p = arr;   // points to arr[0]
p++;             // moves forward by sizeof(Base), NOT sizeof(Derived)!
```
If Derived is bigger than Base (it usually is), `p` now points into the **middle** of `arr[0]`, not to `arr[1]`. **Never use a base pointer to walk through an array of derived objects.**

### The problem this creates
We want `bptr->show()` to run **Derived's** show when bptr points to a Derived. That's the whole point of polymorphism, and the solution is a virtual function.

---

## Part 11: Virtual functions (runtime polymorphism)

### The fix: add `virtual`
```cpp
class Base {
public:
    virtual void show() { cout << "Base show\n"; }     // ← virtual
};
class Derived : public Base {
public:
    void show() override { cout << "Derived show\n"; } // 'override' is optional but recommended
};

int main() {
    Base* bptr;
    Base b;  Derived d;

    bptr = &b;
    bptr->show();     // Base show
    bptr = &d;
    bptr->show();     // Derived show  ✅ (decided at runtime!)
}
```

**Without `virtual`:** C++ looks at the **pointer type**, so the call runs Base's version (early binding).
**With `virtual`:** C++ looks at the **actual object** at runtime, so the call runs Derived's version (late binding).

### Definition
> A **virtual function** is a member function declared with the `virtual` keyword in the base class, which can be **overridden** in derived classes. When it is called through a base-class pointer or reference, the version that runs is decided **at runtime** based on the **type of the object**, not the type of the pointer. This achieves **runtime polymorphism**.

### Why is this so powerful? One interface, many implementations
```cpp
class Animal {
public:
    virtual void sound() { cout << "...\n"; }
};
class Dog : public Animal { public: void sound() override { cout << "Bark\n"; } };
class Cat : public Animal { public: void sound() override { cout << "Meow\n"; } };
class Cow : public Animal { public: void sound() override { cout << "Moo\n"; } };

int main() {
    Animal* zoo[3] = { new Dog, new Cat, new Cow };
    for (int i = 0; i < 3; i++)
        zoo[i]->sound();          // Bark, Meow, Moo
}
```
The loop doesn't know or care what kind of animal each one is. If you add a `Lion` class tomorrow, **the loop doesn't change at all**. This makes code easy to extend.

### How it works inside: the vtable 🎯 (viva favourite)
- Each class with virtual functions gets a hidden table called the **vtable** (virtual table). It holds the addresses of that class's versions of the virtual functions.
- Each object of such a class gets a hidden pointer, the **vptr**, that points to its class's vtable.
- At runtime, `bptr->show()` follows the object's vptr to its vtable, finds `show`, and calls it.

```
Derived object          Derived's vtable
┌──────────┐           ┌──────────────────────┐
│ vptr ────┼──────────►│ show → Derived::show │
│ b, d ... │           └──────────────────────┘
└──────────┘
```
This lookup is why runtime polymorphism is *slightly* slower than compile-time polymorphism.

### Rules of virtual functions 🎯
1. They must be **member functions** of a class.
2. They **can't be static** (static functions have no object, and virtual dispatch needs one).
3. They are accessed through a **pointer or reference** to get runtime behaviour. Called on a plain object (`d.show()`), they behave like normal functions.
4. The derived version must have the **same signature** (name, parameters and, normally, return type). If the parameters differ, it's a new function that *hides* the base one, not an override.
5. `virtual` is written in the base class. In the derived class it's **optional**, because the function stays virtual automatically. Writing `override` is good practice because the compiler then checks you really are overriding something.
6. **Constructors can't be virtual.**
7. **Destructors can, and often should, be virtual** (see below).
8. If the derived class doesn't override a virtual function, the base version is used.

### 🎯 Virtual destructor
```cpp
class Base {
public:
    ~Base() { cout << "Base destructor\n"; }          // NOT virtual
};
class Derived : public Base {
    int* data;
public:
    Derived()  { data = new int[100]; }
    ~Derived() { delete[] data; cout << "Derived destructor\n"; }
};

Base* p = new Derived;
delete p;     // prints ONLY "Base destructor"  ❌ Derived's memory leaks!
```
Fix: `virtual ~Base() { ... }`. Now `delete p;` prints `Derived destructor` and then `Base destructor` ✅.

**Rule:** if a class has any virtual function, **make its destructor virtual**.

---

## Part 12: Pure virtual functions and abstract classes

### The idea
Sometimes the base class **can't give a meaningful body** to a function. What should `Shape::draw()` draw? There's no such thing as a generic shape. But you still want to **force** every child class to have a `draw()`.

### Pure virtual function
A virtual function with **no body**, marked `= 0`:
```cpp
virtual void draw() = 0;
```
Syntax: `virtual return_type function_name(parameters) = 0;`

> A **pure virtual function** is a virtual function that has no definition in the base class and is declared by assigning `= 0`. It must be overridden by derived classes.

### Abstract class
> A class that contains **at least one pure virtual function** is called an **abstract class**. Objects of an abstract class **cannot be created**.

```cpp
class Shape {                        // abstract class
public:
    virtual void draw() = 0;         // pure virtual
    void info() { cout << "This is a shape\n"; }   // normal (concrete) function: allowed
    virtual ~Shape() {}
};

class Circle : public Shape {
public:
    void draw() override { cout << "Drawing a circle\n"; }
};

class Square : public Shape {
public:
    void draw() override { cout << "Drawing a square\n"; }
};

int main() {
    // Shape s;                     ❌ ERROR: can't create an object of an abstract class
    Shape* s1 = new Circle();       // ✅ pointers to an abstract class are allowed
    Shape* s2 = new Square();

    s1->draw();     // Drawing a circle
    s2->draw();     // Drawing a square
    s1->info();     // This is a shape

    delete s1;
    delete s2;
}
```

### Rules 🎯
1. You **can't create objects** of an abstract class.
2. You **can create pointers and references** to it, and that's how it's used for runtime polymorphism.
3. A derived class **must override all** pure virtual functions. If it misses even one, **it becomes abstract too**, and creating its objects gives a compile error.
4. An abstract class **can** have normal functions, data members and constructors.
5. A class whose functions are **all** pure virtual acts like an **interface** (a pure contract).

### Virtual vs pure virtual 🎯

| | Virtual function | Pure virtual function |
|---|---|---|
| Body in the base class | yes | no (`= 0`) |
| Must the derived class override it? | optional | **mandatory** (or the child is abstract) |
| Can you make objects of the base class? | yes | **no** (the class is abstract) |
| Syntax | `virtual void f() { ... }` | `virtual void f() = 0;` |

---

## Part 13: Compile time vs runtime: the full comparison

| Point | Compile-time polymorphism | Runtime polymorphism |
|---|---|---|
| Also called | static polymorphism, early binding | dynamic polymorphism, late binding |
| Decided when | during compilation | during execution |
| Achieved by | function overloading, operator overloading, templates | virtual functions, pure virtual functions (abstract classes) |
| Needs inheritance? | no | **yes** |
| Needs a pointer or reference? | no | **yes** (base-class pointer or reference) |
| Speed | faster | slightly slower (vtable lookup) |
| Flexibility | less | more (new classes plug in easily) |
| Example | `add(int,int)` vs `add(double,double)` | `Shape* s = new Circle; s->draw();` |

### Overloading vs overriding 🎯

| | Overloading | Overriding |
|---|---|---|
| Where | same class / scope | base class vs derived class |
| Parameters | must be **different** | must be the **same** |
| Inheritance needed? | no | yes |
| Polymorphism | compile-time | runtime (with `virtual`) |
| Keyword | none | `virtual` in the base (plus optional `override`) |

---

## How to write answers in the exam

For any "explain X" question:
1. **Definition:** one or two lines with keywords (many forms, compile-time or runtime, early or late binding).
2. **Diagram:** the polymorphism tree (Part 1) or the vtable picture (Part 11).
3. **Syntax**
4. **A short program with its output.** This is essential for operator overloading and virtual functions.
5. **Rules, or advantages**

### Expected long-answer questions
- What is polymorphism? Explain its types with a diagram.
- Explain function overloading with an example. Can we overload on return type?
- What is operator overloading? List its rules and the operators that can't be overloaded.
- Overload a unary operator (`++` prefix and postfix, or unary `-`) with a program.
- Overload a binary operator (`+` for Complex or Distance) with a program.
- Operator overloading using friend functions: why and when?
- Explain the `this` pointer with its uses.
- Explain pointers to derived classes (base pointer → derived object).
- What is a virtual function? Explain runtime polymorphism with a program.
- Pure virtual functions and abstract classes, with a program.
- Compile-time vs runtime polymorphism; overloading vs overriding.

### Quick viva one-liners
- **Polymorphism:** many forms; one interface, multiple implementations.
- **Early binding:** the call is resolved at compile time. **Late binding:** at runtime.
- **Can we overload on return type alone?** No.
- **How does the compiler tell prefix from postfix `++`?** Postfix has a dummy `int` parameter.
- **Operators that can't be overloaded:** `.` `.*` `::` `?:` `sizeof` `typeid`
- **Why use a friend for operator overloading?** When the left operand isn't an object of the class (`5 + obj`, `cout << obj`).
- **What is `this`?** A pointer to the object that called the member function.
- **Does a static function have `this`?** No.
- **Can a base pointer point to a derived object?** Yes (upcasting). The reverse needs a cast.
- **What is a vtable?** A table of virtual function addresses, one per class, used for late binding.
- **Can a constructor be virtual?** No. **A destructor?** Yes, and it should be when the class is used polymorphically.
- **What makes a class abstract?** At least one pure virtual function (`= 0`).
- **Can we create objects of an abstract class?** No, but we can create pointers and references to it.
- **What if a child doesn't override a pure virtual function?** The child becomes abstract too.

### Quick-revision checklist
- [ ] I can draw the polymorphism tree and explain compile-time vs runtime
- [ ] I can write an overloaded function and explain why return type alone isn't enough
- [ ] I can explain "polymorphism by parameter" (overloading and templates)
- [ ] I know the operator overloading rules and the 6 operators that can't be overloaded
- [ ] I know the argument counts: member (0/1) vs friend (1/2)
- [ ] I can write prefix and postfix `++`, and unary `-`
- [ ] I can write binary `+` for Complex and Distance, and `>` and `+=`
- [ ] I can explain why `5 + obj` and `cout << obj` need friend functions
- [ ] I can explain `this` with its 3 uses and know where it doesn't exist
- [ ] I know the base pointer → derived object rules, upcasting and downcasting
- [ ] I can write a virtual function program and explain the vtable
- [ ] I know why destructors should be virtual
- [ ] I can write an abstract class with a pure virtual function
