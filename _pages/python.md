---
title: "Python Cheat Sheet"
description: "Comprehensive Python reference covering modern syntax, standard library, virtual environments, and common patterns."
layout: default
---

## Virtual Environments

### venv (built-in)

```bash
python -m venv .venv                # Create environment
source .venv/bin/activate           # Activate (Linux/macOS)
.venv\Scripts\activate              # Activate (Windows)
deactivate                          # Deactivate
```

### uv (fast, by Astral)

```bash
uv venv                             # Create .venv
source .venv/bin/activate
uv pip install -r requirements.txt  # Fast pip replacement
uv pip compile requirements.in -o requirements.txt
uv pip sync requirements.txt        # Sync to exact versions
uv run python script.py             # Run in venv automatically
uv tool install ruff                # Install CLI tool
```

### conda

```bash
conda create -n myenv python=3.12   # Create environment
conda activate myenv                # Activate
conda deactivate                    # Deactivate
conda env list                      # List environments
conda env export > environment.yml  # Export
conda env create -f environment.yml # Recreate
conda install numpy pandas          # Install packages
```

## pip Operations

```bash
pip install package                 # Install package
pip install package==1.0.0          # Specific version
pip install "package>=1.0,<2.0"     # Version range
pip install -r requirements.txt     # From file
pip install -e ./myproject          # Editable install
pip install --user package          # User-level install
pip install --upgrade package       # Upgrade
pip install --pre package           # Include pre-releases

pip uninstall package               # Uninstall
pip freeze                          # Installed packages (exact versions)
pip freeze > requirements.txt       # Save to file
pip list                            # List installed (with versions)
pip show package                    # Package details
pip check                           # Check for dependency conflicts
pip cache purge                     # Clear download cache
```

## Type Hints

```python
# Basic types
x: int = 5
name: str = "hello"
items: list[str] = ["a", "b"]
scores: dict[str, float] = {"a": 1.0}
unique: set[int] = {1, 2, 3}

# Optional / Union
from typing import Optional, Union
val: Optional[str] = None            # str | None
val: Union[int, str] = "hello"       # int | str (Python 3.10+)

# Type aliases
Vector = list[float]
Matrix = list[Vector]

# Callable
from collections.abc import Callable
def apply(fn: Callable[[int, int], int], a: int, b: int) -> int:
    return fn(a, b)

# Any, Never
from typing import Any
data: Any

# Literal
from typing import Literal
status: Literal["active", "inactive"] = "active"

# TypeGuard (custom type narrowing)
from typing import TypeGuard
def is_str_list(val: list) -> TypeGuard[list[str]]:
    return all(isinstance(x, str) for x in val)

# Protocols (structural typing)
from typing import Protocol
class Drawable(Protocol):
    def draw(self) -> None: ...

# Generic classes
from typing import TypeVar, Generic
T = TypeVar("T")
class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: list[T] = []
    def push(self, item: T) -> None:
        self.items.append(item)

# ParamSpec (for decorators)
from typing import ParamSpec
P = ParamSpec("P")
```

## F-Strings

```python
name = "world"
n = 42

# Basic
f"Hello, {name}!"                   # 'Hello, world!'

# Expressions
f"2 + 2 = {2 + 2}"                 # '2 + 2 = 4'
f"{'hello'.upper()}"                # 'HELLO'

# Formatting
f"{n:05d}"                          # '00042'
f"{3.14159:.2f}"                    # '3.14'
f"{1_000_000:,}"                    # '1,000,000'
f"{0.85:.1%}"                       # '85.0%'
f"{42:#x}"                          # '0x2a'
f"{3.14:e}"                         # '3.140000e+00'

# Debug (Python 3.8+)
f"{n=}"                             # 'n=42'
f"{2 + 2 = }"                       # SyntaxError, use: f"{(2+2)=}"
f"{n = }"                           # 'n =42'

# Multiline
msg = f"""
Hello, {name}!
The answer is {n}.
"""

# Nested (Python 3.12+)
width = 10
f"{'value':>{width}}"               # Right-align in field
```

## Comprehensions

```python
# List comprehension
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
flat = [item for sublist in nested for item in sublist]
pairs = [(x, y) for x in range(3) for y in range(3)]

# Dict comprehension
{key: value for key, value in pairs}
{word: len(word) for word in ["hi", "hello", "hey"]}
{k: v for k, v in data.items() if v is not None}

# Set comprehension
{c for word in "hello world" for c in word}
lengths = {len(word) for word in ["hi", "hey", "hello"]}

# Generator expression (lazy)
total = sum(x**2 for x in range(1_000_000))
lines = (line.strip() for line in file)
```

## pathlib

```python
from pathlib import Path

p = Path("/home/user/documents")
f = Path("file.txt")

# Navigation
p / "subdir" / "file.txt"           # Path joining (operator)
p.parent                            # Parent directory
p.name                              # File name
p.stem                              # Name without suffix
p.suffix                            # File extension
p.parts                             # Tuple of path components

# Reading & writing
p.read_text(encoding="utf-8")       # Read as string
p.read_bytes()                      # Read as bytes
p.write_text("hello", encoding="utf-8")
p.write_bytes(b"hello")

# Directory operations
Path("dir").mkdir(exist_ok=True)    # Create directory
Path("dir").mkdir(parents=True, exist_ok=True)  # With parents
Path("dir").rmdir()                 # Remove empty directory

# File operations
p.exists()                          # Exists?
p.is_file()                         # Is regular file?
p.is_dir()                          # Is directory?
p.stat()                            # File stats (size, mtime, etc.)
p.rename("new_name.txt")            # Rename/move
p.unlink()                          # Delete file

# Globbing
Path(".").glob("*.py")              # Generator of matching paths
Path(".").rglob("*.py")             # Recursive glob
Path(".").glob("**/*.py")           # Also recursive

# Current / home
Path.cwd()                          # Current working directory
Path.home()                         # User home directory
```

## Dataclasses

```python
from dataclasses import dataclass, field, asdict, astuple

@dataclass
class Point:
    x: float
    y: float
    label: str = "origin"

@dataclass
class Team:
    name: str
    members: list[str] = field(default_factory=list)
    active: bool = True

    def __post_init__(self) -> None:
        """Run after __init__"""
        self.name = self.name.strip()

# Usage
p = Point(1.0, 2.0)
p2 = Point(1.0, 2.0, label="A")
p.x = 3.0                           # Mutable by default

# Frozen (immutable)
@dataclass(frozen=True, slots=True)
class Config:
    host: str
    port: int = 8080

# Utility
asdict(team)                        # Convert to dict
astuple(point)                      # Convert to tuple
```

## Context Managers

```python
# Basic usage
with open("file.txt") as f:
    data = f.read()

# Multiple resources
with open("in.txt") as fin, open("out.txt", "w") as fout:
    fout.write(fin.read())

# Custom context manager
from contextlib import contextmanager

@contextmanager
def timer(name: str):
    import time
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        print(f"{name}: {elapsed:.3f}s")

# Usage
with timer("operation"):
    do_something()

# suppress
from contextlib import suppress
with suppress(FileNotFoundError):
    os.remove("maybe_missing.txt")

# redirect_stdout / redirect_stderr
from contextlib import redirect_stdout
with open("log.txt", "w") as f:
    with redirect_stdout(f):
        print("This goes to file")
```

## Decorators

```python
import functools

# Basic decorator
def log_calls(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_calls
def greet(name: str) -> str:
    return f"Hello, {name}"

# Decorator with arguments
def retry(max_attempts: int = 3, delay: float = 1.0):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay)
            return None
        return wrapper
    return decorator

# Class-based decorator
class CountCalls:
    def __init__(self, func):
        self.func = func
        functools.update_wrapper(self, func)
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call #{self.count}")
        return self.func(*args, **kwargs)

# Built-in decorators
@property                             # Getter
@staticmethod                         # No self/cls
@classmethod                          # Receives class as first arg
@functools.lru_cache(maxsize=None)    # Memoization
@functools.singledispatch             # Overload by type
@functools.wraps                      # Preserve metadata
```

## asyncio Basics

```python
import asyncio

# Coroutines
async def fetch_data(url: str) -> str:
    await asyncio.sleep(1)  # Simulate I/O
    return f"Data from {url}"

async def main():
    # Run one
    result = await fetch_data("https://example.com")

    # Run concurrently
    results = await asyncio.gather(
        fetch_data("url1"),
        fetch_data("url2"),
        fetch_data("url3"),
    )

    # Run with timeout
    try:
        result = await asyncio.wait_for(fetch_data("url"), timeout=5.0)
    except asyncio.TimeoutError:
        print("Timed out")

    # Run in background
    task = asyncio.create_task(fetch_data("url"))
    # ... do other things ...
    result = await task

    # Multiple tasks with control
    tasks = [asyncio.create_task(fetch_data(f"url{i}")) for i in range(5)]
    done, pending = await asyncio.wait(tasks, timeout=3.0)
    for t in pending:
        t.cancel()

# Entry point
asyncio.run(main())
```

### asyncio Utilities

```python
# Queue
queue = asyncio.Queue()
await queue.put(item)
item = await queue.get()

# Semaphore (limit concurrency)
sem = asyncio.Semaphore(10)

async def limited_task():
    async with sem:
        await do_work()

# Lock
lock = asyncio.Lock()
async with lock:
    await modify_shared_resource()

# Event
event = asyncio.Event()
await event.wait()          # Block until set
event.set()                 # Unblock waiters
```

## Common Standard Library Modules

### os & pathlib

```python
import os
os.getcwd()                          # Current directory
os.chdir("/path")                    # Change directory
os.environ["VAR"]                    # Environment variables
os.path.exists("file")               # Exists check
os.path.isfile("file")               # Is file?
os.path.isdir("dir")                 # Is directory?
os.makedirs("a/b/c", exist_ok=True)  # Create dirs
os.remove("file")                    # Delete file
os.listdir(".")                      # List directory
os.walk(".")                         # Recursive directory walk
os.path.join("dir", "file.txt")      # Join paths (use pathlib instead)
os.path.basename("/a/b.txt")         # "b.txt"
os.path.dirname("/a/b.txt")          # "/a"
os.urandom(16)                       # Random bytes
```

### sys

```python
import sys
sys.argv                             # CLI arguments
sys.exit(0)                          # Exit
sys.path                             # Module search paths
sys.platform                         # OS platform
sys.version                          # Python version
sys.stdout.write("msg")              # Write to stdout
sys.executable                       # Python interpreter path
sys.getsizeof(obj)                   # Object size in bytes
```

### json

```python
import json

# Serialize
json.dumps({"key": "value"}, indent=2)
json.dump(data, open("out.json", "w"), indent=2)

# Deserialize
json.loads('{"key": "value"}')
json.load(open("data.json"))

# With custom types
json.dumps(obj, default=str)         # Fallback for non-serializable
json.loads(s, object_hook=dict)      # Custom object construction
```

### re (Regular Expressions)

```python
import re

re.match(r"\d+", "42abc")            # Match at start → Match object
re.search(r"\d+", "abc42def")        # Search anywhere → Match object
re.findall(r"\d+", "a1b22c333")      # → ["1", "22", "333"]
re.finditer(r"\d+", "a1b22")         # Iterator of Match objects
re.sub(r"\s+", " ", "a  b   c")     # → "a b c"
re.split(r",", "a,b,,c")             # → ["a", "b", "", "c"]

# Compiled (reuse for performance)
pattern = re.compile(r"\d+", re.IGNORECASE)
pattern.findall(text)

# Named groups
m = re.search(r"(?P<name>\w+)", "hello")
m.group("name")                      # → "hello"

# Flags: re.IGNORECASE, re.MULTILINE, re.DOTALL, re.VERBOSE
```

### datetime

```python
from datetime import datetime, date, timedelta, timezone

# Current
now = datetime.now()                 # Local time
now = datetime.now(timezone.utc)     # UTC
today = date.today()

# Create
dt = datetime(2024, 3, 15, 10, 30)
dt = datetime.fromisoformat("2024-03-15T10:30:00")

# Format
dt.strftime("%Y-%m-%d %H:%M:%S")    # → "2024-03-15 10:30:00"
dt.isoformat()                      # → "2024-03-15T10:30:00"

# Parse
datetime.strptime("2024-03-15", "%Y-%m-%d")

# Arithmetic
dt + timedelta(days=7)
dt - timedelta(hours=3)
(dt2 - dt1).total_seconds()

# Timestamp
dt.timestamp()                      # → float (Unix timestamp)
datetime.fromtimestamp(1710502200)  # → datetime
```

### collections

```python
from collections import Counter, defaultdict, deque, namedtuple

# Counter
Counter("abracadabra")               # → Counter({'a': 5, 'b': 2, ...})
Counter([1, 2, 2, 3, 3, 3])         # → Counter({3: 3, 2: 2, 1: 1})
c = Counter("aab")
c.most_common(2)                    # → [('a', 2), ('b', 1)]
c.update("abc")                     # Add counts

# defaultdict
d = defaultdict(list)
d["key"].append("value")            # Auto-creates empty list
d = defaultdict(int)
d["count"] += 1                     # Auto-creates 0

# deque (double-ended queue)
dq = deque([1, 2, 3])
dq.append(4)                        # Right end
dq.appendleft(0)                    # Left end
dq.pop()                            # Remove right
dq.popleft()                        # Remove left
deque(maxlen=5)                     # Fixed-size, auto-evicts

# namedtuple
Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
p.x, p.y                            # → 1, 2
```

### itertools

```python
import itertools

# Infinite
itertools.count(start=0, step=2)     # 0, 2, 4, 6, ...
itertools.cycle([1, 2, 3])          # 1, 2, 3, 1, 2, 3, ...
itertools.repeat(10, 3)             # 10, 10, 10

# Terminate on shortest
itertools.chain([1, 2], [3, 4])     # 1, 2, 3, 4
itertools.chain.from_iterable([[1], [2, 3]])
itertools.zip_longest([1, 2], [3, 4, 5], fillvalue=0)

# Filtering
itertools.islice(range(10), 2, 8, 2)  # 2, 4, 6
itertools.takewhile(lambda x: x < 5, range(10))  # 0,1,2,3,4
itertools.dropwhile(lambda x: x < 5, range(10))  # 5,6,7,8,9

# Grouping
itertools.groupby(sorted(data, key=func), key=func)

# Combinatorics
itertools.product([1, 2], [3, 4])   # (1,3),(1,4),(2,3),(2,4)
itertools.permutations([1, 2, 3], 2)  # 2-element permutations
itertools.combinations([1, 2, 3, 4], 2)  # 2-element combinations
itertools.combinations_with_replacement([1, 2], 2)

# Accumulate
list(itertools.accumulate([1, 2, 3, 4]))  # [1, 3, 6, 10]
```

### functools

```python
from functools import partial, reduce, lru_cache, wraps, singledispatch

partial(int, base=2)("1010")         # → 10 (partial application)
reduce(lambda a, b: a + b, [1, 2, 3, 4])  # → 10

@lru_cache(maxsize=128)
def fib(n: int) -> int:
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
fib.cache_clear()                    # Clear cache

@singledispatch
def process(val):
    raise NotImplementedError

@process.register(int)
def _(val: int):
    return val * 2

@process.register(str)
def _(val: str):
    return val.upper()
```

## Common Patterns

### Walrus Operator (Python 3.8+)

```python
if (n := len(items)) > 10:
    print(f"Too many: {n}")

while (line := f.readline().strip()):
    process(line)
```

### Match Statement (Python 3.10+)

```python
match status_code:
    case 200:
        print("OK")
    case 404:
        print("Not Found")
    case 500 | 502 | 503:
        print("Server Error")
    case code if 400 <= code < 500:
        print(f"Client Error: {code}")
    case _:
        print("Unknown")

# Structural pattern matching
match point:
    case (0, 0):
        print("Origin")
    case (x, 0):
        print(f"On x-axis: {x}")
    case Point(x=x, y=y):
        print(f"Point at ({x}, {y})")
```

### Exception Handling

```python
try:
    result = risky_operation()
except ValueError as e:
    logger.error(f"Invalid value: {e}")
    raise
except (TypeError, KeyError) as e:
    logger.error(f"Expected type or missing key: {e}")
except Exception:
    logger.exception("Unexpected error")
    raise
else:
    # Runs if no exception
    process(result)
finally:
    # Always runs (cleanup)
    cleanup()
```

### Enum

```python
from enum import Enum, StrEnum, auto

class Status(StrEnum):
    ACTIVE = "active"
    INACTIVE = "inactive"

class Color(Enum):
    RED = auto()
    GREEN = auto()
    BLUE = auto

Status.ACTIVE.value                  # → "active"
Status("active")                     # → Status.ACTIVE
```

### Ternary & Short-Circuit

```python
value = "yes" if condition else "no"
result = a and b                     # b if a is truthy, else a
result = a or b                      # a if a is truthy, else b
```

### Unpacking

```python
a, b, *rest = [1, 2, 3, 4, 5]       # a=1, b=2, rest=[3,4,5]
first, *middle, last = range(5)      # first=0, middle=[1,2,3], last=4
for key, value in data.items():
    ...
```
