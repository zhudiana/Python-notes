# Python Decorators

## 1. What is a Function?

Normally we write functions like this:

```python
def greet():
    return "Hello"

print(greet())
```

Output

```
Hello
```

A function is simply a block of reusable code.

---

## 2. Functions are Objects

One of Python's most powerful features is that **functions are objects**.

That means you can:

- assign them to variables
- pass them into other functions
- return them from functions
- store them inside lists or dictionaries

Example:

```python
def greet():
    return "Hello"

say_hi = greet

print(greet())
print(say_hi())
```

Output

```
Hello
Hello
```

Notice we did **not** write `greet()` when assigning.

This:

```python
say_hi = greet
```

means

> Store the function itself.

Whereas this:

```python
say_hi = greet()
```

means

> Execute the function immediately and store its return value.

---

## 3. Functions Inside Functions

Python allows functions to be created inside other functions.

Example:

```python
def outer():

    def inner():
        print("Inside inner")

    inner()

outer()
```

Output

```
Inside inner
```

`inner()` only exists while `outer()` is running.

---

## 4. Returning Functions

Since functions are objects, we can return them.

Example:

```python
def outer():

    def inner():
        print("Hello")

    return inner

my_function = outer()

my_function()
```

Output

```
Hello
```

Notice:

```python
return inner
```

NOT

```python
return inner()
```

The first returns the function.

The second executes it immediately.

---

## 5. Passing Functions as Arguments

Functions can also be passed into other functions.

Example:

```python
def greet():
    print("Hello")

def execute(func):
    func()

execute(greet)
```

Output

```
Hello
```

Again notice:

```python
execute(greet)
```

NOT

```python
execute(greet())
```

---

## 6. What Problem Do Decorators Solve?

Imagine you have many functions.

```python
def add():
    print("Adding")

def delete():
    print("Deleting")

def update():
    print("Updating")
```

Now suppose you want to print

```
Starting...
```

before every function.

Without decorators:

```python
def add():
    print("Starting...")
    print("Adding")

def delete():
    print("Starting...")
    print("Deleting")

def update():
    print("Starting...")
    print("Updating")
```

This repeats the same code many times.

This is exactly the problem decorators solve.

---

## 7. What is a Decorator?

A decorator is simply a function that

- accepts another function
- adds extra behavior
- returns a new function

Think of it like wrapping a gift.

```
Original function
        ↓
Decorator wraps it
        ↓
New improved function
```

---

## Your First Decorator

```python
def decorator(func):

    def wrapper():
        print("Before function")

        func()

        print("After function")

    return wrapper
```

Now create a function.

```python
@decorator
def hello():
    print("Hello")
```

Run it.

```python
hello()
```

Output

```
Before function
Hello
After function
```

---

## What actually happens?

This:

```python
@decorator
def hello():
    print("Hello")
```

is exactly the same as writing

```python
def hello():
    print("Hello")

hello = decorator(hello)
```

Python replaces

```
hello
```

with

```
wrapper
```

When you call

```python
hello()
```

you're actually calling

```python
wrapper()
```

which then calls the original function.

---

## 8. Returning Values

Decorators often need to return whatever the original function returns.

Example

```python
def uppercase(func):

    def wrapper():
        return func().upper()

    return wrapper
```

Use it

```python
@uppercase
def greet():
    return "hello world"

print(greet())
```

Output

```
HELLO WORLD
```

---

## 9. Why Your First Attempt Didn't Work

Suppose you wrote

```python
def uppercase(func):
    return func().upper()
```

This immediately executes

```python
func()
```

during decoration.

So

```python
@uppercase
def greet():
    return "hello"
```

becomes

```python
greet = "HELLO"
```

Now

```python
greet()
```

tries to call a string.

Result

```
TypeError:
'str' object is not callable
```

Always return a function, not the result.

Correct:

```python
def uppercase(func):

    def wrapper():
        return func().upper()

    return wrapper
```

---

## 10. Decorators with Arguments

Suppose your function has parameters.

```python
def greet(name):
    print(f"Hello {name}")
```

This decorator won't work:

```python
def decorator(func):

    def wrapper():
        func()

    return wrapper
```

because

```
wrapper()
```

accepts no arguments.

Instead use

```python
def decorator(func):

    def wrapper(*args, **kwargs):

        print("Starting")

        result = func(*args, **kwargs)

        print("Finished")

        return result

    return wrapper
```

Now

```python
@decorator
def greet(name):
    print(f"Hello {name}")

greet("Alice")
```

Output

```
Starting
Hello Alice
Finished
```

---

## Why \*args and \*\*kwargs?

They allow your decorator to work with **any** function.

Whether the function has

```
0 arguments
1 argument
5 arguments
keyword arguments
```

the decorator still works.

---

## 11. Multiple Decorators

Decorators stack from bottom to top.

Example

```python
def star(func):

    def wrapper():
        print("*****")
        func()
        print("*****")

    return wrapper

def hash(func):

    def wrapper():
        print("#####")
        func()
        print("#####")

    return wrapper

@star
@hash
def hello():
    print("Hello")
```

Equivalent to

```python
hello = star(hash(hello))
```

Output

```
*****
#####
Hello
#####
*****
```

---
