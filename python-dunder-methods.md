# Python Dunder (Magic) Methods

## What are dunder methods?

"Dunder" is short for **d**ouble **under**score. These are special methods with names like `__add__` or `__str__` (two underscores on each side).

You almost never call them directly. **Python calls them for you** when you use certain syntax:

| You write             | Python secretly calls |
| --------------------- | --------------------- |
| `Point(1, 2)`         | `__init__`            |
| `a + b`               | `a.__add__(b)`        |
| `a == b`              | `a.__eq__(b)`         |
| `a < b`               | `a.__lt__(b)`         |
| `len(a)`              | `a.__len__()`         |
| `print(a)` / `str(a)` | `a.__str__()`         |

**The idea:** by defining these methods in your class, you decide how your objects behave with Python's built-in operators and functions.

> You have already used one: `__init__` is a dunder method, called automatically when you create an object.

---

## `__add__`: the `+` operator

### The problem

Python does not know how to add your own objects:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p1 = Point(1, 2)
p2 = Point(3, 4)
p3 = p1 + p2      # TypeError: unsupported operand type(s) for +: 'Point' and 'Point'
```

### The fix

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)


p1 = Point(1, 2)
p2 = Point(3, 4)
p3 = p1 + p2

print(p3.x, p3.y)    # 4 6
```

### Step by step: `p1 + p2`

1. Python sees `+` between two objects.
2. It calls `p1.__add__(p2)`, so `self = p1` (left side) and `other = p2` (right side).
3. The method computes `1 + 3 = 4` and `2 + 4 = 6`.
4. It creates and returns a **new** `Point(4, 6)`.
5. The new object is stored in `p3`. `p1` and `p2` are unchanged.

### Notes

- `__add__` should usually **return a new object**, not modify `self`.
- `other` is just a parameter name. `other` is the convention.
- `p1 + 5` would fail with an `AttributeError`, because `5` has no `.x`. Check the type first:

```python
def __add__(self, other):
    if not isinstance(other, Point):
        return NotImplemented
    return Point(self.x + other.x, self.y + other.y)
```

`NotImplemented` tells Python "I don't know how to do this", and Python then raises a clean `TypeError`.

### Other math operators work the same way

| Operator | Method        |
| -------- | ------------- |
| `a + b`  | `__add__`     |
| `a - b`  | `__sub__`     |
| `a * b`  | `__mul__`     |
| `a / b`  | `__truediv__` |

---

## `__str__`: what `print()` shows

### The problem

Printing an object gives an unhelpful result:

```python
p = Point(1, 2)
print(p)      # <__main__.Point object at 0x000001F4A3B2C1D0>
```

### The fix

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"Point({self.x}, {self.y})"


p = Point(1, 2)
print(p)         # Point(1, 2)
print(str(p))    # Point(1, 2)
```

**What happens:** `print(p)` calls `p.__str__()` and prints the string it returns.

**Rule:** `__str__` must **return** a string, not print one.

---

## `__repr__`: the developer-friendly version

`__repr__` is similar to `__str__`, but meant for **developers** (debugging). It is used when you inspect an object in the console, and when it appears inside a list.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"


points = [Point(1, 2), Point(3, 4)]
print(points)     # [Point(1, 2), Point(3, 4)]
```

| Method     | Audience                 | Used by                  |
| ---------- | ------------------------ | ------------------------ |
| `__str__`  | users (readable)         | `print()`, `str()`       |
| `__repr__` | developers (unambiguous) | console, lists, `repr()` |

**Tip:** if you only define one, define `__repr__`. When `__str__` is missing, Python falls back to `__repr__` for `print()` too.

---

## `__eq__`: the `==` operator

### The problem

By default, `==` checks whether two variables are the **exact same object**, not whether they have the same data:

```python
p1 = Point(1, 2)
p2 = Point(1, 2)
print(p1 == p2)    # False  (different objects, even with same values)
```

### The fix

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return self.x == other.x and self.y == other.y


p1 = Point(1, 2)
p2 = Point(1, 2)
p3 = Point(5, 5)

print(p1 == p2)    # True
print(p1 == p3)    # False
```

**What happens:** `p1 == p2` calls `p1.__eq__(p2)`, which compares the data.

> **Heads up:** once you define `__eq__`, Python makes your objects **unhashable** by default, so they cannot be used in sets or as dictionary keys unless you also define `__hash__`. You can ignore this until you need it.

---

## `__lt__`: the `<` operator

Defines how objects are compared with `<`. It also lets `sorted()` order your objects.

```python
class Student:
    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

    def __lt__(self, other):
        return self.grade < other.grade

    def __repr__(self):
        return f"Student({self.name}, {self.grade})"


s1 = Student("Ana", 80)
s2 = Student("Ben", 90)

print(s1 < s2)                 # True
print(sorted([s2, s1]))        # [Student(Ana, 80), Student(Ben, 90)]
```

Related methods: `__le__` (`<=`), `__gt__` (`>`), `__ge__` (`>=`).

---

## `__len__`: the `len()` function

```python
class Playlist:
    def __init__(self):
        self.songs = []

    def add(self, song):
        self.songs.append(song)

    def __len__(self):
        return len(self.songs)


pl = Playlist()
pl.add("Song A")
pl.add("Song B")

print(len(pl))    # 2
```

**What happens:** `len(pl)` calls `pl.__len__()`. It must return an integer.

---

## All together: one class

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"


a = Point(1, 2)
b = Point(3, 4)

print(a + b)                  # Point(4, 6)
print(a == Point(1, 2))       # True
print([a, b])                 # [Point(1, 2), Point(3, 4)]
```
