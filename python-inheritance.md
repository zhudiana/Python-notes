# Python Inheritance

## What is inheritance?

Inheritance lets one class **reuse** the attributes and methods of another class.

- **Parent class** (also called base class or superclass): the class being inherited from.
- **Child class** (also called subclass or derived class): the class that inherits.

The child gets everything the parent has, and can **add new things** or **change existing things**.

> **Analogy:** a child inherits traits from a parent, but can also have its own traits.

---

## Basic syntax

Put the parent class name in parentheses after the child class name:

```python
class Parent:
    ...

class Child(Parent):
    ...
```

---

## Simplest example

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        return f"{self.name} is eating"


class Dog(Animal):          # Dog inherits from Animal
    pass                    # nothing new added


d = Dog("Rex")
print(d.name)               # Rex
print(d.eat())              # Rex is eating
```

`Dog` has no code of its own, yet it can use `__init__`, `name`, and `eat()` because it inherited them from `Animal`.

**What Python does for `d.eat()`:**

1. Looks for `eat` in `Dog`. Not found.
2. Looks in the parent `Animal`. Found, so it runs it.

---

## 4. Adding new methods to the child

The child can have extra methods that the parent does not have:

```python
class Dog(Animal):
    def bark(self):
        return f"{self.name} says Woof!"


d = Dog("Rex")
print(d.eat())      # inherited from Animal
print(d.bark())     # defined in Dog
```

This only goes one way: the parent does **not** get the child's methods.

```python
a = Animal("Generic")
a.bark()            # AttributeError: Animal has no bark()
```

---

## Overriding methods (changing parent behavior)

If the child defines a method with the **same name** as the parent's, the child's version is used instead.

```python
class Animal:
    def speak(self):
        return "Some sound"


class Dog(Animal):
    def speak(self):            # overrides Animal.speak
        return "Woof!"


class Cat(Animal):
    def speak(self):            # overrides Animal.speak
        return "Meow!"


print(Animal().speak())    # Some sound
print(Dog().speak())       # Woof!
print(Cat().speak())       # Meow!
```

**Why:** Python searches the child first, so the child's version is found before the parent's.

---

## `super()`: calling the parent's code

Sometimes the child wants to **keep** the parent's behavior and **add** to it. `super()` gives access to the parent class.

### The most common use: `__init__`

```python
class Animal:
    def __init__(self, name):
        self.name = name


class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)      # let Animal set up the name
        self.breed = breed          # Dog adds its own attribute


d = Dog("Rex", "Labrador")
print(d.name)      # Rex       (set by Animal's __init__)
print(d.breed)     # Labrador  (set by Dog's __init__)
```

### Step by step: `Dog("Rex", "Labrador")`

1. Python calls `Dog.__init__` with `name = "Rex"`, `breed = "Labrador"`.
2. `super().__init__(name)` runs the **parent's** `__init__`, which sets `self.name = "Rex"`.
3. Back in `Dog.__init__`, `self.breed = "Labrador"` is set.
4. The object ends up with both `name` and `breed`.

### Common mistake: forgetting `super().__init__()`

```python
class Dog(Animal):
    def __init__(self, name, breed):
        self.breed = breed          # forgot super().__init__(name)


d = Dog("Rex", "Labrador")
print(d.name)      # AttributeError: 'Dog' object has no attribute 'name'
```

When a child defines its own `__init__`, the parent's `__init__` is **not** run automatically. You must call it with `super().__init__(...)`.

### Using `super()` in other methods

```python
class Animal:
    def speak(self):
        return "Some sound"


class Dog(Animal):
    def speak(self):
        return super().speak() + " ... actually, Woof!"


print(Dog().speak())    # Some sound ... actually, Woof!
```

---

## Checking types: `isinstance` and `issubclass`

```python
d = Dog("Rex", "Labrador")

print(isinstance(d, Dog))          # True
print(isinstance(d, Animal))       # True   (a Dog IS an Animal)
print(isinstance(Animal("x"), Dog))# False  (an Animal is not necessarily a Dog)

print(issubclass(Dog, Animal))     # True
```

A child object counts as an instance of **both** its own class and its parent class.
