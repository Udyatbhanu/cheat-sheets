# Python Mastery Cheat Sheet

A single-file reference covering the language, the standard library, tooling, idioms,
concurrency, typing, testing, performance and design patterns. Targets Python 3.12+
(version-specific features are flagged).

**Contents**

1. [Running Python & Tooling](#1-running-python--tooling)
2. [Syntax Basics](#2-syntax-basics)
3. [Built-in Types](#3-built-in-types)
4. [Strings & Formatting](#4-strings--formatting)
5. [Collections](#5-collections)
6. [Comprehensions & Iteration](#6-comprehensions--iteration)
7. [Control Flow & Pattern Matching](#7-control-flow--pattern-matching)
8. [Functions](#8-functions)
9. [Decorators](#9-decorators)
10. [Iterators & Generators](#10-iterators--generators)
11. [Classes & OOP](#11-classes--oop)
12. [Dunder / Magic Methods](#12-dunder--magic-methods)
13. [Dataclasses, NamedTuple, Enum](#13-dataclasses-namedtuple-enum)
14. [Descriptors & Metaclasses](#14-descriptors--metaclasses)
15. [Context Managers](#15-context-managers)
16. [Exceptions](#16-exceptions)
17. [Typing](#17-typing)
18. [Modules, Packages & Imports](#18-modules-packages--imports)
19. [Files, Paths & Serialization](#19-files-paths--serialization)
20. [Standard Library Highlights](#20-standard-library-highlights)
21. [Concurrency: threads, processes, asyncio](#21-concurrency-threads-processes-asyncio)
22. [Testing](#22-testing)
23. [Debugging, Profiling & Performance](#23-debugging-profiling--performance)
24. [Packaging & Environments](#24-packaging--environments)
25. [Design Patterns in Python](#25-design-patterns-in-python)
26. [Idioms, Gotchas & Best Practices](#26-idioms-gotchas--best-practices)
27. [One-Liners & Cookbook](#27-one-liners--cookbook)

---

## 1. Running Python & Tooling

```bash
python -c "print('hi')"           # run a snippet
python -m module_name             # run a module as a script
python -m http.server 8000        # quick static server
python -m json.tool file.json     # pretty-print JSON
python -m venv .venv              # create virtualenv
python -m pdb script.py           # debug
python -m timeit "sum(range(100))"
python -i script.py               # drop into REPL after running
python -O script.py               # disable asserts / __debug__
python -X dev script.py           # dev mode: extra warnings & checks
python -W error script.py         # turn warnings into errors
```

Useful env vars: `PYTHONPATH`, `PYTHONDONTWRITEBYTECODE=1`, `PYTHONUNBUFFERED=1`,
`PYTHONHASHSEED=0`, `PYTHONBREAKPOINT=ipdb.set_trace`, `PYTHONWARNINGS=default`.

Modern toolchain: `uv` / `pip` (install), `ruff` (lint+format), `mypy` / `pyright` (types),
`pytest` (tests), `hatch` / `poetry` / `build` (packaging), `pre-commit` (hooks).

## 2. Syntax Basics

```python
x = 1                       # assignment; no declarations
x: int = 1                  # annotated assignment
a = b = 0                   # chained
a, b = b, a                 # tuple swap
first, *rest = [1, 2, 3]    # starred unpacking -> 1, [2, 3]
a, (b, c) = 1, (2, 3)       # nested unpacking
del x                       # unbind name

if (n := len("abc")) > 2:   # walrus operator (3.8+)
    print(n)

# truthiness: falsy = None, False, 0, 0.0, '', (), [], {}, set(), range(0)
value = maybe or "default"          # or/and return operands, not bools
result = a if cond else b           # conditional expression
_ = ignored_value                   # conventional throwaway name
BIG = 1_000_000                     # numeric separators
s = ("implicit "
     "string concatenation")
```

Operators: `+ - * / // % ** @` (matmul), `& | ^ ~ << >>`, `< <= > >= == != is in`,
`not and or`, chained comparisons `0 <= x < 10`.

Statements: `pass`, `break`, `continue`, `return`, `yield`, `raise`, `assert`,
`global`, `nonlocal`, `import`, `with`, `try`, `del`, `lambda`, `async`/`await`.

## 3. Built-in Types

```python
int, float, complex, bool, str, bytes, bytearray, memoryview
list, tuple, range, dict, set, frozenset, NoneType, type
```

```python
int("ff", 16); hex(255); oct(8); bin(5)      # '0xff' '0o10' '0b101'
divmod(7, 2)          # (3, 1)
round(2.675, 2)       # 2.67  (binary float repr)
abs(-3); pow(2, 10); pow(2, 10, 1000)        # modular exponent
float('inf'), float('nan')
5 / 2, 5 // 2, -5 // 2, 5 % 3, -5 % 3        # 2.5, 2, -3, 2, 1
(0.1 + 0.2) == 0.3                            # False -> use math.isclose
from decimal import Decimal; Decimal("0.1") + Decimal("0.2")
from fractions import Fraction; Fraction(1, 3)
int.bit_length(255); (255).bit_count()        # 8, 8 (3.10+)
```

Conversions/inspection: `str()`, `repr()`, `int()`, `float()`, `list()`, `tuple()`,
`set()`, `dict()`, `bool()`, `type(x)`, `isinstance(x, (int, float))`,
`issubclass(A, B)`, `id(x)`, `hash(x)`, `len(x)`, `dir(x)`, `vars(x)`, `callable(x)`.

Immutable: `int float str bytes tuple frozenset range complex bool`.
Mutable: `list dict set bytearray` and most objects.

## 4. Strings & Formatting

```python
s = "Hello, World"
s.lower(); s.upper(); s.title(); s.casefold(); s.swapcase()
s.strip(); s.lstrip("H"); s.rstrip()
s.split(","); s.rsplit(",", 1); s.splitlines(); "-".join(["a", "b"])
s.replace("l", "L", 1); s.count("l"); s.find("World"); s.index("World")
s.startswith(("He", "Hi")); s.endswith("ld")
s.zfill(20); s.center(20, "*"); s.ljust(5); s.rjust(5)
s.removeprefix("Hello, "); s.removesuffix("World")     # 3.9+
s.isdigit(); s.isalpha(); s.isalnum(); s.isspace(); s.isidentifier()
s.encode("utf-8"); b"bytes".decode("utf-8")
s.translate(str.maketrans("lo", "01"))
s.partition(", ")           # ('Hello', ', ', 'World')
"a\tb".expandtabs(4)
```

f-strings:

```python
name, val = "x", 3.14159
f"{name=} {val=:.2f}"            # "name='x' val=3.14"
f"{val:.3f} {val:10.2f} {val:<10} {val:>10} {val:^10} {val:+}"
f"{255:#x} {255:08b} {1234567:,} {0.25:.1%}"
f"{'text':.<10}"                 # 'text......'
f"{obj!r} {obj!s} {obj!a}"       # repr / str / ascii conversion
f"{value:{width}.{prec}f}"       # nested fields
f"{ {'k': 1}['k'] }"             # arbitrary expressions
```

Other formatting: `"%s %d" % ("a", 1)`, `"{} {n}".format(1, n=2)`,
`string.Template("$x").substitute(x=1)`, `textwrap.dedent/fill/shorten`,
`pprint.pprint(obj)`, `reprlib.repr(obj)`.

Regex:

```python
import re
m = re.search(r"(?P<num>\d+)", "abc 42")
m.group("num"), m.span(), m.groupdict()
re.match(r"^a", s); re.fullmatch(r"\w+", s)
re.findall(r"\d+", s); list(re.finditer(r"\d+", s))
re.sub(r"\s+", " ", s); re.subn(...); re.sub(r"(\w)", lambda m: m[1].upper(), s)
re.split(r"[,;]\s*", s)
pat = re.compile(r"\d+", re.I | re.M | re.S | re.X)
re.escape("a.b")
```

## 5. Collections

```python
# list
lst = [3, 1, 2]
lst.append(4); lst.extend([5]); lst.insert(0, 0); lst.pop(); lst.pop(0)
lst.remove(3); lst.index(2); lst.count(1); lst.reverse(); lst.sort(reverse=True)
lst.copy(); lst.clear()
lst[1:3]; lst[::-1]; lst[::2]; lst[1:3] = [9, 9]; del lst[0]
sorted(lst, key=abs); [*lst, 6]; lst * 2

# tuple: immutable, hashable if elements are
t = (1,)                 # trailing comma makes a 1-tuple

# dict (insertion-ordered)
d = {"a": 1}
d.get("b", 0); d.setdefault("b", 0); d.pop("a", None); d.popitem()
d.keys(); d.values(); d.items(); d.update(other); d |= other      # 3.9+
{**d, "c": 3}; d | other                                          # merge
dict.fromkeys("abc", 0)
{v: k for k, v in d.items()}                                      # invert
max(d, key=d.get); sorted(d.items(), key=lambda kv: -kv[1])

# set
s1, s2 = {1, 2}, {2, 3}
s1 | s2; s1 & s2; s1 - s2; s1 ^ s2
s1 <= s2; s1.isdisjoint(s2)
s1.add(4); s1.discard(9); s1.remove(1); s1.update(s2)
frozenset(s1)                     # hashable set
```

`collections`:

```python
from collections import (defaultdict, Counter, deque, OrderedDict,
                         ChainMap, namedtuple, UserDict, UserList)
defaultdict(list)["k"].append(1)
c = Counter("mississippi"); c.most_common(2); c["s"]; c + Counter("si"); c.total()
dq = deque([1, 2], maxlen=3); dq.appendleft(0); dq.rotate(1); dq.popleft()
ChainMap(overrides, defaults)["key"]          # layered lookup
Point = namedtuple("Point", "x y", defaults=(0, 0)); Point(1)._replace(y=2)._asdict()
```

`heapq` / `bisect` / `array`:

```python
import heapq, bisect
heapq.heappush(h, (prio, item)); heapq.heappop(h); heapq.heapify(lst)
heapq.nlargest(3, data, key=len); heapq.nsmallest(3, data); heapq.merge(a, b)
bisect.bisect_left(sorted_lst, x); bisect.insort(sorted_lst, x)
from array import array; array("i", [1, 2, 3])       # compact numeric array
```

## 6. Comprehensions & Iteration

```python
[x * x for x in range(10) if x % 2]
{k: v for k, v in pairs}
{c for c in "hello"}
(x for x in big)                       # generator expression (lazy)
[y for row in matrix for y in row]     # flatten (left-to-right = nesting order)
[[r[i] for r in matrix] for i in range(len(matrix[0]))]     # transpose
[b if cond else a for x in xs]
[y for x in xs if (y := f(x)) is not None]      # walrus filter+map
```

Built-in iteration helpers:

```python
enumerate(xs, start=1)
zip(a, b); zip(a, b, strict=True)          # 3.10+ length check
reversed(xs); sorted(xs, key=..., reverse=True)
map(f, xs); filter(None, xs)               # filter(None) drops falsy
any(xs); all(xs); sum(xs, start=0); min/max(xs, key=..., default=None)
range(start, stop, step); len(xs); iter(xs); next(it, default)
iter(callable, sentinel)                   # call until sentinel returned
```

`itertools`:

```python
from itertools import (count, cycle, repeat, chain, islice, tee, zip_longest,
    accumulate, groupby, product, permutations, combinations,
    combinations_with_replacement, compress, dropwhile, takewhile,
    filterfalse, starmap, pairwise, batched)
chain.from_iterable(list_of_lists)
islice(gen, 10); islice(gen, 2, 10, 2)
accumulate([1, 2, 3], operator.mul)         # 1, 2, 6
groupby(sorted(rows, key=key), key=key)     # MUST sort first
product("ab", repeat=2); permutations(xs, 2); combinations(xs, 2)
pairwise([1, 2, 3])                         # (1,2), (2,3)   3.10+
batched(range(7), 3)                        # (0,1,2), (3,4,5), (6,)  3.12+
zip_longest(a, b, fillvalue=None)
```

`functools`:

```python
from functools import (reduce, partial, wraps, lru_cache, cache, cached_property,
                       singledispatch, total_ordering, cmp_to_key, partialmethod)
reduce(operator.add, xs, 0)
partial(int, base=2)("1010")
@cache / @lru_cache(maxsize=128)     # memoize; f.cache_clear(), f.cache_info()
```

`operator`: `itemgetter(1)`, `attrgetter("a.b")`, `methodcaller("upper")`,
`add`, `mul`, `lt`, `not_`, `truth`, `iadd`.

## 7. Control Flow & Pattern Matching

```python
if a: ...
elif b: ...
else: ...

for i in range(3):
    ...
else:                 # runs iff loop was not broken
    ...

while cond:
    ...
else:
    ...

try: ...
except (KeyError, IndexError) as e: ...
except* TypeError as eg: ...          # 3.11 exception groups
else: ...                             # no exception raised
finally: ...
```

`match` (structural pattern matching, 3.10+):

```python
match command.split():
    case ["go", ("north" | "south") as direction]:      # or-pattern + capture
        move(direction)
    case ["take", *items] if items:                     # star + guard
        take(items)
    case {"type": "user", "id": int(uid), **rest}:       # mapping pattern
        load(uid)
    case Point(x=0, y=0):                                # class pattern
        origin()
    case [Point() as p, *_]:
        first(p)
    case str() | bytes():
        text()
    case _:
        unknown()
```

Class patterns use `__match_args__` (dataclasses/NamedTuple get it free) for
positional matching: `case Point(0, y)`.

## 8. Functions

```python
def f(pos, /, normal, *args, kw_only, default=1, **kwargs) -> int:
    """Docstring."""
    return 0
```

- `/` marks positional-only params; `*` (or `*args`) marks keyword-only after it.
- Defaults evaluate **once** at definition time — never use mutable defaults.

```python
def bad(acc=[]): ...              # BUG: shared across calls
def good(acc=None):
    acc = [] if acc is None else acc
```

```python
f(*args, **kwargs)                # unpack at call site
lambda x, y=1: x + y              # expression-only anonymous function
def outer():
    n = 0
    def inner():
        nonlocal n; n += 1        # closure write
    return inner
global NAME                       # rebind module-level name
f.__name__, f.__doc__, f.__defaults__, f.__annotations__, f.__closure__
import inspect; inspect.signature(f); inspect.getsource(f)
```

## 9. Decorators

```python
from functools import wraps

def logged(func):
    @wraps(func)                       # preserve name/doc/annotations
    def wrapper(*args, **kwargs):
        print("call", func.__name__)
        return func(*args, **kwargs)
    return wrapper

def retry(times=3, exc=Exception):     # decorator factory (takes args)
    def deco(func):
        @wraps(func)
        def wrapper(*a, **kw):
            for attempt in range(times):
                try:
                    return func(*a, **kw)
                except exc:
                    if attempt == times - 1:
                        raise
        return wrapper
    return deco

class CountCalls:                       # class-based decorator
    def __init__(self, func):
        self.func, self.n = func, 0
        wraps(func)(self)
    def __call__(self, *a, **kw):
        self.n += 1
        return self.func(*a, **kw)

@logged
@retry(times=5, exc=TimeoutError)       # applied bottom-up
def fetch(url): ...
```

Built-in decorators: `@property`, `@x.setter`, `@x.deleter`, `@staticmethod`,
`@classmethod`, `@functools.cache`, `@cached_property`, `@singledispatch`,
`@total_ordering`, `@dataclass`, `@contextlib.contextmanager`,
`@typing.overload`, `@typing.final`, `@abc.abstractmethod`,
`@pytest.fixture`, `@pytest.mark.parametrize`.

```python
@singledispatch
def render(obj): return str(obj)
@render.register
def _(obj: list): return ", ".join(map(render, obj))
```

## 10. Iterators & Generators

```python
class Countdown:
    def __init__(self, n): self.n = n
    def __iter__(self): return self
    def __next__(self):
        if self.n <= 0: raise StopIteration
        self.n -= 1
        return self.n + 1

def gen():
    x = yield 1            # yields 1, receives value from .send()
    yield from range(3)    # delegate to sub-iterable
    return "done"          # becomes StopIteration.value

g = gen(); next(g); g.send(10); g.throw(ValueError); g.close()

async def agen():
    yield 1                # async generator
async for v in agen(): ...
```

Pipelines are cheap and memory-flat:

```python
lines = (l.rstrip("\n") for l in open("big.log"))
errors = (l for l in lines if "ERROR" in l)
count = sum(1 for _ in errors)
```

## 11. Classes & OOP

```python
class Base:
    class_attr = 0                      # shared
    __slots__ = ("x",)                  # no __dict__, less memory (careful w/ inheritance)

    def __init__(self, x): self.x = x
    def method(self): return self.x
    @classmethod
    def create(cls, *a): return cls(*a)
    @staticmethod
    def helper(): return 42
    @property
    def double(self): return self.x * 2
    @double.setter
    def double(self, v): self.x = v / 2
    def _internal(self): ...            # convention: non-public
    def __mangled(self): ...            # name-mangled to _Base__mangled

class Child(Base):
    def __init__(self, x, y):
        super().__init__(x)
        self.y = y
    def method(self):
        return super().method() + self.y

class Mixin: ...
class Multi(Mixin, Child): ...           # MRO: Multi.__mro__ (C3 linearization)
```

Abstract bases & protocols:

```python
from abc import ABC, abstractmethod
class Repo(ABC):
    @abstractmethod
    def get(self, id: int) -> object: ...

from typing import Protocol, runtime_checkable
@runtime_checkable
class Closeable(Protocol):
    def close(self) -> None: ...        # structural (duck) typing
```

Object internals: `__dict__`, `__class__`, `__mro__`, `type(name, bases, ns)`,
`getattr/setattr/hasattr/delattr`, `object.__new__`, `copy.copy`, `copy.deepcopy`,
`__init_subclass__(cls, **kw)`, `__set_name__(owner, name)`.

## 12. Dunder / Magic Methods

| Group | Methods |
|---|---|
| Construction | `__new__`, `__init__`, `__del__`, `__init_subclass__` |
| Representation | `__repr__`, `__str__`, `__format__`, `__bytes__` |
| Comparison/hash | `__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`, `__hash__` |
| Container | `__len__`, `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`, `__iter__`, `__reversed__`, `__next__`, `__missing__` |
| Attributes | `__getattr__`, `__getattribute__`, `__setattr__`, `__delattr__`, `__dir__`, `__get__`, `__set__`, `__delete__` |
| Callable/context | `__call__`, `__enter__`, `__exit__`, `__aenter__`, `__aexit__` |
| Numeric | `__add__`, `__radd__`, `__iadd__`, `__sub__`, `__mul__`, `__matmul__`, `__truediv__`, `__floordiv__`, `__mod__`, `__pow__`, `__neg__`, `__abs__`, `__round__`, `__index__`, `__int__`, `__float__`, `__bool__` |
| Async | `__await__`, `__aiter__`, `__anext__` |
| Copy/pickle | `__copy__`, `__deepcopy__`, `__reduce__`, `__getstate__`, `__setstate__` |
| Misc | `__match_args__`, `__slots__`, `__class_getitem__`, `__set_name__` |

```python
class Vec:
    def __init__(self, x, y): self.x, self.y = x, y
    def __repr__(self): return f"Vec({self.x}, {self.y})"
    def __eq__(self, o): return (self.x, self.y) == (o.x, o.y)
    def __hash__(self): return hash((self.x, self.y))
    def __add__(self, o): return Vec(self.x + o.x, self.y + o.y)
    def __iter__(self): yield from (self.x, self.y)
    def __abs__(self): return (self.x**2 + self.y**2) ** 0.5
```

Defining `__eq__` without `__hash__` makes instances unhashable.

## 13. Dataclasses, NamedTuple, Enum

```python
from dataclasses import dataclass, field, asdict, astuple, replace, fields, InitVar

@dataclass(frozen=True, slots=True, kw_only=True, order=True)
class User:
    id: int
    name: str = "anon"
    tags: list[str] = field(default_factory=list)
    secret: str = field(default="", repr=False, compare=False)
    meta: dict = field(default_factory=dict, metadata={"doc": "extra"})

    def __post_init__(self): object.__setattr__(self, "name", self.name.strip())

asdict(u); astuple(u); replace(u, name="new"); fields(User)
```

```python
from typing import NamedTuple, TypedDict, Required, NotRequired
class Point(NamedTuple):
    x: int
    y: int = 0

class Config(TypedDict, total=False):
    host: Required[str]
    port: NotRequired[int]
```

```python
from enum import Enum, StrEnum, IntEnum, IntFlag, auto, unique
@unique
class Color(Enum):
    RED = auto()
    GREEN = auto()
    @property
    def css(self): return self.name.lower()
Color.RED.name, Color.RED.value, Color("RED"), Color["RED"], list(Color)

class Perm(IntFlag):
    READ = 1; WRITE = 2; ALL = READ | WRITE
```

## 14. Descriptors & Metaclasses

```python
class Positive:                      # data descriptor
    def __set_name__(self, owner, name): self.attr = f"_{name}"
    def __get__(self, obj, owner=None):
        return self if obj is None else getattr(obj, self.attr)
    def __set__(self, obj, value):
        if value <= 0: raise ValueError("must be > 0")
        setattr(obj, self.attr, value)

class Order:
    qty = Positive()
```

```python
class Meta(type):
    def __new__(mcls, name, bases, ns, **kw):
        ns["registry_key"] = name.lower()
        return super().__new__(mcls, name, bases, ns)

class Model(metaclass=Meta): ...

# Prefer __init_subclass__ when you only need subclass hooks:
class Plugin:
    registry: dict[str, type] = {}
    def __init_subclass__(cls, key, **kw):
        super().__init_subclass__(**kw)
        Plugin.registry[key] = cls

class Csv(Plugin, key="csv"): ...
```

## 15. Context Managers

```python
with open("a") as f, open("b", "w") as g:      # multiple
    g.write(f.read())

with (open("a") as f, open("b") as g):          # parenthesized, 3.10+
    ...

class Timer:
    def __enter__(self):
        self.t = time.perf_counter(); return self
    def __exit__(self, exc_type, exc, tb):
        self.elapsed = time.perf_counter() - self.t
        return False                            # True swallows the exception

from contextlib import (contextmanager, asynccontextmanager, suppress,
    closing, redirect_stdout, redirect_stderr, nullcontext, ExitStack, chdir)

@contextmanager
def transaction(conn):
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback(); raise

with suppress(FileNotFoundError):
    os.remove(path)

with ExitStack() as stack:                      # dynamic number of CMs
    files = [stack.enter_context(open(p)) for p in paths]
```

## 16. Exceptions

```python
raise ValueError("bad")                  # raise
raise ValueError("bad") from err         # explicit chaining (__cause__)
raise ValueError("bad") from None        # suppress context
try: ...
except ValueError as e:
    logging.exception("failed")          # logs traceback
    raise                                # re-raise preserving traceback
```

Hierarchy essentials: `BaseException` > `Exception` > (`ArithmeticError`
[`ZeroDivisionError`, `OverflowError`], `LookupError` [`KeyError`, `IndexError`],
`OSError` [`FileNotFoundError`, `PermissionError`, `TimeoutError`,
`ConnectionError`], `ValueError` [`UnicodeError`], `TypeError`,
`AttributeError`, `RuntimeError` [`RecursionError`, `NotImplementedError`],
`StopIteration`, `ImportError` [`ModuleNotFoundError`], `AssertionError`).
Outside `Exception`: `KeyboardInterrupt`, `SystemExit`, `GeneratorExit` — do not
swallow them with bare `except:`.

```python
class AppError(Exception):
    def __init__(self, msg, *, code=500):
        super().__init__(msg); self.code = code

e.add_note("extra context")                       # 3.11+
raise ExceptionGroup("many", [ValueError(), KeyError()])   # 3.11+
try: ...
except* ValueError as eg: ...                     # handle subset of group
import traceback; traceback.format_exc(); traceback.print_exc()
```

EAFP (try/except) is idiomatic; LBYL (check first) risks races.

## 17. Typing

```python
from typing import (Any, Optional, Union, Literal, Final, ClassVar, TypeVar,
    Generic, Protocol, Callable, Iterable, Iterator, Sequence, Mapping,
    TypeAlias, TypedDict, NamedTuple, overload, cast, NoReturn, Never,
    Self, ParamSpec, Concatenate, Annotated, TypeGuard, assert_never)

def f(x: int, y: str | None = None) -> list[dict[str, int]]: ...
Handler: TypeAlias = Callable[[str, int], bool]
type Vector = list[float]                       # 3.12 type alias statement

T = TypeVar("T", bound="Comparable")
def first(xs: Sequence[T]) -> T: return xs[0]

class Box[T]:                                    # 3.12 generics syntax
    def __init__(self, item: T) -> None: self.item = item
    def get(self) -> T: return self.item

P = ParamSpec("P"); R = TypeVar("R")
def deco(f: Callable[P, R]) -> Callable[P, R]: ...

Mode = Literal["r", "w"]
Age = Annotated[int, "years"]
MAX: Final = 10
count: ClassVar[int] = 0

def is_str_list(v: list[object]) -> TypeGuard[list[str]]:
    return all(isinstance(x, str) for x in v)

@overload
def get(k: str) -> str: ...
@overload
def get(k: int) -> int: ...
def get(k): return k

from __future__ import annotations       # lazy annotations (avoids fwd-ref quotes)
```

Rules of thumb: annotate parameters with abstract types (`Iterable`, `Mapping`),
return concrete types; avoid `Any`; run `mypy --strict` / `pyright` in CI.

## 18. Modules, Packages & Imports

```python
import os
import os.path as p
from os import path, sep
from . import sibling            # relative (packages only)
from .. pkg import thing
import importlib; importlib.reload(mod); importlib.import_module("pkg.mod")

if __name__ == "__main__":
    main()

__all__ = ["public_name"]        # controls `from mod import *`
```

Layout:

```
project/
  pyproject.toml
  src/mypkg/__init__.py
  src/mypkg/__main__.py          # python -m mypkg
  tests/test_x.py
```

Import mechanics: `sys.path`, `sys.modules` (cache), `__pycache__`,
namespace packages (no `__init__.py`), circular imports → move import into the
function or restructure. Avoid `from x import *` in library code.

## 19. Files, Paths & Serialization

```python
from pathlib import Path
p = Path("~/data/f.csv").expanduser().resolve()
p.parent, p.name, p.stem, p.suffix, p.suffixes, p.parts
p.exists(), p.is_file(), p.is_dir(), p.stat().st_size
p.read_text(encoding="utf-8"), p.write_text(s), p.read_bytes(), p.write_bytes(b)
p.with_suffix(".json"), p.with_name("g.csv"), p / "sub" / "file"
Path("dir").mkdir(parents=True, exist_ok=True); p.unlink(missing_ok=True)
list(Path(".").glob("**/*.py")); Path(".").rglob("*.txt"); p.iterdir()
p.rename(target); p.relative_to(base); p.samefile(q)
```

```python
with open(path, "r", encoding="utf-8", newline="") as f:
    for line in f: ...
# modes: r w a x, + (update), b (binary); use encoding= always for text
open(path, "w").write(...)              # BAD: unclosed handle
```

```python
import json, csv, pickle, sqlite3, tomllib, shelve, io
json.dumps(obj, indent=2, sort_keys=True, default=str, ensure_ascii=False)
json.loads(s); json.dump(obj, f); json.load(f)
csv.DictReader(f); csv.DictWriter(f, fieldnames=[...]).writeheader()
pickle.dumps(obj)                        # NEVER unpickle untrusted data
tomllib.load(open("pyproject.toml", "rb"))   # 3.11+ read-only TOML
conn = sqlite3.connect(":memory:"); conn.execute("SELECT ?", (1,)).fetchall()
io.StringIO(); io.BytesIO()
import gzip, zipfile, tarfile, shutil, tempfile, os
shutil.copy2, shutil.rmtree, shutil.make_archive, shutil.which
tempfile.TemporaryDirectory(), tempfile.NamedTemporaryFile()
os.environ.get("KEY", "default"); os.getenv; os.walk("."); os.cpu_count()
```

## 20. Standard Library Highlights

```python
import datetime as dt
dt.datetime.now(dt.UTC); dt.datetime.now(dt.timezone.utc)     # aware, preferred
dt.datetime.fromisoformat("2024-01-01T00:00:00+00:00"); d.isoformat()
d.strftime("%Y-%m-%d %H:%M:%S"); dt.datetime.strptime(s, "%Y-%m-%d")
dt.timedelta(days=1, hours=2); (a - b).total_seconds()
from zoneinfo import ZoneInfo; d.astimezone(ZoneInfo("Asia/Kolkata"))
import time; time.time(); time.perf_counter(); time.monotonic(); time.sleep(0.1)
```

```python
import logging
logging.basicConfig(level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s %(message)s")
log = logging.getLogger(__name__)
log.debug("v=%s", v)          # lazy %-args, not f-strings
log.exception("failed")       # inside except: includes traceback
```

```python
import argparse
ap = argparse.ArgumentParser(description="tool")
ap.add_argument("src"); ap.add_argument("-n", "--num", type=int, default=1)
ap.add_argument("-v", "--verbose", action="store_true")
ap.add_argument("--mode", choices=["a", "b"]); ap.add_argument("files", nargs="*")
args = ap.parse_args()
```

```python
import subprocess
subprocess.run(["ls", "-l"], check=True, capture_output=True, text=True,
               timeout=10, cwd="/tmp", env={**os.environ, "X": "1"})
```

Others worth knowing: `os`, `sys`, `shutil`, `glob`, `math`, `statistics`,
`random` (`random.Random(0)`, `choice`, `sample`, `shuffle`, `randint`),
`secrets` (tokens/passwords), `hashlib`, `hmac`, `base64`, `uuid`, `zlib`,
`urllib.request`/`urllib.parse`, `http.client`, `socket`, `ssl`, `email`,
`sched`, `signal`, `atexit`, `warnings`, `weakref`, `copy`, `pprint`, `enum`,
`abc`, `numbers`, `typing`, `dataclasses`, `contextvars`, `queue`, `struct`,
`difflib`, `unicodedata`, `locale`, `gettext`, `platform`, `getpass`,
`configparser`, `graphlib.TopologicalSorter`, `ast`, `inspect`, `dis`, `gc`,
`tracemalloc`, `sysconfig`, `zoneinfo`.

## 21. Concurrency: threads, processes, asyncio

Choose: CPU-bound → processes; blocking I/O → threads; many sockets/high
concurrency → asyncio. The GIL serializes CPython bytecode across threads.

```python
import threading
lock = threading.Lock(); rlock = threading.RLock()
with lock: shared += 1
ev = threading.Event(); ev.set(); ev.wait(timeout=1)
sem = threading.Semaphore(5); cond = threading.Condition()
local = threading.local()
t = threading.Thread(target=work, args=(1,), daemon=True); t.start(); t.join()
import queue; q = queue.Queue(maxsize=10); q.put(x); q.get(); q.task_done(); q.join()
```

```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor, as_completed
with ThreadPoolExecutor(max_workers=8) as ex:
    futs = {ex.submit(fetch, u): u for u in urls}
    for fut in as_completed(futs):
        try: data = fut.result(timeout=30)
        except Exception as e: log.warning("%s failed: %s", futs[fut], e)
with ProcessPoolExecutor() as ex:
    list(ex.map(cpu_heavy, items, chunksize=10))
```

```python
import multiprocessing as mp
mp.set_start_method("spawn")           # safest cross-platform
with mp.Pool(4) as pool: pool.map(f, xs)
mp.Queue(), mp.Value("i", 0), mp.Manager().dict(), mp.shared_memory
```

asyncio:

```python
import asyncio

async def fetch(session, url):
    async with session.get(url) as r:
        return await r.text()

async def main():
    async with asyncio.TaskGroup() as tg:              # 3.11+, cancels siblings on error
        t1 = tg.create_task(fetch(s, u1))
        t2 = tg.create_task(fetch(s, u2))
    results = await asyncio.gather(a(), b(), return_exceptions=True)
    done, pending = await asyncio.wait(tasks, timeout=5)
    async with asyncio.timeout(5): await slow()        # 3.11+
    await asyncio.wait_for(slow(), timeout=5)
    await asyncio.sleep(0)                             # yield control
    val = await asyncio.to_thread(blocking_io, arg)    # offload blocking call
    async with asyncio.Semaphore(10): ...
    q = asyncio.Queue(); await q.put(1); await q.get()
    lock = asyncio.Lock(); ev = asyncio.Event()

asyncio.run(main(), debug=True)
```

Rules: never call blocking code in a coroutine (use `to_thread`/executor); every
`await` is a possible cancellation point; always await/cancel tasks you create;
`asyncio.create_task` results must be retained or exceptions vanish.

## 22. Testing

```python
# tests/test_math.py
import pytest
from mypkg import add

@pytest.fixture
def db(tmp_path):
    conn = connect(tmp_path / "t.db")
    yield conn                  # teardown after yield
    conn.close()

@pytest.mark.parametrize("a,b,want", [(1, 2, 3), (-1, 1, 0)])
def test_add(a, b, want):
    assert add(a, b) == want

def test_raises():
    with pytest.raises(ValueError, match="bad"):
        add("x", 1)

def test_approx():
    assert 0.1 + 0.2 == pytest.approx(0.3)

@pytest.mark.skipif(sys.platform == "win32", reason="posix only")
@pytest.mark.xfail(strict=True)
def test_todo(): ...

def test_mock(monkeypatch):
    monkeypatch.setenv("K", "v")
    monkeypatch.setattr("mypkg.now", lambda: 0)
```

```python
from unittest.mock import Mock, MagicMock, patch, call, ANY, AsyncMock
with patch("mypkg.client.get", return_value=Mock(status_code=200)) as m:
    ...
m.assert_called_once_with("url", timeout=ANY); m.call_args_list
mock.side_effect = [1, 2, ValueError("boom")]
```

```bash
pytest -x -q --lf --ff -k "add and not slow" -m "not slow" \
       --cov=mypkg --cov-report=term-missing -n auto -p no:randomly
python -m unittest discover -s tests
python -m doctest -v module.py
```

Builtin fixtures: `tmp_path`, `tmp_path_factory`, `capsys`, `capfd`, `caplog`,
`monkeypatch`, `request`, `recwarn`. Shared fixtures go in `conftest.py`.
`hypothesis` for property-based testing; `pytest-asyncio` for async tests.

## 23. Debugging, Profiling & Performance

```python
breakpoint()                        # honors PYTHONBREAKPOINT
# pdb: n(ext) s(tep) c(ont) l(ist) ll p pp w(here) u/d b <line> tbreak
#      a(rgs) r(eturn) q(uit) display <expr> interact
import pdb; pdb.post_mortem()
import faulthandler; faulthandler.enable()      # dump on segfault
import traceback, warnings, logging, sys
sys.settrace / sys.setrecursionlimit(10_000)
```

```bash
python -m cProfile -s cumtime script.py
python -m pstats profile.out
python -m timeit -n 1000 -s "setup" "stmt"
python -m tracemalloc            # or tracemalloc.start(); take_snapshot()
py-spy top --pid <PID>           # sampling profiler (external)
```

Performance checklist: measure before optimizing; prefer builtins/comprehensions
over Python-level loops; hoist attribute lookups out of loops; `join()` strings
instead of `+=`; use `set`/`dict` for membership; `deque` for queue ops;
`slots`/`array`/`numpy` for memory; `functools.cache` for pure functions;
generators to avoid materializing lists; batch I/O and DB calls; move hot loops
to C extensions (`numpy`, `Cython`, `numba`, Rust/PyO3); consider
`multiprocessing` for CPU-bound work; look at big-O first.

Complexity: list index/append O(1), insert/remove/`in` O(n); dict/set get/add/`in`
O(1) average; sort O(n log n); `deque` append/pop both ends O(1);
`heapq` push/pop O(log n); string concat in a loop O(n²).

## 24. Packaging & Environments

```bash
python -m venv .venv && source .venv/bin/activate    # Windows: .venv\Scripts\activate
pip install -e ".[dev]"        # editable + extras
pip install -r requirements.txt
pip freeze > requirements.txt
pip list --outdated; pip show pkg; pip check
uv venv && uv pip install -e ".[dev]"; uv run pytest; uv sync; uv lock
python -m build            # sdist + wheel into dist/
twine upload dist/*
pipx install ruff          # install CLI tools isolated
```

```toml
# pyproject.toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "mypkg"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = ["httpx>=0.27"]

[project.optional-dependencies]
dev = ["pytest", "ruff", "mypy"]

[project.scripts]
mycli = "mypkg.cli:main"

[tool.ruff]
line-length = 100
[tool.ruff.lint]
select = ["E", "F", "I", "UP", "B", "SIM", "RUF"]

[tool.mypy]
strict = true

[tool.pytest.ini_options]
addopts = "-q --strict-markers"
testpaths = ["tests"]
```

## 25. Design Patterns in Python

Python's first-class functions, duck typing and modules make many GoF patterns
lighter — often a function, a dict, or a module-level object is the whole pattern.

### Creational

```python
# Singleton -> just use a module-level object, or:
@functools.cache
def get_settings() -> Settings: return Settings()

# Factory / registry (replaces Factory Method + Abstract Factory)
PARSERS: dict[str, Callable[[str], Doc]] = {"json": parse_json, "csv": parse_csv}
def parse(kind: str, text: str) -> Doc: return PARSERS[kind](text)

# Builder — for many optional params; dataclass + replace() often suffices
@dataclass(frozen=True)
class Query:
    table: str; where: tuple[str, ...] = (); limit: int | None = None
    def filter(self, cond): return replace(self, where=self.where + (cond,))
    def top(self, n): return replace(self, limit=n)
Query("users").filter("age > 18").top(10)          # fluent, immutable

# Prototype -> copy.deepcopy(obj)
```

### Structural

```python
# Adapter: wrap a foreign interface
class ListAsFile:
    def __init__(self, lines): self._lines = lines
    def read(self): return "".join(self._lines)

# Decorator (object form) via composition + __getattr__ delegation
class CachingRepo:
    def __init__(self, inner): self._inner, self._cache = inner, {}
    def get(self, k):
        if k not in self._cache: self._cache[k] = self._inner.get(k)
        return self._cache[k]
    def __getattr__(self, name): return getattr(self._inner, name)

# Facade: one simple function/class over a subsystem
def send_report(rows): render(rows) |> email(...)      # conceptual

# Proxy: lazy/remote/access control — same interface, deferred work
class LazyImage:
    def __init__(self, path): self.path, self._img = path, None
    @property
    def img(self):
        if self._img is None: self._img = load(self.path)
        return self._img

# Composite: uniform tree
class Node:
    def __init__(self, *children): self.children = children
    def size(self): return 1 + sum(c.size() for c in self.children)

# Flyweight: sys.intern(), frozen dataclass + cache, __slots__
# Bridge: inject the implementation object (see DI below)
```

### Behavioral

```python
# Strategy -> pass a function
def sort_by(rows, key: Callable[[Row], Any]): return sorted(rows, key=key)

# Template Method -> ABC with hooks (or a function taking callbacks)
class Job(ABC):
    def run(self):
        self.setup(); self.work(); self.teardown()
    def setup(self): ...
    @abstractmethod
    def work(self): ...
    def teardown(self): ...

# Observer / pub-sub
class Event:
    def __init__(self): self._subs: list[Callable] = []
    def subscribe(self, fn): self._subs.append(fn); return fn
    def emit(self, *a, **kw):
        for fn in list(self._subs): fn(*a, **kw)
on_save = Event()
@on_save.subscribe
def audit(user): log.info("saved %s", user)

# Command -> callables / functools.partial; undo via a stack of inverse ops
# Chain of Responsibility
def chain(*handlers):
    def run(req):
        for h in handlers:
            if (res := h(req)) is not None: return res
        return None
    return run

# State -> dict of transitions, or Enum + match
# Iterator -> __iter__/generators (built into the language)
# Visitor -> functools.singledispatch on node type
@singledispatch
def evaluate(node): raise TypeError(node)
@evaluate.register
def _(node: Num): return node.value
@evaluate.register
def _(node: Add): return evaluate(node.l) + evaluate(node.r)

# Memento -> copy/deepcopy or dataclasses.replace snapshots
# Mediator -> a coordinator object owning the interactions
# Interpreter -> ast module / recursive evaluate()
# Null Object -> a do-nothing instance instead of None checks
```

### Architectural & Pythonic patterns

```python
# Dependency injection = pass collaborators in __init__ (no framework needed)
class Service:
    def __init__(self, repo: Repo, clock: Callable[[], float] = time.time): ...

# Repository + Unit of Work: isolate persistence behind an interface
# Ports & Adapters (hexagonal): Protocols at the boundary, adapters per backend
# Plugin registry: entry_points in pyproject.toml, or __init_subclass__
# Mixins for cross-cutting behavior; prefer composition over deep hierarchies
# Result/either style: return value | None, or raise domain-specific exceptions
# Borg / shared state, MRO cooperation via super(), context-var scoping:
from contextvars import ContextVar
request_id: ContextVar[str] = ContextVar("request_id", default="-")
```

SOLID in Python terms: small focused modules (S), extend via new
functions/classes and registries rather than editing dispatch chains (O), honour
the informal protocol of the base type (L), define narrow `Protocol`s (I),
depend on `Protocol`/ABC parameters instead of concrete imports (D).

## 26. Idioms, Gotchas & Best Practices

```python
if x is None: ...                   # identity for None/True/False singletons
for i, v in enumerate(xs): ...      # not range(len(xs))
for a, b in zip(xs, ys): ...
"".join(parts)                      # not += in a loop
with open(...) as f: ...            # always close
try/except  (EAFP)                  # not hasattr-chains
d.get(k, default) / d.setdefault    # not `if k in d`
sorted(xs, key=itemgetter(1))
x, y = y, x
if seq:                             # not len(seq) > 0
{**a, **b}; a | b
print(*items, sep="\n", end="", file=sys.stderr, flush=True)
```

Common traps:

- Mutable default arguments (`def f(x=[])`).
- Late-binding closures: `[lambda: i for i in range(3)]` all return 2 — use
  `lambda i=i: i` or `functools.partial`.
- `is` vs `==` for numbers/strings (interning is an implementation detail).
- Mutating a list while iterating it — iterate over a copy or build a new list.
- Shallow `copy()` for nested structures — use `copy.deepcopy`.
- Class attributes shared across instances (mutable class-level list/dict).
- `except Exception` swallowing errors, or bare `except:` catching `KeyboardInterrupt`.
- Float equality; use `math.isclose` or `Decimal` for money.
- Integer division/modulo signs with negatives.
- Naive vs timezone-aware datetimes; always store UTC.
- `sys.setrecursionlimit` instead of an iterative algorithm.
- Circular imports; shadowing stdlib module names (`random.py`, `json.py`).
- Chained `+` list concat in loops (quadratic).
- Threads for CPU-bound work (GIL) — use processes.
- Unretained `asyncio.create_task` results being garbage collected.
- `pickle`/`eval`/`exec` on untrusted input; `subprocess(..., shell=True)` with
  user input; hardcoded secrets; `assert` for validation (stripped under `-O`).

Style: PEP 8 (4 spaces, `snake_case` funcs/vars, `PascalCase` classes,
`UPPER_SNAKE` constants, ~88–100 col lines), PEP 257 docstrings, PEP 20
(`import this`), type hints on public APIs, small pure functions, explicit over
implicit, one obvious way.

## 27. One-Liners & Cookbook

```python
# flatten one level
flat = [x for sub in nested for x in sub]
# unique preserving order
list(dict.fromkeys(items))
# group by key
groups = defaultdict(list)
for r in rows: groups[r["k"]].append(r)
# chunk an iterable
chunks = [xs[i:i+n] for i in range(0, len(xs), n)]      # or itertools.batched
# frequency count / top-n
Counter(words).most_common(10)
# invert a dict
{v: k for k, v in d.items()}
# merge dicts
{**a, **b}
# sort dict by value
dict(sorted(d.items(), key=lambda kv: kv[1], reverse=True))
# transpose
list(zip(*matrix))
# read JSON lines
records = [json.loads(l) for l in Path(p).read_text().splitlines() if l]
# retry with backoff
for attempt in range(5):
    try: return call()
    except TransientError: time.sleep(2 ** attempt)
# temp cwd
with contextlib.chdir(tmp): ...            # 3.11+
# memoized recursion
@cache
def fib(n): return n if n < 2 else fib(n-1) + fib(n-2)
# run shell command and capture
out = subprocess.run(cmd, check=True, capture_output=True, text=True).stdout
# random secure token
secrets.token_urlsafe(32)
# stable hash of a file
hashlib.sha256(Path(p).read_bytes()).hexdigest()
# swap keys/values safely with duplicates
inv = defaultdict(list)
for k, v in d.items(): inv[v].append(k)
# timing block
t0 = time.perf_counter(); work(); print(time.perf_counter() - t0)
# CLI entry
if __name__ == "__main__": raise SystemExit(main())
```

---

### Further reading

- Official docs & tutorial: https://docs.python.org/3/
- Standard library index: https://docs.python.org/3/library/
- PEP index (8, 20, 257, 484, 604, 634, 695): https://peps.python.org/
- `import this`, `python -m dis`, `help(obj)`, `inspect.getsource(obj)`
