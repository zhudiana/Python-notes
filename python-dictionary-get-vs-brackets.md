# Python Dictionary: `get()` vs `[]`

When accessing dictionary values, you can use either square brackets (`[]`) or the `get()` method.

## Using `[]`

Using `[]` requires the key to exist. Otherwise, Python raises a `KeyError`.

```python
student = {
    "name": "Alice",
    "age": 21
}

print(student["name"])   # Alice
print(student["grade"])  # KeyError
```

## Using `get()`

`get()` returns the value if the key exists. If it doesn't, it returns `None` by default instead of raising a `KeyError`.

```python
student = {
    "name": "Alice",
    "age": 21
}

print(student.get("name"))   # Alice
print(student.get("grade"))  # None
```

You can also provide a default value to return when the key is missing.

```python
print(student.get("grade", "Not Assigned"))
# Not Assigned
```

## Why use `get()`?

- Avoids `KeyError` when a key doesn't exist.
- Makes your code cleaner when missing keys are expected.
- Lets you specify a sensible default value.

```python
inventory = {
    "apples": 10,
    "oranges": 5
}

# Using []
if "bananas" in inventory:
    print(inventory["bananas"])
else:
    print(0)

# Using get()
print(inventory.get("bananas", 0))

```

> **Rule of thumb:** Use `[]` when the key **must** exist. Use `get()` when the key **might not** exist.
