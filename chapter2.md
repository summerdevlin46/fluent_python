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

*Source: Fluent Python, 2nd Edition — Luciano Ramalho, Chapter 2 (partial)*  
*Remaining sections: Tuples, Unpacking, Slicing, Arrays, Deques — to be added*
