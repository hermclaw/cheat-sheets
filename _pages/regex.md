---
title: "Regex Cheat Sheet"
description: "Comprehensive regular expression reference covering syntax, patterns, flags, and Python re module usage."
layout: default
---

## Anchors

| Symbol | Meaning | Example | Matches |
|--------|---------|---------|---------|
| `^` | Start of string/line | `^hello` | "hello" at start |
| `$` | End of string/line | `world$` | "world" at end |
| `\b` | Word boundary | `\bcat\b` | "cat" but not "scatter" |
| `\B` | Non-word boundary | `\Bcat\B` | "scatter" but not "cat" |
| `\A` | Start of string only | `\Ahello` | "hello" (even in multiline) |
| `\z` | End of string only | `world\z` | "world" at absolute end |

```python
import re
re.search(r"^hello", "hello world")     # ✅ Match
re.search(r"^hello", "say hello")       # ❌ None
re.search(r"world$", "hello world")     # ✅ Match
re.search(r"\bcat\b", "cat")            # ✅ Match
re.search(r"\bcat\b", "scatter")        # ❌ None
```

## Character Classes

| Pattern | Meaning | Example |
|---------|---------|---------|
| `[abc]` | Any of a, b, c | `[aeiou]` — vowels |
| `[^abc]` | Not a, b, c | `[^0-9]` — non-digit |
| `[a-z]` | Range a to z | `[A-Za-z]` — letters |
| `[0-9]` | Range 0 to 9 | digits |
| `.` | Any char (except newline) | `a.c` matches "abc", "a1c" |
| `\d` | Digit `[0-9]` | `\d{3}` — 3 digits |
| `\D` | Non-digit `[^0-9]` | `\D+` — non-digits |
| `\w` | Word char `[a-zA-Z0-9_]` | `\w+` — word |
| `\W` | Non-word char | `\W+` — non-word |
| `\s` | Whitespace `[ \t\r\n\f]` | `\s+` — spaces |
| `\S` | Non-whitespace | `\S+` — non-space |
| `[\s\S]` | Any char (including newline) | Alternative to `.` with DOTALL |

```python
re.findall(r"\d+", "a1b22c333")        # → ['1', '22', '333']
re.findall(r"\w+", "hello world")      # → ['hello', 'world']
re.findall(r"[aeiou]", "hello")        # → ['e', 'o']
re.findall(r"[^aeiou]", "hello")       # → ['h', 'l', 'l']
```

## Quantifiers

| Symbol | Meaning | Example |
|--------|---------|---------|
| `*` | 0 or more | `ab*c` → "ac", "abc", "abbc" |
| `+` | 1 or more | `ab+c` → "abc", "abbc" |
| `?` | 0 or 1 (optional) | `colou?r` → "color", "colour" |
| `{n}` | Exactly n | `\d{4}` → "1234" |
| `{n,}` | n or more | `\d{2,}` → "12", "123", ... |
| `{n,m}` | Between n and m | `\d{2,4}` → "12", "123", "1234" |

### Greedy vs Lazy (Non-Greedy)

```python
# Greedy (default) — match as much as possible
re.search(r"<.*>", "<p>hello</p>")     # → '<p>hello</p>'

# Lazy — match as little as possible
re.search(r"<.*?>", "<p>hello</p>")    # → '<p>'
```

| Greedy | Lazy | Meaning |
|--------|------|---------|
| `*` | `*?` | 0 or more |
| `+` | `+?` | 1 or more |
| `?` | `??` | 0 or 1 |
| `{n,}` | `{n,}?` | n or more |
| `{n,m}` | `{n,m}?` | Between n and m |

## Groups & Capture

### Capturing Groups

```python
m = re.search(r"(\w+)@(\w+)\.(\w+)", "user@example.com")
m.group(0)        # → 'user@example.com'  (full match)
m.group(1)        # → 'user'               (first group)
m.group(2)        # → 'example'
m.group(3)        # → 'com'
m.groups()        # → ('user', 'example', 'com')
m.start(1)        # → 0  (start index of group 1)
m.end(1)          # → 4
m.span(1)         # → (0, 4)
```

### Non-Capturing Groups

```python
# (?:...) — group without capturing
re.findall(r"(?:ab)+", "ababab")         # → ['ababab']
re.findall(r"(ab)+", "ababab")           # → ['ab']  (last capture)
```

### Named Groups

```python
# Python / .NET / PCRE syntax
m = re.search(r"(?P<user>\w+)@(?P<domain>\w+)\.(\w+)", "user@example.com")
m.group("user")                          # → 'user'
m.group("domain")                        # → 'example'
m.groupdict()                            # → {'user': 'user', 'domain': 'example'}

# Backreference by name
re.search(r"(?P<word>\w+) (?P=word)", "hello hello")     # ✅
re.search(r"(?P<word>\w+) (?P=word)", "hello world")     # ❌

# Backreference by number
re.search(r"(\w+) \1", "hello hello")    # ✅
```

### Backreferences

```python
# \1, \2, ... refer to captured groups
re.search(r"(\w+)\s+\1", "hello hello")  # ✅ (same word repeated)
re.search(r"(\w+)\s+\1", "hello world")  # ❌
```

## Alternation

```python
# | acts like OR
re.search(r"cat|dog", "I have a cat")    # ✅ 'cat'
re.search(r"cat|dog", "I have a dog")    # ✅ 'dog'

# Group with alternation
re.search(r"(cat|dog)s?", "dogs")        # ✅ 'dogs'
re.search(r"gr(a|e)y", "gray")           # ✅ 'gray'
re.search(r"gr(a|e)y", "grey")           # ✅ 'grey'

# Word boundaries with alternation
re.search(r"\b(cat|dog)\b", "cats")      # ❌
```

## Lookarounds

### Lookahead

```python
# Positive lookahead: X(?=Y) — X followed by Y (Y not consumed)
re.search(r"\w+(?=\.com)", "example.com")  # → 'example'
re.findall(r"\d+(?=px)", "10px 20px 30em") # → ['10', '20']

# Negative lookahead: X(?!Y) — X NOT followed by Y
re.findall(r"\d+(?!px)", "10px 20em 30")   # → ['2', '30']
```

### Lookbehind

```python
# Positive lookbehind: (?<=Y)X — X preceded by Y (Y not consumed)
re.search(r"(?<=\$)\d+", "Price: $42")     # → '42'
re.findall(r"(?<=@)\w+", "user@domain")    # → ['domain']

# Negative lookbehind: (?<!Y)X — X NOT preceded by Y
re.findall(r"(?<!@)\w+@\w+", "user@domain") # → ['user@domain']
```

### Summary

| Type | Syntax | Meaning |
|------|--------|---------|
| Positive lookahead | `X(?=Y)` | X followed by Y |
| Negative lookahead | `X(?!Y)` | X not followed by Y |
| Positive lookbehind | `(?<=Y)X` | X preceded by Y |
| Negative lookbehind | `(?<!Y)X` | X not preceded by Y |

Note: Python's `re` module requires fixed-width lookbehinds (but `regex` library does not).

## Flags

| Flag | Inline | Meaning |
|------|--------|---------|
| `re.IGNORECASE` | `(?i)` | Case-insensitive |
| `re.MULTILINE` | `(?m)` | `^` and `$` match per line |
| `re.DOTALL` | `(?s)` | `.` matches newline |
| `re.VERBOSE` | `(?x)` | Allow whitespace and comments |
| `re.ASCII` | `(?a)` | ASCII-only for `\w`, `\d`, etc. |
| `re.UNICODE` | `(?u)` | Unicode matching (default) |

```python
# Multiple flags
pattern = re.compile(r"pattern", re.IGNORECASE | re.MULTILINE)

# Inline flags
re.search(r"(?i)hello", "HELLO")         # ✅ case-insensitive
re.search(r"(?im)^line", "LINE 1\nLINE 2")  # ✅ multiline + case-insensitive

# Verbose mode
pattern = re.compile(r"""
    \b              # word boundary
    \d{3}           # area code
    [-.]?           # optional separator
    \d{3}           # exchange
    [-.]?           # optional separator
    \d{4}           # subscriber
    \b              # word boundary
""", re.VERBOSE)
```

## Common Patterns

### Email

```python
# Basic (covers most common cases)
r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"

# More strict
r"(?:[a-zA-Z0-9._%+-]+)@(?:[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})"
```

### URL

```python
# Basic URL
r"https?://(?:www\.)?[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}(?:/[^\s]*)?"

# With protocol
r"(?:https?|ftp)://[^\s/$.?#].[^\s]*"
```

### IPv4 Address

```python
# IPv4 (0-255 per octet)
r"(?:25[0-5]|2[0-4]\d|1\d{2}|[1-9]\d|\d)(?:\.(?:25[0-5]|2[0-4]\d|1\d{2}|[1-9]\d|\d)){3}"

# Simple (less strict)
r"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}"
```

### IPv6 Address

```python
r"(?:[0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}"
```

### Phone Number (US)

```python
# Various formats: 555-1234, (555) 123-4567, 555.123.4567, +1-555-123-4567
r"(?:\+?1[-.\s]?)?\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}"

# Strict 10-digit
r"\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}"
```

### Date Formats

```python
# YYYY-MM-DD
r"\d{4}-\d{2}-\d{2}"

# MM/DD/YYYY
r"\d{2}/\d{2}/\d{4}"

# Flexible
r"\d{4}[-/]\d{2}[-/]\d{2}"
```

### HTML Tags

```python
# Matching tags (with content)
r"<(\w+)[^>]*>.*?</\1>"

# Self-closing
r"<\w+[^>]*/>"

# Extract attributes
r'(\w+)=[\"\'](.*?)[\"\']'
```

### Password Validation

```python
# At least 8 chars, one uppercase, one lowercase, one digit, one special
r"^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}$"
```

## Python re Module

### Core Functions

```python
import re

# Compile (for reuse)
pattern = re.compile(r"\d+", re.IGNORECASE)

# Search — first match anywhere
re.search(r"\d+", "abc 123 def")         # → <Match: '123'>
m = re.search(r"(\d+)", "abc 123")
m.group(0)                                # → '123'
m.group(1)                                # → '123'
m.start()                                 # → 4
m.end()                                   # → 7
m.span()                                  # → (4, 7)

# Match — match at start of string only
re.match(r"\d+", "123abc")               # → <Match: '123'>
re.match(r"\d+", "abc123")               # → None

# Fullmatch — entire string must match
re.fullmatch(r"\d{3}", "123")            # ✅
re.fullmatch(r"\d{3}", "1234")           # ❌

# Findall — all non-overlapping matches
re.findall(r"\d+", "a1b22c333")          # → ['1', '22', '333']
re.findall(r"(\d+)([a-z])", "a1b22c")   # → [('1', 'b'), ('22', 'c')]

# Finditer — iterator of Match objects
for m in re.finditer(r"\d+", "a1b22c"):
    print(m.group(), m.span())

# Sub — substitution
re.sub(r"\d+", "NUM", "a1b22c")         # → 'aNUMbNUMc'
re.sub(r"\d+", "NUM", "a1b22c", count=1) # → 'aNUMb22c'

# Sub with function
re.sub(r"\d+", lambda m: str(int(m.group()) * 2), "a1b2c3")
# → 'a2b4c6'

# Subn — substitution with count
result, count = re.subn(r"\d+", "X", "a1b22c333")
# → ('aXbXXcXXX', 3)

# Split — split by pattern
re.split(r"[,\s]+", "a, b  c,d")        # → ['a', 'b', 'c', 'd']
re.split(r"(\d+)", "a1b22c")            # → ['a', '1', 'b', '22', 'c']
re.split(r",", "a,b,,c", maxsplit=2)    # → ['a', 'b', ',c']

# Escape — escape special characters
re.escape("file.txt")                    # → 'file\\.txt'
re.escape("price: $10")                  # → 'price\\: \\$10'
```

### Match Object Methods

```python
m = re.search(r"(?P<name>\w+)@(?P<domain>\w+)\.(\w+)", "user@example.com")

m.group()              # → 'user@example.com' (full match)
m.group(0)             # same
m.group(1)             # → 'user'
m.group("name")        # → 'user'
m.groups()             # → ('user', 'example', 'com')
m.groupdict()          # → {'name': 'user', 'domain': 'example'}
m.start(1)             # → 0
m.end(1)               # → 4
m.span(1)              # → (0, 4)
m.re                   # → compiled regex object
m.string               # → 'user@example.com'
m.lastindex            # → 3 (last group)
m.lastgroup            # → None or last named group
m.expand(r"\g<name>@\g<domain>")  # → 'user@example'
```

### Compiled Regex Methods

```python
p = re.compile(r"\d+")
p.search("abc 123")                # Search
p.match("123abc")                  # Match
p.findall("a1b22")                 # Findall
p.finditer("a1b22")                # Finditer
p.sub("X", "a1b22")               # Substitute
p.subn("X", "a1b22")              # Substitute with count
p.split("a1b22c")                 # Split
p.pattern                          # → original pattern string
p.flags                            # → flags integer
p.groups                           # → number of groups
```

## Special Characters

| Char | Escaped | Meaning |
|------|---------|---------|
| `.` | `\.` | Literal dot |
| `*` | `\*` | Literal asterisk |
| `+` | `\+` | Literal plus |
| `?` | `\?` | Literal question mark |
| `(` | `\(` | Literal open paren |
| `)` | `\)` | Literal close paren |
| `[` | `\[` | Literal open bracket |
| `]` | `\]` | Literal close bracket |
| `{` | `\{` | Literal open brace |
| `}` | `\}` | Literal close brace |
| `\` | `\\` | Literal backslash |
| `^` | `\^` | Literal caret |
| `$` | `\$` | Literal dollar |
| `\|` | `\|` | Literal pipe |

## Testing & Debugging

```python
# Quick test
def test_pattern(pattern, test_strings):
    p = re.compile(pattern)
    for s in test_strings:
        match = p.search(s)
        status = "✅" if match else "❌"
        print(f"{status} {s!r:30} → {match.group(0) if match else 'None'}")

test_pattern(r"\d{3}-\d{4}", [
    "123-4567",    # ✅
    "12-3456",     # ❌
    "abc-defg",    # ❌
    "phone: 123-4567 ext",  # ✅
])
```

### Useful Online Tools

- regex101.com — Interactive tester with explanation
- regexr.com — Community patterns and reference
- pythex.org — Python-specific regex tester
