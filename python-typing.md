# Python Type Hints (Typing)

Type hints allow you to specify the expected type of variables, function parameters, and return values.

They **do not enforce types at runtime**. Instead, they improve:

- Code readability
- IDE autocomplete
- Static type checking (e.g. mypy)
- Team collaboration
- Easier debugging

---

# Basic Type Hints

```python
name: str = "Alice"
age: int = 22
height: float = 1.75
is_student: bool = True
```

---

# Function Type Hints

Specify parameter types and return type.

```python
def greet(name: str) -> str:
    return f"Hello {name}"
```

```python
def add(a: int, b: int) -> int:
    return a + b
```

---

# Functions Returning Nothing

Use `None`.

```python
def print_name(name: str) -> None:
    print(name)
```

---

# Multiple Possible Types

Use `|` (Python 3.10+).

```python
def square(x: int | float) -> int | float:
    return x * x
```

Older syntax:

```python
from typing import Union

def square(x: Union[int, float]) -> Union[int, float]:
    return x * x
```

---

# Optional Values

Use `None` when a value may be missing.

```python
def greet(name: str | None) -> str:
    if name is None:
        return "Hello!"
    return f"Hello {name}"
```

Equivalent:

```python
from typing import Optional

def greet(name: Optional[str]) -> str:
    ...
```

---

# List

```python
numbers: list[int] = [1, 2, 3]
names: list[str] = ["Alice", "Bob"]
```

---

# Tuple

```python
point: tuple[int, int] = (10, 20)
```

Different types:

```python
person: tuple[str, int] = ("Alice", 23)
```

---

# Dictionary

```python
scores: dict[str, int] = {
    "Alice": 95,
    "Bob": 88
}
```

---

# Set

```python
unique_ids: set[int] = {1, 2, 3}
```

---

# Callable

For functions passed as arguments.

```python
from collections.abc import Callable

def apply(func: Callable[[int], int], x: int) -> int:
    return func(x)
```

Example:

```python
def double(x: int) -> int:
    return x * 2

apply(double, 5)
```

---

# Any

Use when a variable can literally be anything.

```python
from typing import Any

value: Any = 10
value = "hello"
value = [1, 2, 3]
```

Avoid `Any` unless necessary because it disables most type checking.

---

# Type Aliases

Useful for long types.

```python
type Scores = dict[str, int]

student_scores: Scores = {
    "Alice": 90,
    "Bob": 85
}
```

Older syntax:

```python
Scores = dict[str, int]
```

---

# Generic Types

Works with many data types.

```python
from typing import TypeVar

T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]
```

Examples:

```python
first([1, 2, 3])

first(["a", "b", "c"])
```

---

# Literal Values

Restrict allowed values.

```python
from typing import Literal

def set_mode(mode: Literal["train", "test"]) -> None:
    print(mode)
```

Valid:

```python
set_mode("train")
```

Invalid:

```python
set_mode("hello")
```

---

# Typed Dictionary

Useful when dictionaries have fixed keys.

```python
from typing import TypedDict

class Student(TypedDict):
    name: str
    age: int

student: Student = {
    "name": "Alice",
    "age": 22
}
```

---

# Type Checking with isinstance()

Type hints don't replace runtime checks.

```python
def square(x: int | float) -> float:
    if not isinstance(x, (int, float)):
        raise TypeError("Must be a number")

    return x * x
```

---

# Best Practices

1. Always type function parameters.

```python
def add(a: int, b: int) -> int:
    return a + b
```

---

2. Type return values.

```python
def load_data() -> list[str]:
    ...
```

---

3. Prefer concrete types.

```python
list[int]
dict[str, float]
```

instead of

```python
Any
```

---

4. Use `|` instead of `Union` if using Python 3.10+.

```python
str | None
```

instead of

```python
Optional[str]
```

---

# Cheat Sheet

| Type           | Syntax                         | Notes                                    |
| -------------- | ------------------------------ | ---------------------------------------- |
| int            | `x: int`                       | Integer values                           |
| float          | `x: float`                     | Decimal values                           |
| str            | `x: str`                       | Text values                              |
| bool           | `x: bool`                      | `True` or `False`                        |
| list           | `list[int]`                    | List of integers                         |
| tuple          | `tuple[int, str]`              | Fixed-size ordered values                |
| dict           | `dict[str, int]`               | Key/value pairs                          |
| set            | `set[int]`                     | Unique values                            |
| None           | `-> None`                      | Function returns nothing                 |
| Multiple types | `int \| float`                 | Use `\|` for union types in Python 3.10+ |
| Optional       | `str \| None`                  | Value may be missing                     |
| Callable       | `Callable[[int], int]`         | Function type                            |
| Any            | `Any`                          | Disables strict type checking            |
| Literal        | `Literal["train", "test"]`     | Restrict to specific values              |
| TypedDict      | Fixed dictionary structure     | Dictionary with defined keys             |
| Type Alias     | `type Scores = dict[str, int]` | Create a reusable type name              |
| Generic        | `TypeVar("T")`                 | Works across multiple types              |

---

## Remember

Type hints **do not change how Python executes your code**.

This is perfectly valid:

```python
def add(a: int, b: int) -> int:
    return a + b

print(add("3", "4"))
```

Output:

```text
34
```

Python doesn't enforce the hints at runtime. They're primarily for developers, IDEs, and static type checkers.
