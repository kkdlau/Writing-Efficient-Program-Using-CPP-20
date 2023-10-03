# Virtual Function and Virtual Table

A virtual function is a special type of function in C++ and other object-oriented programming languages that is declared within a base class and can be overridden in a derived class.

Virtual functions enable polymorphism, a core principle of object-oriented programming. Polymorphism allows objects of different classes to be treated as objects of a common superclass, while still being able to call the correct implementation of a function.

## Usage

```cpp
class Base {
public:
    virtual void foo() { 
        cout << "Base::foo() called." << endl; 
    }
};

class Derived : public Base {
public:
    void foo() override { 
        cout << "Derived::foo() called." << endl; 
    }
};
```

```cpp
static_cast<Base>(Derived{}).foo();
```

Expect to see `Derived::foo() called.`.

## How Virtual Function is Implemented?

The implmentation of the virtual function depends on the compiler. But in general, virtual functions in C++ are implemented using a mechanism known as a "vtable" (virtual table), and "vptr" (virtual pointer).

### Virtual Table

You can consider a virtual table is a table contains all the pointers to the virtual function:
```cpp
/**
 * Class Base virtual table:
 * - Base::f1
 * - Base::f2
 */
class Base {
public:
  Base() {}
  virtual void f1() {

  }

  virtual void f2() {

  }
}
```

When the user creates another class, deriving the base class `Base`, the compiler will construct another virtual table:
```cpp
/**
 * Class Derived virtual table:
 * - Derived::f1
 * - Base::f2
 */
class Derived: public Base {
public:
  Derived() {}
  virtual void f1() {

  }
}
```

The virtual table for `Base` first gets copied, and replace its virtual function entries by the virtual functions defined in `Derived`. In this case, `Base::f1` is replaced by `Derived::f1`.

### Virtual Pointer

After the compiler constructed the virtual table, the compiler then inserts a special pointer called "virtual pointer":
```cpp
class Derived: public Base {
  // insert by the compiler
  VirtualPointer _vptr; // points to Derived's virtual table
public:
  Derived(): _vptr{/* filled by the compiler */} {}

  virtual void f1() {

  }
}
```

Later when we involve the virtual function:
```cpp
Base* b = new Derived{};
b->f1();
```

The compiler will translate the above statement into below:
```cpp
b->_vptr[0](); // f1 is located at the first entry, as such the index is 0.
```

Compared by calling functions that can resolved in compile time, calling a virtual function requires extra steps:
- Move the value of `_vptr` into cache.
- Read `_vptr`.
- from the address provided `_vptr`, jump to that address and copy the virtual table into cache.
- Read the target entry.
- Now you know where is the virtual function, jump to the memory and execute the instruction.

## Reference

- http://www.dietmar-kuehl.de/mirror/c++-faq/virtual-functions.html#faq-20.3