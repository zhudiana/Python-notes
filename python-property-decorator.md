# Python `@property` Decorator

## What is `@property`?

`@property` lets you write a **method** inside a class but use it like a **normal attribute** (no parentheses).

```python
obj.area        # looks like a variable
obj.area()      # NOT how you use a property
```

From the outside it looks like a simple variable. Behind the scenes, Python runs a function.

---

## Why use it?

Two main reasons:

1. **Computed values**: a value that depends on other values and should always be up to date.
2. **Validation**: check or modify a value when someone assigns it.

Without `@property` you would need methods like `get_age()` and `set_age(5)`. With it, you keep the clean `obj.age` and `obj.age = 5` syntax.

---

## Example: read-only computed value

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * self.radius ** 2


c = Circle(5)
print(c.area)    # 78.53975  (no parentheses!)

c.radius = 10
print(c.area)    # 314.159   (automatically recalculated)
```

**What happens:**

- `area` is never stored anywhere.
- Every time you write `c.area`, Python calls the method and calculates the result from the current `radius`.
- It is always up to date.

**Read-only:** because there is no setter, this fails:

```python
c.area = 100     # AttributeError: property 'area' ... has no setter
```

---

## The setter

### The problem

With a normal attribute, Python accepts anything:

```python
class Person:
    def __init__(self, age):
        self.age = age

p = Person(30)
p.age = -5       # accepted, even though it makes no sense
```

### What a setter does

A setter is a function that Python runs **automatically every time someone assigns a value** to that attribute (`p.age = something`).

It intercepts the assignment so you can check or change the value **before** it is stored.

> **Analogy:** a security guard at a door. Without a setter, anyone walks in. With a setter, every value must pass the guard first.

### Full example (getter + setter)

```python
class Person:
    def __init__(self, age):
        self.age = age               # triggers the setter below

    @property
    def age(self):                   # GETTER: runs when you READ p.age
        return self._age

    @age.setter
    def age(self, value):            # SETTER: runs when you WRITE p.age = ...
        if value < 0:
            raise ValueError("Age can't be negative")
        self._age = value
```

---

## Step by step: what Python does

### Creating the object

```python
p = Person(30)
```

1. `__init__` runs `self.age = 30`.
2. Python sees `age` is a property with a setter, so it does **not** store the value directly.
3. It calls the setter with `value = 30`.
4. The setter checks `30 < 0`, which is `False`, so it continues.
5. The setter stores the value in `self._age`.

### Reading the value

```python
print(p.age)
```

1. Python sees `age` is a property.
2. It calls the getter.
3. The getter returns `self._age`, which is `30`.

### Assigning a bad value

```python
p.age = -5
```

1. Python calls the setter with `value = -5`.
2. `-5 < 0` is `True`, so it raises `ValueError`.
3. `self._age` is **never changed**. It stays `30`.

### Assigning a good value

```python
p.age = 31
```

1. Setter is called with `value = 31`.
2. Check passes.
3. `self._age` becomes `31`.

---

## Why two names: `age` and `_age`?

There are two different things:

| Name   | Role                                                                   |
| ------ | ---------------------------------------------------------------------- |
| `age`  | The **property**, the public door that outside code uses               |
| `_age` | The **actual storage**, the room behind the door where the value lives |

The leading underscore is a convention meaning "internal, please don't touch directly." It is not enforced by Python.

### Common mistake: infinite recursion

```python
@age.setter
def age(self, value):
    self.age = value      # WRONG
```

`self.age = value` calls the setter again, which calls itself again, forever (`RecursionError`).

**Always store the value under a different name** (like `self._age`).

---

## Do the names have to match?

**Yes.** The getter, the setter, and the name in the decorator must all use the **same name**. That name is what outside code uses (`p.age`).

```python
@property
def age(self): ...          # getter, name: age

@age.setter                 # refers to the property named "age"
def age(self, value): ...   # setter, name: age
```

### What goes wrong with different names

```python
@property
def age(self):
    return self._age

@age.setter
def set_age(self, value):   # different name!
    self._age = value
```

- `age` stays a **read-only** property (no setter attached to it).
- A _new_ property called `set_age` gets created instead.
- `self.age = age` in `__init__` then fails with an `AttributeError`.

### What can be named freely

| Thing                            | Must match?                                       |
| -------------------------------- | ------------------------------------------------- |
| Getter function name (`def age`) | Yes                                               |
| Setter function name (`def age`) | Yes                                               |
| Name in `@age.setter`            | Yes                                               |
| Storage attribute (`self._age`)  | **No**, any name works if getter and setter agree |

This is perfectly valid:

```python
@property
def age(self):
    return self._years

@age.setter
def age(self, value):
    self._years = value
```
