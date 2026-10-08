# Python `@classmethod` Decorator

## What is `@classmethod`?

`@classmethod` defines a method that belongs to the **class itself** rather than to an individual object.

- A regular method receives `self` (the **object**).
- A class method receives `cls` (the **class**).

Python passes `cls` automatically, just like it passes `self`. You never type it when calling the method.

---

## Regular method vs class method

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):                 # regular method: gets the OBJECT (self)
        return f"Hi, I'm {self.name}"

    @classmethod
    def describe(cls):               # class method: gets the CLASS (cls)
        return f"This is the {cls.__name__} class"


p = Person("Ana", 30)
print(p.greet())          # Hi, I'm Ana               (needs an object)
print(Person.describe())  # This is the Person class  (called on the class, no object needed)
```

|                | First parameter     | Can access                      | Usually called on                   |
| -------------- | ------------------- | ------------------------------- | ----------------------------------- |
| Regular method | `self` (the object) | the object's data (`self.name`) | an object                           |
| `@classmethod` | `cls` (the class)   | the class and its shared data   | the class (also works on an object) |

---

## Most common use: alternative constructors

Normally you create a `Person` with a name and an age. But sometimes your data arrives in a different shape, like a string `"Ben-25"`.

A class method lets you add a **second way to build the object**:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def from_string(cls, text):
        name, age = text.split("-")
        return cls(name, int(age))    # same as Person(name, int(age))


p1 = Person("Ana", 30)               # normal way
p2 = Person.from_string("Ben-25")    # alternative way

print(p2.name, p2.age)               # Ben 25
```

### Step by step: `Person.from_string("Ben-25")`

1. You call the method on the **class** `Person`, not on an object.
2. Python automatically passes the class as the first argument, so `cls = Person`.
3. `text` receives `"Ben-25"`.
4. The method splits the string: `name = "Ben"`, `age = "25"`.
5. `cls(name, int(age))` creates a new `Person`, exactly like `Person("Ben", 25)`.
6. The new object is returned and stored in `p2`.

> Naming convention: alternative constructors usually start with `from_` (`from_string`, `from_dict`, `from_file`).

---

## Why `cls(...)` instead of `Person(...)`?

Because of **inheritance**. When a subclass uses the method, `cls` becomes the subclass, so you get the correct type back.

```python
class Student(Person):
    pass

s = Student.from_string("Cara-20")
print(type(s))    # <class '__main__.Student'>   (correct!)
```

If the method had hardcoded `Person(...)`, then `s` would wrongly be a `Person`, not a `Student`.

---

## Another use: working with class-level data

Class-level data is shared by **all** objects of the class. A class method can read or change it.

```python
class Person:
    count = 0                         # shared by the whole class

    def __init__(self, name):
        self.name = name
        Person.count += 1

    @classmethod
    def total_people(cls):
        return cls.count


Person("Ana")
Person("Ben")
print(Person.total_people())    # 2
```

The method needs the class's shared data, not any single object's data, so `@classmethod` is the right fit.
