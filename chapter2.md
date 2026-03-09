# Chapter 2 — An Array of Sequences
### Sections covered: Overview of Built-In Sequences → Generator Expressions

> **Core idea:** Python's sequence types share a common interface. Understanding how they're categorized — and using listcomps and genexps correctly — is fundamental to writing idiomatic Python.

---

## Overview of Built-In Sequences

Python sequences can be grouped along **two axes**:

### By what they store

| Type | Description | Examples |
|------|-------------|---------|
| **Container sequences** | Hold references to other objects; can store mixed types | `list`, `tuple`, `collections.deque` |
| **Flat sequences** | Physically store values in their own memory; limited to atomic types | `str`, `bytes`, `array.array` |

Flat sequences are more compact and faster, but can only hold primitives (numbers, characters, bytes). Container sequences are more flexible but can surprise you when they hold mutable objects.

### By mutability

| Type | Examples |
|------|---------|
| **Mutable** | `list`, `bytearray`, `array.array`, `collections.deque` |
| **Immutable** | `tuple`, `str`, `bytes` |

> ⚠️ "Immutable" doesn't mean deeply immutable. A `tuple` containing a `list` can still have its contents changed — the tuple can't be reassigned, but the inner list can be mutated.

---

## List Comprehensions (Listcomps)

List comprehensions are the idiomatic way to build a new list by filtering or transforming an iterable.

### Basic syntax

```python
# Instead of this:
# Chapter 2 — An Array of Sequences
### Sections covered: Overview of Built-In Sequences → Generator Expressions

> **Core idea:** Python's sequence types share a common interface. Understanding how they're categorized — and using listcomps and genexps correctly — is fundamental to writing idiomatic Python.

---

## Overview of Built-In Sequences

Python sequences can be grouped along **two axes**:

### By what they store

| Type | Description | Examples |
|------|-------------|---------|
| **Container sequences** | Hold references to other objects; can store mixed types | `list`, `tuple`, `collections.deque` |
| **Flat sequences** | Physically store values in their own memory; limited to atomic types | `str`, `bytes`, `array.array` |

Flat sequences are more compact and faster, but can only hold primitives (numbers, characters, bytes). Container sequences are more flexible but can surprise you when they hold mutable objects.

### By mutability

| Type | Examples |
|------|---------|
| **Mutable** | `list`, `bytearray`, `array.array`, `collections.deque` |
| **Immutable** | `tuple`, `str`, `bytes` |

> ⚠️ "Immutable" doesn't mean deeply immutable. A `tuple` containing a `list` can still have its contents changed — the tuple can't be reassigned, but the inner list can be mutated.

---

## List Comprehensions (Listcomps)

List comprehensions are the idiomatic way to build a new list by filtering or transforming an iterable.

### Basic syntax

```python
# Instead of this:
# Chapter 2 — An Array of Sequences
### Sections covered: Overview of Built-In Sequences → Generator Expressions

> **Core idea:** Python's sequence types share a common interface. Understanding how they're categorized — and using listcomps and genexps correctly — is fundamental to writing idiomatic Python.

---

## Overview of Built-In Sequences

Python sequences can be grouped along **two axes**:

### By what they store

| Type | Description | Examples |
|------|-------------|---------|
| **Container sequences** | Hold references to other objects; can store mixed types | `list`, `tuple`, `collections.deque` |
| **Flat sequences** | Physically store values in their own memory; limited to atomic types | `str`, `bytes`, `array.array` |

Flat sequences are more compact and faster, but can only hold primitives (numbers, characters, bytes). Container sequences are more flexible but can surprise you when they hold mutable objects.

### By mutability

| Type | Examples |
|------|---------|
| **Mutable** | `list`, `bytearray`, `array.array`, `collections.deque` |
| **Immutable** | `tuple`, `str`, `bytes` |

> ⚠️ "Immutable" doesn't mean deeply immutable. A `tuple` containing a `list` can still have its contents changed — the tuple can't be reassigned, but the inner list can be mutated.

---

## List Comprehensions (Listcomps)

List comprehensions are the idiomatic way to build a new list by filtering or transforming an iterable.

### Basic syntax

```python
# Instead of this:
codes = []
for symbol in '$¢£¥€¤':
    codes.append(ord(symbol))

# Do this:
codes = [ord(symbol) for symbol in '$¢£¥€¤']
```

### Readability is the goal

Listcomps should be used when they make code *clearer*, not just shorter. If a listcomp is getting long or complex, a regular `for` loop may be more readable.

### Local scope

In Python 3, variables inside a listcomp are **local** — they don't leak into the surrounding scope:

```python
x = 'ABC'
codes = [ord(x) for x in x]  # 'x' inside is local
x  # Still 'ABC' — not overwritten
```

### Listcomps vs `map` and `filter`

Listcomps build lists from sequences or any other iterable by filtering and transforming items. `filter` and `map` can do the same, but readability suffers. Listcomps are generally preferred because they're more readable and don't require `lambda`.

```python
# map + filter approach (less readable)
list(filter(lambda c: c > 127, map(ord, symbols)))

# Listcomp approach (cleaner)
[ord(s) for s in symbols if ord(s) > 127]
```

### Cartesian Products

Listcomps can compute the cartesian product of two or more iterables using nested `for` clauses:

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']

tshirts = [(color, size) for color in colors for size in sizes]
# [('black', 'S'), ('black', 'M'), ..., ('white', 'L')]
```

The order of the `for` clauses matches what you'd write in nested loops — the first `for` is the outer loop.

---

## Generator Expressions (Genexps)

Generator expressions use the same syntax as listcomps but with **parentheses** instead of brackets. The key difference: they **don't build the whole list in memory** — they yield items one at a time.

```python
# Listcomp — builds entire list in memory
[ord(s) for s in symbols]

# Genexp — yields one item at a time
(ord(s) for s in symbols)
```

### When to use genexps

Listcomps build lists, but to fill up all other sequences, genexps are the way to go. Genexps save memory because they only generate one element at a time instead of storing the whole thing in memory.

Use a genexp when:
- You're initializing a non-list sequence (tuple, array, etc.)
- You're going to iterate over the result once and don't need to store it
- The dataset is large and memory matters

```python
# Initializing a tuple
symbols = '$¢£¥€¤'
tuple(ord(symbol) for symbol in symbols)

# Initializing an array
import array
array.array('I', (ord(symbol) for symbol in symbols))
```

### Genexps and Cartesian Products

When using a genexp for a cartesian product, the generator expression yields items one by one; a list with all variations is never produced. This is a big win for large datasets:

```python
# Only one tuple is created at a time — memory efficient
for tshirt in (f'{c} {s}' for c in colors for s in sizes):
    print(tshirt)
```

### Syntax note

If a genexp is the **only argument** to a function, you can omit the extra parentheses:

```python
sum(ord(s) for s in symbols)   # OK — no double parens needed
sum((ord(s) for s in symbols)) # Also OK, just redundant
```

---

## Key Takeaways

- Python sequences split into **container vs flat** and **mutable vs immutable** — knowing this helps you pick the right tool.
- Use **listcomps** for building lists; they're more readable than `map`/`filter` + `lambda`.
- Listcomp variables in Python 3 are **locally scoped** — no leakage.
- Use **genexps** when you don't need to store the whole result — they're memory-efficient and work great for initializing non-list sequences.
- Genexps and listcomps both support **cartesian products** with multiple `for` clauses.

---

## Tuples Are Not Just Immutable Lists

Tuples do double duty in Python: they work as **immutable lists** and as **records with no field names**. The record use is often overlooked but is just as important.

### Tuples as Records

When used as records, each item in a tuple holds data for one field, and the **position gives it meaning**. Swapping or reordering items would break the data.

```python
# Coordinates — position is the meaning
tokyo = (35.689722, 139.691667)
city, year, pop = ('Tokyo', 2003, 32_450)

# Iterating over structured records
traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ('ESP', 'XDA205856')]
for country, passport in sorted(traveler_ids):
    print(f'{country}/{passport}')
```

The `_` convention is used to discard items you don't need during unpacking:

```python
_, passport = ('USA', '31195855')  # only care about the passport
```

### Tuples as Immutable Lists

When used as an immutable list, tuples signal that the sequence won't change. Two practical benefits: clarity of intent, and potential performance gains (Python can optimize fixed-length tuples).

> ⚠️ A tuple's *immutability applies to the references it holds*, not the objects themselves. A tuple containing a list can still have that list mutated — just not replaced.

```python
t = (1, [2, 3])
t[1].append(4)  # This works — the list inside is mutable
t[1] = [9]      # This fails — can't reassign the tuple's reference
```

### Named Tuples

`collections.namedtuple` creates tuple subclasses with field names, making record-style tuples much more readable without the memory overhead of a full object.

```python
from collections import namedtuple

City = namedtuple('City', 'name country population coordinates')
tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667))

tokyo.name        # 'Tokyo'
tokyo.population  # 36.933
```

Useful named tuple attributes:
- `._fields` — tuple of field names
- `._make(iterable)` — create an instance from any iterable
- `._asdict()` — return as a `dict`

```python
City._fields  # ('name', 'country', 'population', 'coordinates')
tokyo._asdict()  # {'name': 'Tokyo', 'country': 'JP', ...}
```

### Comparing Tuple and List Methods

Tuples support all list methods that don't involve adding or removing items. The main things tuples *lack* compared to lists are `append`, `clear`, `insert`, `pop`, `remove`, `reverse`, and `sort`. Everything read-only (indexing, counting, slicing, `in`) works the same.

---

## Unpacking Sequences and Iterables

Unpacking is one of Python's most elegant features — it lets you assign the items of any iterable to multiple variables in a single statement. It works with any iterable, not just tuples or lists.

### Basic unpacking

```python
coordinates = (35.689722, 139.691667)
lat, lon = coordinates  # clean, readable, no indexing needed

# Swap variables without a temp — a classic Python idiom
a, b = b, a
```

### Using `*` to grab excess items

The `*` operator lets you capture a variable number of "leftover" items into a list. It can appear anywhere in the assignment — beginning, middle, or end:

```python
first, *rest = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4, 5]

*head, last = [1, 2, 3, 4, 5]
# head = [1, 2, 3, 4], last = 5

first, *middle, last = [1, 2, 3, 4, 5]
# first = 1, middle = [2, 3, 4], last = 5
```

The `*` variable always receives a list, even if it captures zero or one item.

### Unpacking with `*` in function calls and sequence literals

`*` can be used to unpack iterables into function calls or when building new sequences:

```python
# In function calls
def add(a, b, c): return a + b + c
args = (1, 2, 3)
add(*args)  # same as add(1, 2, 3)

# In sequence literals (Python 3.5+)
a = [1, 2]
b = [3, 4]
[*a, *b]       # [1, 2, 3, 4]
(*a, *b)       # (1, 2, 3, 4)
{*a, *b}       # {1, 2, 3, 4}
```

### Nested unpacking

The target of an unpacking assignment can have nested tuples — Python will match the structure as long as it mirrors the shape of the data:

```python
metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
]

for name, _, _, (lat, lon) in metro_areas:
    print(f'{name}: {lat}, {lon}')
```

The nested `(lat, lon)` unpacks the inner tuple directly in the `for` loop.

### Pattern Matching with sequences (Python 3.10+)

The `match/case` statement introduced in Python 3.10 supports sequence patterns — a powerful extension of unpacking that can match on structure, type, and value simultaneously:

```python
def handle(command):
    match command.split():
        case ['quit']:
            print('Quitting')
        case ['go', direction]:
            print(f'Going {direction}')
        case ['pick', item, 'from', container]:
            print(f'Picking {item} from {container}')
        case _:
            print('Unknown command')
```

Pattern matching is more expressive than `if/elif` chains for complex branching on sequence shapes.

---# Pattern Matching with Sequences

> **New in Python 3.10.** `match/case` brings structural pattern matching to Python — it's more powerful than `if/elif` chains when branching on the shape or content of a sequence.

### How sequence patterns work

A `case` clause matches if the subject is a sequence whose items match the pattern positionally. Unlike unpacking, patterns can also check literal values and types simultaneously:

```python
def handle(command):
    match command.split():
        case ['quit']:
            print('Quitting')
        case ['go', direction]:
            print(f'Going {direction}')
        case ['go', direction] if direction in ('north', 'south'):
            print(f'Going {direction}')  # guard clause
        case ['pick', item, 'from', container]:
            print(f'Picking {item} from {container}')
        case _:
            print(f'Unknown command')
```

The `case _:` wildcard is the catch-all — equivalent to `else`.

### Key rules for sequence patterns

- Any sequence type can be matched (lists, tuples, etc.) — but **not** `str`, `bytes`, or `bytearray` (they are sequences but treated as atomic values in patterns).
- You can use `*extra` inside a pattern to capture remaining items, just like in unpacking:

```python
match record:
    case [name, *rest]:
        print(f'Name: {name}, rest: {rest}')
```

- Type constraints can be added inline:

```python
match point:
    case [int(x), int(y)]:
        print(f'Integer point: {x}, {y}')
```

- **Guard clauses** (`if` after a `case`) add extra conditions that must be true for the case to match.

### Pattern matching in an interpreter (the book's example)

The book demonstrates `match/case` by building a simple Scheme-like interpreter where commands are represented as lists. Pattern matching lets you express complex dispatch logic cleanly — matching on command length, specific keywords, and variable capture all in one `case` clause — something that would require deeply nested `if/elif` chains otherwise.

---

## Slicing

Slicing is a core feature of Python sequences — it lets you extract sub-sequences without writing loops, and on mutable sequences, you can also assign to slices.

### Why slices and ranges exclude the last item

Python uses exclusive upper bounds throughout (`s[a:b]` includes index `a` up to but *not* `b`). There are three practical reasons:

1. It's easy to see the length of a slice: `s[:3]` has 3 items.
2. Non-overlapping ranges split cleanly: `s[:3]` and `s[3:]` partition `s` with no duplication or gap.
3. It works naturally with zero-based indexing.

```python
s = 'bicycle'
s[:3]   # 'bic'  — first 3 characters
s[3:]   # 'ycle' — everything from index 3 onward
```

The case for exclusive upper bounds was famously argued by Dutch computer scientist Edsger W. Dijkstra — the short version is that it makes length calculations and non-overlapping splits trivially clean, and it composes naturally with zero-based indexing.

### Stride and step: `s[a:b:c]`

The full slice notation `s[a:b:c]` accepts a third argument — the **step** (or stride). It causes the slice to skip items, and a negative step reverses direction:

```python
s = 'bicycle'
s[::3]   # 'bye'     — every 3rd character
s[::-1]  # 'elcycib' — reversed
s[::-2]  # 'eccb'    — reversed, every 2nd character
```

A negative stride is the idiomatic way to reverse a sequence in Python. Note that `a` and `b` can be omitted when using a step — Python fills in the appropriate start/end automatically.

### Slice objects

Slices are actually `slice(start, stop, step)` objects under the hood. You can name them for readability — especially useful when working with fixed-format data like CSV files or byte streams:

```python
# Instead of magic index numbers:
UNIT_PRICE = slice(40, 52)
DESCRIPTION = slice(6, 40)

line = '...'
print(line[DESCRIPTION], line[UNIT_PRICE])
```

Named slices make code self-documenting and easy to update.

### Multidimensional slicing and ellipsis

Python's `[]` operator can accept **comma-separated indices or slices** for multidimensional access. This is used by libraries like NumPy:

```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6]])
a[1, :]    # second row: array([4, 5, 6])
a[:, 1]    # second column: array([2, 5])
a[1, 1:3]  # row 1, columns 1–2: array([5, 6])
```

The **ellipsis** (`...`) is shorthand for "all remaining dimensions" in NumPy:

```python
x[i, ...]   # equivalent to x[i, :, :, :] for a 4D array
```

In plain Python lists, multidimensional indexing is not supported — only one index at a time.

### Assigning to slices

On **mutable** sequences, you can assign to a slice to replace, insert, or delete a range of items in-place:

```python
l = list(range(10))   # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

l[2:5] = [20, 30]     # replace items 2–4 with two values
# [0, 1, 20, 30, 5, 6, 7, 8, 9]

del l[5:7]            # delete items at index 5 and 6
# [0, 1, 20, 30, 5, 8, 9]

l[3::2] = [11, 22]    # assign to a strided slice
# [0, 1, 20, 11, 5, 22, 9]
```

> ⚠️ When assigning to a slice, the right-hand side must be an iterable — even if you're replacing with a single value, it must be wrapped in a list or other iterable.

---

## Using `+` and `*` with Sequences

> **Core idea:** Both `+` and `*` always produce a **new object** — they never modify either of the original sequences. But `*` with mutable objects has a subtle trap worth knowing.

### Concatenation with `+`

`+` joins two sequences of the **same type** into a new sequence:

```python
[1, 2, 3] + [4, 5, 6]   # [1, 2, 3, 4, 5, 6]
(1, 2) + (3, 4)          # (1, 2, 3, 4)
'abc' + 'def'            # 'abcdef'
```

Both operands must be the same type — you can't concatenate a list and a tuple directly.

### Repetition with `*`

`*` repeats a sequence a given number of times, producing a new sequence:

```python
[0] * 5        # [0, 0, 0, 0, 0]
'abc' * 3      # 'abcabcabc'
(1, 2) * 3     # (1, 2, 1, 2, 1, 2)
```

This is commonly used to initialise a list of a fixed size with a default value.

### ⚠️ The Building Lists of Lists trap

A common mistake when initialising nested lists with `*` is that the **same inner object gets repeated by reference**, not copied:

```python
# WRONG — all rows are the same list object
board = [['_'] * 3] * 3
board[1][1] = 'X'
# [['_', 'X', '_'], ['_', 'X', '_'], ['_', 'X', '_']]
# Changing one row changes all of them!
```

This happens because `* 3` copies the *reference* to the inner list three times, not the list itself. The fix is to use a listcomp, which creates a fresh list object for each row:

```python
# CORRECT — each row is a distinct list object
board = [['_'] * 3 for _ in range(3)]
board[1][1] = 'X'
# [['_', '_', '_'], ['_', 'X', '_'], ['_', '_', '_']]
```

The listcomp version is equivalent to:

```python
board = []
for _ in range(3):
    row = ['_'] * 3   # a new list is created each iteration
    board.append(row)
```

> 💡 **Rule of thumb:** Use `*` freely with immutable objects (ints, strings, tuples). For nested mutable objects like lists, always use a listcomp to ensure independent copies.

---

## Key Takeaways (updated)

- Tuples serve **two roles**: lightweight records (position = meaning) and immutable lists — don't conflate them.
- Use `namedtuple` when you want a tuple with self-documenting field names and no class overhead.
- A tuple is only truly immutable if all its contents are immutable — watch out for mutable items inside.
- **Unpacking** works with any iterable and is almost always cleaner than indexing.
- Use `*` to capture leftover items, unpack into function calls, or merge sequences.
- Nested unpacking mirrors the shape of your data structure directly in the assignment.
- `match/case` sequence patterns are more powerful than unpacking — they match on structure, value, and type at once, with optional guard clauses.
- Slices use **exclusive upper bounds** — consistent with `range()` and zero-based indexing.
- Name your slices with `slice()` objects to replace magic numbers with readable labels.
- Mutable sequences support **slice assignment** — replace, insert, or delete ranges in-place.
- `+` and `*` always produce a **new sequence** — originals are never modified.
- Never use `[obj] * n` to initialise nested mutable sequences — use a listcomp instead.

---

*Source: Fluent Python, 2nd Edition — Luciano Ramalho, Chapter 2 (partial)*  
*Remaining sections: `+` and `*` with Sequences, Augmented Assignment, `sort` and `sorted`, `bisect`, Arrays, Memory Views, Deques — to be added*

```python

*Source: Fluent Python, 2nd Edition — Luciano Ramalho, Chapter 2 (partial)*  
*Remaining sections: Tuples, Unpacking, Slicing, Arrays, Deques — to be added*
