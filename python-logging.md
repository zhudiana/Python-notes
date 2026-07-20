# Python Logging

> Logging is the process of recording events that happen while a program is running.
>
> Unlike `print()`, log messages can be filtered, formatted, stored in files, and used to understand what happened long after the program has finished.

---

# What is Logging?

Imagine you've built a FastAPI application.

A user tries to log in.

Everything works today.

Three days later they email you saying:

> "I can't log in anymore."

Without any record of what happened, you're left guessing.

Maybe:

- the database stopped responding
- the password was incorrect
- the server crashed
- the internet connection failed

A program needs a way to leave behind a record of what happened while it was running.

That record is called a **log**.

A log might look like this:

```text
2026-07-20 09:41:12 INFO User alice logged in

2026-07-20 09:42:33 WARNING Password entered incorrectly

2026-07-20 09:42:40 ERROR Database connection failed
```

Each line describes an event that occurred.

---

# Why `print()` Isn't Enough

Many beginners write programs like this:

```python
print("Connecting to database...")
print("Connected")
print("Downloading data...")
print("Finished")
```

This works for learning.

As projects become larger, it becomes difficult to manage hundreds or thousands of `print()` statements.

`print()` has several limitations:

- Messages disappear after the program ends.
- There is no timestamp.
- There are no severity levels.
- Messages cannot easily be saved to files.
- Every message looks the same.

Logging solves all of these problems.

---

# The `logging` Module

Python already includes a logging system.

```python
import logging
```

No installation is required.

---

# Your First Log Message

```python
import logging

logging.warning("Low disk space")
```

Output

```text
WARNING:root:Low disk space
```

The output contains three parts.

```
WARNING
```

The severity level.

```
root
```

The logger that created the message.

```
Low disk space
```

The actual message.

---

# Logging Levels

Not every event has the same importance.

Python categorizes messages into five levels.

| Level    | Purpose                                                 |
| -------- | ------------------------------------------------------- |
| DEBUG    | Detailed information while developing                   |
| INFO     | Normal program events                                   |
| WARNING  | Something unexpected happened but the program continues |
| ERROR    | An operation failed                                     |
| CRITICAL | The application cannot continue normally                |

---

## DEBUG

Used while developing.

```python
logging.debug("Reading configuration file")
```

Example:

```text
DEBUG Reading configuration file
```

---

## INFO

Used for normal events.

```python
logging.info("Server started")
```

Example:

```text
INFO Server started
```

---

## WARNING

Used when something isn't ideal but execution can continue.

```python
logging.warning("Configuration file not found. Using defaults.")
```

---

## ERROR

Used when something fails.

```python
logging.error("Unable to connect to database")
```

---

## CRITICAL

Used when the application encounters a serious failure.

```python
logging.critical("Application shutting down")
```

---

# The Root Logger

When calling

```python
logging.info("Program started")
```

you are using Python's default logger.

Internally, it behaves like:

```python
root_logger.info("Program started")
```

This default logger is called the **root logger**.

Small scripts often use the root logger directly.

Large applications usually create their own loggers.

---

# Creating Your Own Logger

```python
import logging

logger = logging.getLogger(__name__)
```

This is one of the most common lines you'll see in Python projects.

## Why use `__name__`?

Suppose this file is named

```
database.py
```

Then

```python
__name__
```

becomes

```
database
```

If the file is

```
users/auth.py
```

then

```python
__name__
```

becomes

```
users.auth
```

Now every log message records where it came from.

```python
logger.info("Connected")
```

Output

```text
INFO:database:Connected
```

instead of

```text
INFO:root:Connected
```

---

# Configuring Logging

The logging system can be configured once at the beginning of the program.

```python
import logging

logging.basicConfig(level=logging.INFO)
```

This tells Python:

> Ignore everything below INFO.

Now consider

```python
logging.debug("Debug")

logging.info("Info")

logging.warning("Warning")

logging.error("Error")
```

Output

```text
INFO:root:Info

WARNING:root:Warning

ERROR:root:Error
```

The DEBUG message does not appear because it is below the configured level.

---

# Formatting Log Messages

By default, logging uses a simple format.

You can customize it.

```python
logging.basicConfig(
    level=logging.INFO,
    format="%(levelname)s - %(message)s"
)
```

Output

```text
INFO - Connected
```

---

A more useful format:

```python
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s %(message)s"
)
```

Example output

```text
2026-07-20 10:12:41 INFO database Connected
```

---

# Common Formatting Fields

| Placeholder     | Meaning                      |
| --------------- | ---------------------------- |
| `%(asctime)s`   | Time the message was created |
| `%(levelname)s` | Logging level                |
| `%(name)s`      | Logger name                  |
| `%(message)s`   | Log message                  |
| `%(filename)s`  | File name                    |
| `%(lineno)d`    | Line number                  |
| `%(funcName)s`  | Function name                |

Example

```python
logging.basicConfig(
    format="%(filename)s:%(lineno)d %(message)s"
)
```

Output

```text
database.py:42 Connection established
```

---

# Writing Logs to a File

Instead of displaying messages in the terminal:

```python
logging.basicConfig(
    filename="app.log",
    level=logging.INFO
)
```

Now every message is written into

```
app.log
```

Example

```text
INFO User logged in

WARNING Invalid password

ERROR Database timeout
```

---

# The Logging Flow

Every log message follows the same path.

```
Logger
   │
   ▼
Creates a LogRecord
   │
   ▼
Optional Filters
   │
   ▼
Handler
   │
   ▼
Formatter
   │
   ▼
Console / File / Network / Email
```

Understanding this flow makes the entire logging module much easier to understand.

---

# Loggers

A **logger** creates log messages.

Think of it as a reporter.

```python
logger = logging.getLogger(__name__)
```

The logger decides:

- which messages to create
- their severity level
- where they should go

Different parts of an application usually have different loggers.

```
database

authentication

payment

api

users
```

---

# Log Records

Whenever you call

```python
logger.info("Connected")
```

Python creates an object called a **LogRecord**.

It stores information such as:

- timestamp
- logger name
- level
- filename
- function
- line number
- your message

A formatter later converts that object into readable text.

---

# Handlers

A handler decides **where** log messages go.

Common handlers include:

| Handler       | Destination    |
| ------------- | -------------- |
| StreamHandler | Terminal       |
| FileHandler   | File           |
| SMTPHandler   | Email          |
| HTTPHandler   | Web server     |
| SocketHandler | Network socket |

One logger can have multiple handlers.

```
Logger

├── Console

├── File

└── Email
```

The same message can appear in multiple places.

---

# Formatters

A formatter decides how a message looks.

Without a formatter:

```text
INFO Connected
```

With a formatter:

```text
2026-07-20 11:21:34 INFO database Connected
```

The information is identical.

Only the presentation changes.

---

# Filters

Filters decide whether a message should continue.

For example,

```
Only log ERROR messages.

Ignore DEBUG messages.

Ignore messages from payment.py.
```

Most beginners rarely need custom filters.

---

# Best Practices

## Configure logging once

Usually in `main.py`.

```python
logging.basicConfig(...)
```

---

## Create one logger per module

```python
logger = logging.getLogger(__name__)
```

---

## Choose appropriate log levels

Good:

```python
logger.info("User logged in")

logger.warning("Password incorrect")

logger.error("Database unavailable")
```

Avoid:

```python
logger.error("Program started")
```

Starting the program is not an error.

---

## Avoid using `print()` in production code

Use logging instead.

---

## Write meaningful messages

Bad

```python
logger.info("Done")
```

Better

```python
logger.info("Finished processing customer orders")
```

---

# Common Mistakes

## Forgetting to configure logging

```python
logging.info("Hello")
```

Nothing appears because the default level hides INFO messages.

---

## Using the wrong log level

Incorrect

```python
logger.error("Application started")
```

Correct

```python
logger.info("Application started")
```

---

## Using `print()` for debugging

Instead of

```python
print(user)
```

use

```python
logger.debug(user)
```

---

# Complete Example

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s [%(name)s] %(message)s"
)

logger = logging.getLogger(__name__)


def divide(a, b):
    logger.info("Dividing %s by %s", a, b)

    if b == 0:
        logger.error("Division by zero")
        return None

    result = a / b

    logger.info("Result = %s", result)

    return result


divide(10, 2)
divide(10, 0)
```

Output

```text
2026-07-20 11:32:11 INFO [__main__] Dividing 10 by 2

2026-07-20 11:32:11 INFO [__main__] Result = 5.0

2026-07-20 11:32:11 INFO [__main__] Dividing 10 by 0

2026-07-20 11:32:11 ERROR [__main__] Division by zero
```

---

# Summary

- Logging records events while a program runs.
- `print()` is useful for learning but not for maintaining applications.
- Every log message has a severity level.
- `basicConfig()` configures the logging system.
- `getLogger(__name__)` creates a logger associated with a module.
- A logger creates a `LogRecord`.
- A handler decides where the message is sent.
- A formatter controls how the message is displayed.
- Filters decide which messages are allowed through.
- Most Python projects configure logging once and create one logger per module.

---

# Quick Reference

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s [%(name)s] %(message)s"
)

logger = logging.getLogger(__name__)

logger.debug("Detailed information")

logger.info("Normal event")

logger.warning("Unexpected situation")

logger.error("Operation failed")

logger.critical("Application cannot continue")
```

---

## Official Documentation

The Python documentation describes every class, function, and configuration option in detail.

Once the concepts in this note are familiar, the official reference becomes much easier to follow.

- [Python Logging Documentation](https://docs.python.org/3/library/logging.html)
