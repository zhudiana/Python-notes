# Understanding `async` and `await` in Python

## What is Synchronous Programming?

By default, Python executes code **one statement at a time**, from top to bottom.

```python
print("A")
print("B")
print("C")
```

Output:

```
A
B
C
```

Python will never execute `B` until `A` finishes, and it won't execute `C` until `B` finishes.

Think of Python as a single worker:

```
Start
 ↓
Task A
 ↓
Task B
 ↓
Task C
 ↓
Finish
```

This execution model is called **synchronous programming**.

---

## What is a Blocking Operation?

A **blocking operation** is any operation that forces the program to stop and wait before moving on.

Example:

```python
import time

print("Start")

time.sleep(5)

print("Finished")
```

Output:

```
Start
(wait 5 seconds)
Finished
```

During those five seconds, Python is **doing absolutely nothing**.

It is simply waiting.

```
Start
 ↓
Wait 5 seconds
 ↓
Finished
```

This waiting is called **blocking** because the next line of code cannot execute until the current operation finishes.

---

## Why is Blocking a Problem?

Imagine downloading three files.

Each download takes **3 seconds**.

A synchronous program would do this:

```
Download File 1 (3 sec)
↓
Download File 2 (3 sec)
↓
Download File 3 (3 sec)
```

Total time:

```
9 seconds
```

During each download, Python isn't actually working. It is mostly waiting for the internet. The CPU sits idle.

That waiting time is wasted.

---

## Concurrency vs Parallelism

Although they sound similar, they are different.

## Concurrency

Concurrency means **making progress on multiple tasks by switching between them whenever one has to wait**.

Imagine cooking dinner.

You put pasta into boiling water.

Instead of standing there watching it boil, you start chopping vegetables.

When the pasta is ready, you return to it.

You are **not doing everything at exactly the same time**.

You are switching between tasks whenever one is waiting.

```
Task A
 ↓
(wait)
 ↓
Task B
 ↓
(wait)
 ↓
Task A
```

Only one task is actively running at a given moment.

---

## Parallelism

Parallelism means **multiple tasks are literally running at the same time**.

Imagine three chefs.

```
Chef 1 → Pasta

Chef 2 → Salad

Chef 3 → Dessert
```

Everything happens simultaneously.

Parallelism usually requires:

- Multiple CPU cores
- Multiple processes
- Multiple threads

---

### Quick Comparison

| Concurrency                        | Parallelism                                     |
| ---------------------------------- | ----------------------------------------------- |
| One worker switching between tasks | Multiple workers executing tasks simultaneously |
| Great for waiting on I/O           | Great for CPU-intensive work                    |
| Uses `asyncio`                     | Uses threads or processes                       |

**Async programming provides concurrency, not parallelism.**

---

## What is Asynchronous Programming?

Asynchronous programming allows Python to **work on another task while one task is waiting**.

Instead of saying:

> Wait here until this operation finishes.

Python says:

> While we're waiting, let's work on something else.

Imagine baking three pizzas.

A synchronous approach would be:

```
Put pizza in oven

Wait 20 minutes

Take it out

Put second pizza

Wait 20 minutes
```

A better approach:

```
Put Pizza 1 in oven

Prepare Pizza 2

Put Pizza 2 in oven

Prepare Pizza 3

Take Pizza 1 out

Continue...
```

Notice that the waiting time is being used productively.

This is the idea behind asynchronous programming.

---

## What is a Coroutine?

A **coroutine** is a special kind of function that can pause and resume its execution.

Normal function:

```python
def greet():
    print("Hello")
```

Coroutine:

```python
async def greet():
    print("Hello")
```

Unlike normal functions, calling a coroutine **does not immediately execute it**.

```python
async def greet():
    print("Hello")

result = greet()

print(result)
```

Output:

```
<coroutine object greet at ...>
```

Why?

Because calling a coroutine only creates it.

Think of it like a recipe.

Writing the recipe doesn't cook the meal.

Someone still needs to follow the recipe.

---

## The `async` Keyword

The keyword `async` tells Python:

> This function may pause while waiting.

Example:

```python
async def fetch_data():
    ...
```

An async function:

- Returns a coroutine object
- Can use `await`
- Must be executed by an event loop

Without `async`, Python will not allow the use of `await`.

---

## The `await` Keyword

`await` tells Python:

> Pause this coroutine until another asynchronous operation finishes.

Example:

```python
await asyncio.sleep(3)
```

`await` **does not freeze the whole program**.

It only pauses the current coroutine.

While that coroutine waits, the event loop is free to execute another coroutine.

Think of it like this:

```
Coroutine A

↓

Waiting...

↓

Coroutine B runs

↓

Coroutine A resumes
```

This is what makes asynchronous programming efficient.

---

## The Event Loop

The **event loop** is the engine that manages asynchronous tasks.

It repeatedly asks:

```
Is Task A ready?

No.

Is Task B ready?

Yes.

Run Task B.

Is Task C ready?

No.

Go back and check again.
```

It continuously switches between waiting tasks and ready tasks.

Without an event loop, asynchronous functions cannot run.

---

## Running an Async Program

Calling a coroutine is **not enough**.

This does **not** execute the function:

```python
async def hello():
    print("Hello")

hello()
```

Instead, use `asyncio.run()`:

```python
import asyncio

async def hello():
    print("Hello")

asyncio.run(hello())
```

Output:

```
Hello
```

`asyncio.run()` starts the event loop and executes the coroutine.

---

### A Complete Example

```python
import asyncio

async def task(name):
    print(f"{name} started")

    await asyncio.sleep(2)

    print(f"{name} finished")

async def main():
    await asyncio.gather(
        task("A"),
        task("B"),
        task("C")
    )

asyncio.run(main())
```

Output:

```
A started
B started
C started

(wait 2 seconds)

A finished
B finished
C finished
```

Without asynchronous programming, the tasks would run one after another:

```
A → 2 sec

B → 2 sec

C → 2 sec
```

Total time:

```
6 seconds
```

With `asyncio.gather()`:

```
All start together

↓

All wait together

↓

All finish together
```

Total time:

```
2 seconds
```

The waiting time overlaps, making the program much more efficient.

---

## When Should You Use Async?

Async is most useful when your program spends time waiting for external resources.

Examples include:

- Making API requests
- Downloading web pages
- Reading from databases
- Chat servers
- WebSockets
- Network communication
- FastAPI applications
- Calling multiple AI APIs

---

## When NOT to Use Async

Async is **not** designed to speed up CPU-heavy computations.

Examples:

- Machine Learning training
- Video editing
- Image processing
- Large mathematical calculations
- Data compression

For CPU-bound work, consider:

- `multiprocessing`
- Parallel computing
- Distributed computing

---

## Common Misconceptions

### "async makes my code faster."

Not always.

It only makes programs faster when they spend significant time **waiting**.

---

### "`await` blocks the program."

No.

It only pauses the current coroutine.

Other coroutines can continue running.

---

### "async runs everything simultaneously."

No.

It provides **concurrency**, not true parallel execution.

---

### "I should make every function async."

No.

Only functions that perform asynchronous operations should be asynchronous.

Making everything async usually adds unnecessary complexity.

---

# Further Reading

If you'd like to dive deeper into Python's asynchronous programming model, this Real Python article is an excellent next step:

[**Real Python – Async IO in Python: A Complete Walkthrough**](https://realpython.com/async-io-python/#a-first-look-at-async-io)
