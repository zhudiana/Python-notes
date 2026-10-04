# Python `__str__()`

## What is `__str__()`?

`__str__()` is a **special (dunder) method** in Python that defines the **human-readable representation of an object**.

When Python needs to display an object as a string, `__str__()` tells it **what text to show**.

The basic structure is:

```python
def __str__(self):
    return "some text"
```

---

## Custom `__str__()`

Suppose we create a `Person` class:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

If we display a `Person` object, Python doesn't automatically know how we want the object to look.

We can define `__str__()`:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name} is {self.age} years old."
```

Now:

```python
person = Person("Judiana", 26)

print(person)
```

Output:

```text
Judiana is 26 years old.
```

The `__str__()` method controls this output.

---

## How it works

When we write:

```python
print(person)
```

Python needs a readable representation of `person`.

It uses the object's `__str__()` method:

```text
print(person)
      ↓
person.__str__()
      ↓
"Judiana is 26 years old."
```

So `__str__()` is essentially saying:

> **"When someone wants to see this object as text, show them this."**

---

## Why use a custom `__str__()`?

Without a custom `__str__()`, a class may produce an output like:

```text
<__main__.Person object at 0x000001...>
```

This tells us that the object exists, but it isn't very useful to a human.

With:

```python
def __str__(self):
    return f"{self.name} is {self.age} years old."
```

we get something meaningful:

```text
Judiana is 26 years old.
```

---

## `__str__()` must return a string

The return value **must be a string**.

Correct:

```python
def __str__(self):
    return f"{self.name}, {self.age}"
```

Incorrect:

```python
def __str__(self):
    return self.age
```

If `self.age` is an integer, this is invalid because `__str__()` must return a `str`.

You can convert it:

```python
def __str__(self):
    return str(self.age)
```

or use an f-string:

```python
def __str__(self):
    return f"{self.age}"
```
