# C++ 17 Feature: Variant

`std::variant` in C++ is essentially a type-safe union, which can hold an instance of any of the types specified in its template arguments list. The size of `std::variant` is determined in compile time, and no additional memory allocated in runtime.

## Example

```cpp
#include <variant>

class Square;
class Circle;

using Shape = std::variant<Cricle, Square>;

Shape shape = Circle(10);
```

## Accessing the Variant

- `std::visit`

  ```cpp
  // Note that here there are two versions of lambda generated - Cricle& and Square&.
  std::visit([](auto&& s) {
    std::cout << s << std::endl;
  }, shape);

  // or

  class Draw {
    void operator()(Circle c) {}
    void operator()(Square c) {}
  }

  std::visit(Draw{}, shape);
  ```

  You can pass a polymorphic lambda or a functional object.

- `std::get<T>`

  ```cpp
  std::get<0>(s);
  // or
  std::get<Circle>(s);
  ```

- `std::get_if<T>`

  ```cpp
  auto* ptr = std::get_if<Circle>(&s);
  ```
  A `nullptr` is return if the type `T` does not match the underlying type of `s`.