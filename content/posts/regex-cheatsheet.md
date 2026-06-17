---
title: "Regex Complete Cheatsheet"
description: "A practical regular expressions cheatsheet for daily coding — syntax, quantifiers, groups, lookahead, common patterns, and real-world examples in JavaScript, Python, and Vim."
date: 2026-06-17
tags: ["regex", "regular-expressions", "cheatsheet", "developer-tools"]
url: /regex
draft: false
---

# Regex Complete Cheatsheet

Regular expressions are pattern-matching tools built into nearly every programming language, editor, and command-line tool. They let you search, validate, extract, and transform text with a concise pattern language.

This cheatsheet is designed as a daily reference — not just a list of syntax, but practical examples you'll actually use when parsing logs, validating input, refactoring code, or searching through a codebase.

---

---

## What is Regex

A regular expression (regex) is a sequence of characters that defines a search pattern. You give the engine a pattern, and it finds text that matches.

```text
Pattern:  \d{3}-\d{4}
Matches:  "555-1234" in "Call 555-1234 for info"
```

Regex is used everywhere:

- **Validation** — check if an email address or phone number is well-formed
- **Search** — find all URLs in a document
- **Extract** — pull dates out of log lines
- **Replace** — rename variables across a codebase
- **Split** — break a CSV line into fields

The syntax is mostly standard across languages (PCRE-compatible), but Vim has its own dialect. This cheatsheet covers both.

---

## Basic Matching

At the simplest level, a regex is just a literal string. The pattern `error` matches the substring "error" wherever it appears.

```text
Pattern:  error
Text:     "An error occurred in the server"
Match:    "error" (position 3-7)
```

### Case Sensitivity

By default, regex is **case-sensitive**:

```text
Pattern:  Error
Text:     "error" → no match
Text:     "Error" → match
Text:     "ERROR" → no match
```

To match case-insensitively, use the `i` flag (covered in [Common Flags](#common-flags)):

```text
Pattern:  /error/i
Matches:  "error", "Error", "ERROR", "eRrOr"
```

### Matching Multiple Characters in Sequence

Patterns are matched left-to-right, character by character:

```text
Pattern:  404
Text:     "HTTP 404 Not Found"
Match:    "404" (position 5-7)
```

```text
Pattern:  not found
Text:     "HTTP 404 Not Found" → no match (case-sensitive)
Pattern:  /not found/i → matches "Not Found"
```

---

## Metacharacters

These characters have special meaning in regex. If you want to match them literally, you must escape them with a backslash `\`.

| Metachar | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `.` | Any character (except newline) | `c.t` | "cat", "cut", "c9t" |
| `^` | Start of string/line | `^From:` | "From:" at line start |
| `$` | End of string/line | `\.js$` | "app.js" at line end |
| `\|` | Alternation (OR) | `cat\|dog` | "cat" or "dog" |
| `\` | Escape character | `\.` | literal "." |
| `(` `)` | Grouping | `(ab)+` | "ab", "abab" |
| `[` `]` | Character class | `[aeiou]` | any vowel |
| `{` `}` | Quantifier range | `\d{3}` | three digits |
| `*` | Zero or more | `bo*` | "b", "bo", "boo" |
| `+` | One or more | `bo+` | "bo", "boo", "booo" |
| `?` | Zero or one | `colou?r` | "color", "colour" |

### Escaping Metacharacters

When you need to match a literal metacharacter, prefix it with `\`:

```text
Pattern:  \$100\.00
Text:     "Price: $100.00"
Match:    "$100.00"
```

```text
Pattern:  index\.html
Text:     "Serve index.html from /var/www"
Match:    "index.html"
```

```text
Pattern:  file\[0\]
Text:     "Access file[0] in the array"
Match:    "file[0]"
```

### The Dot `.`

The dot matches any single character **except a newline** (unless the `s` flag is set):

```text
Pattern:  192\.168\.0\..
Text:     "192.168.0.1" → match
Text:     "192.168.0.A" → match
Text:     "192x168.0.1" → no match (the first "." is escaped, expects literal dot)
```

A common mistake is using `.` when you mean a literal dot — always escape it in URLs, IPs, and filenames:

```text
Wrong:    192.168.0.1     → also matches "192x168y0z1"
Right:    192\.168\.0\.1  → only matches "192.168.0.1"
```

---

## Character Classes

Character classes match **one character** from a defined set.

### Basic Character Classes

```text
[abc]     → matches "a", "b", or "c"
[0123]    → matches "0", "1", "2", or "3"
[aeiou]   → matches any lowercase vowel
```

### Ranges

```text
[a-z]     → any lowercase letter
[A-Z]     → any uppercase letter
[0-9]     → any digit
[a-zA-Z]  → any letter
[a-zA-Z0-9] → any alphanumeric character
```

### Negation

A caret `^` inside brackets negates the class:

```text
[^0-9]    → any character that is NOT a digit
[^aeiou]  → any character that is NOT a lowercase vowel
[^ ]      → any character that is NOT a space
```

### Shorthand Character Classes

| Shorthand | Equivalent | Meaning |
|-----------|------------|---------|
| `\d` | `[0-9]` | Digit |
| `\D` | `[^0-9]` | Non-digit |
| `\w` | `[a-zA-Z0-9_]` | Word character |
| `\W` | `[^a-zA-Z0-9_]` | Non-word character |
| `\s` | `[ \t\r\n\f]` | Whitespace |
| `\S` | `[^ \t\r\n\f]` | Non-whitespace |

### Practical Examples

Match a hex color code:

```text
Pattern:  #[0-9a-fA-F]{6}
Matches:  "#ff5733", "#00AABB", "#123def"
No match: "#xyz", "#12"
```

Match a CSS class name (starts with letter or underscore):

```text
Pattern:  [a-zA-Z_][\w-]*
Matches:  "nav-bar", "_hidden", "Container"
```

Match a username (alphanumeric, underscore, 3-16 characters):

```text
Pattern:  ^[a-zA-Z0-9_]{3,16}$
Matches:  "john_doe", "user42", "dev"
No match: "ab", "user name", "this_is_way_too_long_username"
```

### Special Characters Inside Classes

Inside `[ ]`, most metacharacters lose their special meaning. Only these are special:

| Character | Rule |
|-----------|------|
| `]` | Close the class — escape as `\]` or put it first `[]abc]` |
| `\` | Escape character — still works as escape |
| `^` | Negation — only special when it's the first character |
| `-` | Range — escape as `\-` or put it first/last `[-abc]` or `[abc-]` |

```text
Pattern:  [$@#!]
Matches:  any one of "$", "@", "#", "!" — no escaping needed
```

### POSIX Character Classes (Shell/grep)

| POSIX | Equivalent | Meaning |
|-------|------------|---------|
| `[:alpha:]` | `[a-zA-Z]` | Letters |
| `[:digit:]` | `[0-9]` | Digits |
| `[:alnum:]` | `[a-zA-Z0-9]` | Alphanumeric |
| `[:space:]` | `[ \t\r\n\f\v]` | Whitespace |
| `[:upper:]` | `[A-Z]` | Uppercase |
| `[:lower:]` | `[a-z]` | Lowercase |
| `[:punct:]` | Punctuation characters | Symbols |

Used inside brackets: `[[:digit:]]` — note the double brackets.

---

## Quantifiers

Quantifiers control **how many times** the preceding element must appear.

| Quantifier | Meaning | Example | Matches |
|------------|---------|---------|---------|
| `*` | 0 or more | `https?://.*` | Any URL starting with http or https |
| `+` | 1 or more | `\d+` | "1", "42", "99999" |
| `?` | 0 or 1 | `colou?r` | "color", "colour" |
| `{n}` | Exactly n | `\d{4}` | "2026", "1999" |
| `{n,}` | n or more | `\w{8,}` | Words with 8+ characters |
| `{n,m}` | Between n and m | `\d{1,3}` | "1", "42", "255" |

### Greedy vs. Lazy (Non-Greedy)

By default, quantifiers are **greedy** — they match as much text as possible:

```text
Pattern:  ".+"
Text:     He said "hello" and "goodbye"
Greedy:   "hello" and "goodbye"   ← matches the entire span
```

Add `?` after a quantifier to make it **lazy** — match as little as possible:

```text
Pattern:  ".+?"
Text:     He said "hello" and "goodbye"
Lazy:     "hello"  and  "goodbye"  ← two separate matches
```

| Greedy | Lazy | Meaning |
|--------|------|---------|
| `*` | `*?` | 0 or more (shortest) |
| `+` | `+?` | 1 or more (shortest) |
| `?` | `??` | 0 or 1 (prefer 0) |
| `{n,m}` | `{n,m}?` | Between n and m (shortest) |

### Real-World Greedy vs. Lazy

**Extracting HTML tag content:**

```text
Greedy:   <.+>
Text:     <span>Hello</span>
Match:    <span>Hello</span>   ← matches everything between first < and last >

Lazy:     <.+?>
Text:     <span>Hello</span>
Matches:  <span>  and  </span>  ← two separate matches
```

**Extracting JSON values:**

```text
Greedy:   "value": "(.+)"
Text:     "value": "hello", "other": "world"
Match:    hello", "other": "world    ← too greedy

Lazy:     "value": "(.+?)"
Text:     "value": "hello", "other": "world"
Match:    hello   ← correct
```

### Possessive Quantifiers (where supported)

Possessive quantifiers (`*+`, `++`, `?+`) are like greedy but **never backtrack**. They're faster when you know backtracking won't help:

```text
Pattern:  \w++\.txt
Text:     "report.txt"
```

Supported in Java, PCRE, and Python's `regex` module (not the built-in `re`).

---

## Anchors

Anchors don't match characters — they match **positions** in the string.

| Anchor | Meaning | Example |
|--------|---------|---------|
| `^` | Start of string (or line with `m` flag) | `^#!/bin/bash` |
| `$` | End of string (or line with `m` flag) | `\.py$` |
| `\b` | Word boundary | `\berror\b` |
| `\B` | Non-word boundary | `\Berror\B` |

### Start `^` and End `$`

```text
Pattern:  ^#
Text:     "# This is a heading" → match (starts with #)
Text:     "Not a # heading"    → no match

Pattern:  ;$
Text:     "let x = 5;"    → match (ends with ;)
Text:     "let x = 5"     → no match
```

Combine both for full-string validation:

```text
Pattern:  ^\d{5}$
Matches:  "90210" (exactly 5 digits, nothing else)
No match: "ZIP: 90210" (extra text)
```

### Word Boundary `\b`

A word boundary is the position between a word character `\w` and a non-word character `\W` (or start/end of string):

```text
Pattern:  \berror\b
Text:     "An error occurred"    → matches "error"
Text:     "RuntimeError thrown"  → no match ("error" is inside "Error")
Text:     "error_code is set"   → no match ("error" is part of "error_code")
```

This is incredibly useful for searching code without false positives:

```text
Pattern:  \bclass\b
Matches:  "class User"         (the keyword)
No match: "className"          (part of another word)
No match: "subclass"           (part of another word)
```

```text
Pattern:  \buser_id\b
Matches:  "SELECT user_id FROM" (exact variable name)
No match: "user_id_backup"     (underscore is a word character)
```

### Non-Word Boundary `\B`

Matches a position that is NOT a word boundary:

```text
Pattern:  \Bport\B
Text:     "transportation" → matches "port" (inside a word)
Text:     "port 8080"     → no match (word boundary before "port")
```

---

## Groups and Capturing

Parentheses `()` create groups. Groups serve two purposes: **grouping** patterns for quantifiers/alternation, and **capturing** matched text for later use.

### Basic Capturing Groups

```text
Pattern:  (\d{4})-(\d{2})-(\d{2})
Text:     "Date: 2026-06-17"
Group 0:  "2026-06-17"  (entire match)
Group 1:  "2026"        (year)
Group 2:  "06"          (month)
Group 3:  "17"          (day)
```

### Non-Capturing Groups `(?:...)`

When you need grouping but don't need to capture:

```text
Pattern:  (?:https?|ftp)://[\w.-]+
Text:     "Visit https://example.com"
Match:    "https://example.com"
```

The `(?:...)` syntax groups `https?` and `ftp` for alternation without creating a capture group. This is slightly faster and keeps your group numbering clean.

### Named Capturing Groups `(?P<name>...)` or `(?<name>...)`

Named groups make your regex self-documenting:

```python
# Python syntax
Pattern:  (?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})

# JavaScript syntax
Pattern:  (?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})
```

```text
Text:     "2026-06-17"
year  →   "2026"
month →   "06"
day   →   "17"
```

### Backreferences `\1`, `\2`

Refer back to a previously captured group within the same regex:

```text
Pattern:  <(\w+)>.*?</\1>
Text:     "<div>Hello</div>"
Group 1:  "div"
\1:       matches "div" again in the closing tag
Match:    "<div>Hello</div>"
```

This ensures the opening and closing tags match:

```text
Pattern:  <(\w+)>.*?</\1>
Text:     "<div>Hello</span>" → no match (div ≠ span)
```

**Finding repeated words** (a classic use case):

```text
Pattern:  \b(\w+)\s+\1\b
Text:     "the the quick brown fox"
Match:    "the the"
```

### Grouping for Quantifiers

```text
Pattern:  (ha)+
Matches:  "ha", "haha", "hahaha"

Pattern:  (\d{1,3}\.){3}\d{1,3}
Matches:  "192.168.1.1" (simplified IP match)
```

---

## Alternation

The pipe `|` acts as OR. It has the **lowest precedence** of all regex operators, so it applies to everything on either side:

```text
Pattern:  cat|dog
Matches:  "cat" or "dog"

Pattern:  error|warning|info
Matches:  "error", "warning", or "info"
```

### Alternation with Grouping

Without grouping, `|` applies to the entire expression on each side:

```text
Pattern:  gray|grey        → matches "gray" or "grey"
Better:   gr(a|e)y         → same result, more explicit
Even:     gr[ae]y          → same result, best for single chars
```

### Practical Example: Matching File Extensions

```text
Pattern:  \.(js|ts|jsx|tsx)$
Matches:  "App.tsx", "index.js", "utils.ts"
No match: "style.css", "README.md"
```

### Practical Example: Log Level Parsing

```text
Pattern:  \[(ERROR|WARN|INFO|DEBUG)\]
Text:     "[ERROR] Database connection failed"
Match:    "[ERROR]"
Group 1:  "ERROR"
```

### Order Matters

The regex engine tries alternatives left to right and stops at the first match:

```text
Pattern:  http|https
Text:     "https://example.com"
Match:    "http"  ← stops here, never tries "https"

Fix:      https?          ← better approach
Or:       https|http      ← put the longer alternative first
```

---

## Lookahead and Lookbehind

Lookarounds are **zero-width assertions** — they check if a pattern exists ahead or behind the current position, without consuming characters. The matched text is not included in the result.

### Positive Lookahead `(?=...)`

"Match X only if followed by Y":

```text
Pattern:  \d+(?= dollars)
Text:     "Price is 100 dollars"
Match:    "100"  (the " dollars" part is asserted but not consumed)
```

```text
Pattern:  \w+(?=\()
Text:     "Call getData() here"
Match:    "getData"  (word followed by an opening parenthesis — matches function names)
```

### Negative Lookahead `(?!...)`

"Match X only if NOT followed by Y":

```text
Pattern:  \d{3}(?!-)
Text:     "555-1234 and 9990"
Match:    "999"  (three digits NOT followed by a dash)
No match: "555"  (followed by a dash)
```

```text
Pattern:  const \w+(?! =)
Text:     "const x = 5" → no match (followed by " =")
Text:     "const y;"    → matches "const y"
```

### Positive Lookbehind `(?<=...)`

"Match X only if preceded by Y":

```text
Pattern:  (?<=\$)\d+(\.\d{2})?
Text:     "Total: $49.99"
Match:    "49.99"  (digits preceded by $, but $ is not in the match)
```

```text
Pattern:  (?<=@)\w+\.\w+
Text:     "Email: user@example.com"
Match:    "example.com"  (domain after @)
```

### Negative Lookbehind `(?<!...)`

"Match X only if NOT preceded by Y":

```text
Pattern:  (?<!un)happy
Text:     "I am happy"   → matches "happy"
Text:     "I am unhappy"  → no match ("happy" preceded by "un")
```

```text
Pattern:  (?<!\.)com\b
Text:     "dot com boom"  → matches "com"
Text:     "example.com"   → no match (preceded by ".")
```

### Combining Lookarounds

**Password validation** — must contain at least one digit, one uppercase, one lowercase, minimum 8 chars:

```text
Pattern:  ^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}$
```

Breaking it down:
- `(?=.*\d)` — somewhere ahead, there's a digit
- `(?=.*[a-z])` — somewhere ahead, there's a lowercase letter
- `(?=.*[A-Z])` — somewhere ahead, there's an uppercase letter
- `.{8,}` — at least 8 characters total

**Match a number not inside quotes:**

```text
Pattern:  (?<!")\b\d+\b(?!")
Text:     Value is 42 and "99"
Match:    "42" only
```

### Lookaround Summary Table

| Syntax | Name | Meaning |
|--------|------|---------|
| `(?=...)` | Positive lookahead | Followed by ... |
| `(?!...)` | Negative lookahead | NOT followed by ... |
| `(?<=...)` | Positive lookbehind | Preceded by ... |
| `(?<!...)` | Negative lookbehind | NOT preceded by ... |

---

## Common Flags

Flags modify how the regex engine interprets the pattern.

| Flag | Name | Effect |
|------|------|--------|
| `g` | Global | Find all matches, not just the first |
| `i` | Case-insensitive | `a` matches "a" and "A" |
| `m` | Multiline | `^` and `$` match line starts/ends, not just string |
| `s` | Dotall / Single-line | `.` also matches newline characters |
| `u` | Unicode | Treat pattern as Unicode (JS) |
| `x` | Extended / Verbose | Ignore whitespace, allow comments (Python, PCRE) |

### Global Flag `g`

```javascript
// Without g — only first match
"aaa".match(/a/)        // ["a"]

// With g — all matches
"aaa".match(/a/g)       // ["a", "a", "a"]
```

### Multiline Flag `m`

```text
Text:
  "Line 1\nLine 2\nLine 3"

Without m:
  ^Line   → matches only "Line 1" (start of string)

With m:
  ^Line   → matches "Line 1", "Line 2", "Line 3" (start of each line)
```

### Extended/Verbose Flag `x`

Allows you to write readable regex with comments and whitespace (Python, PCRE):

```python
pattern = re.compile(r"""
    ^                   # start of string
    (?P<year>\d{4})     # 4-digit year
    -                   # separator
    (?P<month>\d{2})    # 2-digit month
    -                   # separator
    (?P<day>\d{2})      # 2-digit day
    $                   # end of string
""", re.VERBOSE)
```

---

## Practical Patterns

These are real-world patterns you'll reach for frequently. Each one is tested against common valid and invalid inputs.

### Email Address (simplified)

```text
Pattern:  ^[\w.+-]+@[\w-]+\.[\w.-]+$
```

```text
✓  user@example.com
✓  john.doe+tag@company.co.uk
✓  admin@sub.domain.org
✗  @example.com
✗  user@
✗  user@.com
```

> **Note:** Fully RFC 5322-compliant email validation with regex is impractical (the spec allows quoted strings, comments, and other rarely-used syntax). For production, validate the format loosely with regex, then verify with a confirmation email.

### URL

```text
Pattern:  https?://[^\s/$.?#].[^\s]*
```

A more structured version:

```text
Pattern:  https?://[\w.-]+(?::\d+)?(?:/[\w./?%&=~#-]*)?
```

```text
✓  https://example.com
✓  http://api.example.com:8080/v1/users?page=2
✓  https://en.wikipedia.org/wiki/Regular_expression
✗  ftp://files.example.com (use (?:https?|ftp) to include ftp)
✗  example.com (no protocol)
```

### IPv4 Address

```text
Simple:   \d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}

Strict (0-255 per octet):
(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)
```

```text
✓  192.168.1.1
✓  10.0.0.255
✓  0.0.0.0
✗  256.1.1.1    (strict version rejects)
✗  192.168.1    (missing octet)
```

### Phone Number (US format)

```text
Pattern:  ^(\+1[-.\s]?)?(\(?\d{3}\)?[-.\s]?)?\d{3}[-.\s]?\d{4}$
```

```text
✓  555-123-4567
✓  (555) 123-4567
✓  +1-555-123-4567
✓  5551234567
✓  555.123.4567
✗  123-45-6789 (wrong grouping)
```

### Date (YYYY-MM-DD)

```text
Pattern:  ^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])$
```

```text
✓  2026-06-17
✓  2000-01-31
✗  2026-13-01  (invalid month)
✗  2026-06-32  (invalid day)
✗  26-06-17    (2-digit year)
```

### Hex Color Code

```text
Pattern:  ^#([0-9a-fA-F]{3}|[0-9a-fA-F]{6})$
```

```text
✓  #fff
✓  #FF5733
✓  #00aabb
✗  #12       (too short)
✗  #1234567  (too long)
✗  #xyz      (invalid characters)
```

### HTML Tags

**Match opening tags:**

```text
Pattern:  <([a-z][\w-]*)(\s[^>]*)?>
```

```text
✓  <div>
✓  <a href="https://example.com">
✓  <input type="text" />
✓  <my-component class="active">
```

**Match a specific tag with content:**

```text
Pattern:  <h[1-6][^>]*>(.*?)</h[1-6]>
Text:     "<h1>Welcome</h1>"
Match:    "<h1>Welcome</h1>"
Group 1:  "Welcome"
```

> **Warning:** Don't parse complex HTML with regex. Use a proper parser (DOMParser, BeautifulSoup, cheerio). Regex is fine for simple search/replace in known, simple HTML structures.

### Slug (URL-Friendly String)

```text
Pattern:  ^[a-z0-9]+(?:-[a-z0-9]+)*$
```

```text
✓  hello-world
✓  my-blog-post-2026
✓  regex
✗  Hello-World  (uppercase)
✗  -leading     (starts with dash)
✗  trailing-    (ends with dash)
```

### Semantic Version

```text
Pattern:  ^v?(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)(?:-([\w.-]+))?(?:\+([\w.-]+))?$
```

```text
✓  1.0.0
✓  v2.3.1
✓  1.0.0-beta.1
✓  1.0.0-alpha+build.123
✗  1.0        (missing patch)
✗  01.0.0     (leading zero)
```

### Log Line Parser

```text
Pattern:  ^\[(\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2})\]\s\[(\w+)\]\s(.+)$
```

```text
Text:     "[2026-06-17 14:30:45] [ERROR] Database connection timeout after 30s"
Group 1:  "2026-06-17 14:30:45"  (timestamp)
Group 2:  "ERROR"                (level)
Group 3:  "Database connection timeout after 30s"  (message)
```

### UUID

```text
Pattern:  ^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$
```

```text
✓  550e8400-e29b-41d4-a716-446655440000
✗  550e8400e29b41d4a716446655440000  (no dashes)
```

---

## Regex in JavaScript

JavaScript regex uses the `/pattern/flags` literal syntax or the `RegExp` constructor.

### Creating Regex

```javascript
// Literal syntax (preferred when pattern is static)
const pattern = /\d{3}-\d{4}/g;

// Constructor (use when pattern is dynamic)
const userInput = "error";
const pattern = new RegExp(`\\b${userInput}\\b`, "gi");
// Note: double backslash needed in strings
```

### `test()` — Check if a Pattern Matches

Returns `true` or `false`:

```javascript
const emailRegex = /^[\w.+-]+@[\w-]+\.[\w.-]+$/;

emailRegex.test("user@example.com");     // true
emailRegex.test("not-an-email");          // false

// Validate before processing
const input = "admin@company.org";
if (emailRegex.test(input)) {
  sendVerificationEmail(input);
}
```

### `match()` — Find Matches

Returns an array of matches (with `g` flag) or match details (without `g`):

```javascript
// All matches
const text = "Errors on lines 42, 87, and 156";
const numbers = text.match(/\d+/g);
// ["42", "87", "156"]

// Single match with details
const logLine = "[2026-06-17] ERROR: Connection refused";
const result = logLine.match(/\[(\d{4}-\d{2}-\d{2})\]\s(\w+):\s(.+)/);
// result[0] = "[2026-06-17] ERROR: Connection refused"
// result[1] = "2026-06-17"
// result[2] = "ERROR"
// result[3] = "Connection refused"
```

### `matchAll()` — Iterate All Matches with Groups

Returns an iterator of all matches, each with group info:

```javascript
const text = 'import { useState } from "react"; import { useEffect } from "react";';
const importRegex = /import\s+{([^}]+)}\s+from\s+"([^"]+)"/g;

for (const match of text.matchAll(importRegex)) {
  console.log(`Module: ${match[2]}, Imports: ${match[1].trim()}`);
}
// Module: react, Imports: useState
// Module: react, Imports: useEffect
```

### `replace()` — Search and Replace

```javascript
// Simple replacement
const cleaned = "  hello   world  ".replace(/\s+/g, " ").trim();
// "hello world"

// Using capture groups
const isoDate = "2026-06-17";
const usDate = isoDate.replace(/(\d{4})-(\d{2})-(\d{2})/, "$2/$3/$1");
// "06/17/2026"

// Using named groups
const formatted = isoDate.replace(
  /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/,
  "$<month>/$<day>/$<year>"
);
// "06/17/2026"

// Using a function for complex replacements
const template = "Hello {name}, you have {count} messages";
const data = { name: "Alice", count: 5 };
const result = template.replace(/\{(\w+)\}/g, (match, key) => data[key] ?? match);
// "Hello Alice, you have 5 messages"
```

### `replaceAll()` — Replace All Occurrences

```javascript
// replaceAll with string (no regex needed for simple cases)
"a.b.c".replaceAll(".", "/");  // "a/b/c"

// replaceAll with regex (must use g flag)
"CamelCase".replaceAll(/([A-Z])/g, "_$1").toLowerCase().slice(1);
// "camel_case"
```

### `split()` — Split by Pattern

```javascript
// Split CSV with varying whitespace
const line = "Alice,  Bob, Charlie ,  Dave";
const names = line.split(/\s*,\s*/);
// ["Alice", "Bob", "Charlie", "Dave"]

// Split on multiple delimiters
const path = "src/components\\Header\\index.js";
const parts = path.split(/[/\\]/);
// ["src", "components", "Header", "index.js"]

// Split but keep delimiters (using capturing group)
const sentence = "Hello! How are you? I'm fine.";
const segments = sentence.split(/([.!?])\s*/);
// ["Hello", "!", "How are you", "?", "I'm fine", ".", ""]
```

### `exec()` — Stateful Iteration

```javascript
const regex = /TODO:\s*(.+)/g;
const source = `
  // TODO: Fix memory leak
  // TODO: Add error handling
  // TODO: Write tests
`;

let match;
while ((match = regex.exec(source)) !== null) {
  console.log(`Found: ${match[1]} at index ${match.index}`);
}
// Found: Fix memory leak at index 5
// Found: Add error handling at index 34
// Found: Write tests at index 63
```

> **Tip:** Prefer `matchAll()` over `exec()` in modern code — it's cleaner and avoids the statefulness pitfalls of `exec()`.

---

## Regex in Python

Python uses the `re` module (or the third-party `regex` module for advanced features).

### Import and Basic Usage

```python
import re

# Search anywhere in the string
result = re.search(r"\d+", "Order #12345 confirmed")
if result:
    print(result.group())   # "12345"
    print(result.start())   # 7
    print(result.end())     # 12

# Match only at the start
result = re.match(r"\d+", "12345 confirmed")
if result:
    print(result.group())   # "12345"

re.match(r"\d+", "Order #12345")  # None (doesn't start with digits)
```

### `findall()` — All Matches

```python
text = "Temperatures: 72°F, 68°F, 75°F"
temps = re.findall(r"\d+(?=°F)", text)
# ['72', '68', '75']

# With groups, returns a list of tuples
log = "[ERROR] 404 Not Found\n[WARN] 301 Redirect"
entries = re.findall(r"\[(\w+)\]\s(\d+)\s(.+)", log)
# [('ERROR', '404', 'Not Found'), ('WARN', '301', 'Redirect')]
```

### `finditer()` — Match Objects Iterator

```python
text = "Call 555-1234 or 555-5678 for support"
for match in re.finditer(r"\d{3}-\d{4}", text):
    print(f"Found {match.group()} at position {match.start()}")
# Found 555-1234 at position 5
# Found 555-5678 at position 18
```

### `sub()` — Search and Replace

```python
# Clean up whitespace
text = "too   many    spaces"
clean = re.sub(r"\s+", " ", text)
# "too many spaces"

# Using backreferences
text = "John Smith, Jane Doe"
swapped = re.sub(r"(\w+)\s(\w+)", r"\2 \1", text)
# "Smith John, Doe Jane"

# Using named groups
date = "2026-06-17"
formatted = re.sub(
    r"(?P<y>\d{4})-(?P<m>\d{2})-(?P<d>\d{2})",
    r"\g<m>/\g<d>/\g<y>",
    date
)
# "06/17/2026"

# Using a function
def censor(match):
    word = match.group()
    return word[0] + "*" * (len(word) - 1)

text = "Contact admin@secret.com for help"
censored = re.sub(r"\S+@\S+", censor, text)
# "Contact a**************** for help"
```

### `split()` — Split by Pattern

```python
# Split on varying delimiters
text = "apple, banana;  cherry| date"
fruits = re.split(r"[,;|]\s*", text)
# ['apple', 'banana', 'cherry', 'date']

# Limit splits
text = "one:two:three:four:five"
parts = re.split(r":", text, maxsplit=2)
# ['one', 'two', 'three:four:five']
```

### Compiled Patterns

Compile patterns you use repeatedly for better performance:

```python
import re

# Compile once
IP_PATTERN = re.compile(
    r"(?P<ip>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})"
    r"\s-\s-\s"
    r"\[(?P<timestamp>[^\]]+)\]"
    r'\s"(?P<method>\w+)\s(?P<path>\S+)\sHTTP/[\d.]+"'
    r"\s(?P<status>\d{3})"
    r"\s(?P<size>\d+)"
)

# Use many times
with open("/var/log/nginx/access.log") as f:
    for line in f:
        match = IP_PATTERN.search(line)
        if match:
            print(f"{match.group('ip')} - {match.group('status')} - {match.group('path')}")
```

### Verbose Mode

Write readable patterns with `re.VERBOSE`:

```python
email_pattern = re.compile(r"""
    ^                   # start of string
    [\w.+-]+            # username: word chars, dots, plus, hyphen
    @                   # literal @
    [\w-]+              # domain name
    \.                  # literal dot
    [\w.-]+             # TLD (and potential subdomains)
    $                   # end of string
""", re.VERBOSE | re.IGNORECASE)

email_pattern.match("user@example.com")  # Match
```

---

## Regex in Vim

Vim has its own regex dialect that differs from PCRE. The biggest difference: many special characters need to be escaped in Vim's default "magic" mode, which is the opposite of what you're used to.

### Magic Modes

Vim has four magic modes that control which characters are "special":

| Mode | Prefix | Behavior |
|------|--------|----------|
| `\v` (very magic) | `\v` | Closest to PCRE — `+`, `(`, `)`, `{`, `}`, `|` are special |
| `\m` (magic) | `\m` | Default — `.`, `*`, `^`, `$`, `[` are special; must escape `+`, `(`, `{` |
| `\M` (nomagic) | `\M` | Almost everything is literal |
| `\V` (very nomagic) | `\V` | Everything is literal except `\` |

**Recommendation:** Use `\v` (very magic) to get PCRE-like behavior and avoid escape headaches.

### Default Magic Mode vs. Very Magic

Here's the same pattern in default magic and very magic:

```vim
" Default magic — need to escape +, (, ), {, }, |
/\(error\|warning\)\+
:%s/\(\w\+\)\s\+\(\w\+\)/\2 \1/g

" Very magic — like PCRE, cleaner
/\v(error|warning)+
:%s/\v(\w+)\s+(\w+)/\2 \1/g
```

The very magic versions are much more readable. Always prefer `\v`.

### Basic Search

```vim
" Search forward
/pattern

" Search backward
?pattern

" Search with very magic (recommended)
/\v pattern

" Next match
n

" Previous match
N

" Clear search highlighting
:noh
```

### Case Sensitivity in Search

```vim
" Force case-insensitive
/\cpattern
/\cerror        " matches error, Error, ERROR

" Force case-sensitive
/\Cpattern
/\CError        " matches only Error

" Smart case (with 'ignorecase' and 'smartcase' set):
/error          " case-insensitive (all lowercase)
/Error          " case-sensitive (has uppercase)
```

### Useful Search Patterns

```vim
" Find function definitions (JavaScript/TypeScript)
/\vfunction\s+\w+\s*\(
/\v(const|let|var)\s+\w+\s*\=\s*(async\s+)?\(

" Find TODO/FIXME comments
/\v(TODO|FIXME|HACK|XXX):?\s+.+

" Find trailing whitespace
/\s\+$

" Find empty lines
/^$

" Find lines longer than 80 characters
/\v^.{81,}$

" Find duplicate words
/\v<(\w+)\s+\1>

" Find import statements
/\v^import\s+.+\s+from\s+

" Find console.log statements
/\vconsole\.(log|warn|error|debug)\(

" Find hex colors
/\v#[0-9a-fA-F]{3,8}

" Find IP addresses
/\v\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
```

### Search and Replace (Substitute)

The substitute command is one of the most powerful features in Vim:

```vim
" Basic syntax
:%s/pattern/replacement/flags

" Common flags
" g — replace all occurrences on each line (not just the first)
" c — confirm each replacement
" i — case-insensitive
" I — case-sensitive
" n — count matches without replacing
" e — suppress errors when pattern not found
```

### Substitute Examples

```vim
" Replace all occurrences in the file
:%s/oldFunction/newFunction/g

" Replace with confirmation
:%s/oldName/newName/gc
" y = yes, n = no, a = all remaining, q = quit, l = last (replace and quit)

" Replace only in lines 10-50
:10,50s/error/warning/g

" Replace in visual selection (select first, then type :)
:'<,'>s/old/new/g

" Replace only in current line
:s/old/new/g

" Using very magic
:%s/\v(\w+)\.length/\1.size/g
" "array.length" → "array.size"

" Case-insensitive replace
:%s/error/warning/gi

" Count matches without replacing
:%s/TODO//gn
" → "42 matches on 30 lines"
```

### Capture Groups in Substitute

```vim
" Swap first and last names (very magic)
:%s/\v(\w+)\s+(\w+)/\2 \1/g
" "John Smith" → "Smith John"

" Add quotes around unquoted attribute values
:%s/\v(\w+)\=([^ >]+)/\1="\2"/g
" class=active → class="active"

" Convert snake_case to camelCase
:%s/\v_([a-z])/\u\1/g
" "user_name" → "userName"
" "get_all_items" → "getAllItems"

" Convert camelCase to snake_case
:%s/\v([a-z])([A-Z])/\1_\l\2/g
" "userName" → "user_name"
" "getAllItems" → "get_all_items"

" Wrap a word in a function call
:%s/\v<(\d{4}-\d{2}-\d{2})>/parseDate("\1")/g
" 2026-06-17 → parseDate("2026-06-17")

" Add semicolons to lines that don't have them
:%s/\v([^;{}\s])\s*$/\1;/

" Remove trailing whitespace
:%s/\s\+$//e

" Convert double quotes to single quotes
:%s/"/'/g

" Remove HTML tags
:%s/<[^>]*>//g
```

### Special Replacement Sequences

| Sequence | Meaning |
|----------|---------|
| `\1`, `\2` | Captured group 1, 2, etc. |
| `\0` or `&` | Entire match |
| `\u` | Uppercase next character |
| `\l` | Lowercase next character |
| `\U` | Uppercase until `\E` or end |
| `\L` | Lowercase until `\E` or end |
| `\E` | End case change |
| `\r` | Newline (in replacement) |
| `\t` | Tab (in replacement) |

```vim
" Uppercase the first letter of each word
:%s/\v<(\w)/\u\1/g
" "hello world" → "Hello World"

" Uppercase entire match
:%s/\v<(error|warning)>/\U\1/g
" "error" → "ERROR", "warning" → "WARNING"

" Insert a newline after each comma
:%s/,\s*/,\r/g

" Wrap each line in <li> tags
:%s/\v^(.+)$/<li>\1<\/li>/
```

### The Global Command `:g`

Execute a command on all lines matching a pattern:

```vim
" Delete all lines containing 'console.log'
:g/console\.log/d

" Delete all blank lines
:g/^$/d

" Delete all comments (// style)
:g/\v^\s*\/\//d

" Move all TODO comments to end of file
:g/TODO/m$

" Copy all lines with 'import' to register a
:let @a="" | g/\v^import/y A

" Execute normal mode command on matching lines
" — add semicolons to all lines containing 'return'
:g/return/normal A;

" Reverse: execute on lines NOT matching pattern
:v/error/d
" (deletes all lines NOT containing 'error' — keeps only error lines)

" Print all matches with line numbers
:g/pattern/p

" Sort all lines containing a pattern
:g/TODO/m0
```

### Vim Regex Special Atoms

Vim has some unique regex atoms not found in PCRE:

| Atom | Meaning |
|------|---------|
| `\<` | Start of word (like `\b` before word) |
| `\>` | End of word (like `\b` after word) |
| `\zs` | Set start of match (like `\K` in PCRE) |
| `\ze` | Set end of match |
| `\%V` | Match inside visual selection |
| `\%l` | Match at specific line |
| `\%c` | Match at specific column |
| `\_s` | Whitespace including newline |
| `\_.` | Any character including newline |
| `\{-}` | Non-greedy match (like `*?` in PCRE) |

```vim
" Match word boundaries
/\<error\>
" Same as PCRE \berror\b

" Match a function name but not the parentheses
/\vgetData\ze\(
" In "getData()" — matches "getData" only

" Match arguments inside parentheses only
/\v\(\zs[^)]+\ze\)
" In "func(arg1, arg2)" — matches "arg1, arg2"

" Non-greedy matching in Vim uses \{-} instead of *?
/\v<.{-}>
" Matches individual tags: <div>, </div>
" (Vim equivalent of <.+?> in PCRE)

" Match across multiple lines
/\v\_s+
" Matches whitespace including newlines
```

### Searching in Files (vimgrep)

```vim
" Search all JavaScript files in the project
:vimgrep /\vconsole\.log/ **/*.js

" Search and populate quickfix list
:vimgrep /TODO/ **/*.{js,ts,py}

" Navigate results
:cnext      " next match
:cprev      " previous match
:copen      " open quickfix window
:cclose     " close quickfix window
```

---

## Regex in Shell

### grep — Search Files

```bash
# Basic search
grep "error" /var/log/syslog

# Case-insensitive
grep -i "error" server.log

# Extended regex (ERE) — same as egrep
grep -E "(error|warning|critical)" server.log

# Perl-compatible regex (PCRE) — supports lookaheads, etc.
grep -P "(?<=\[)\d{3}(?=\])" server.log

# Show line numbers
grep -n "TODO" src/*.js

# Show only the matched text (not the entire line)
grep -o "\d\+\.\d\+\.\d\+" package.json

# Count matches
grep -c "404" access.log

# Recursive search in directory
grep -rn "deprecated" src/

# Invert match (show lines that DON'T match)
grep -v "^#" config.conf     # skip comment lines

# Show context (lines before/after match)
grep -B 2 -A 2 "error" app.log   # 2 lines before and after

# Multiple patterns
grep -E "^(import|from|require)" app.js

# Match whole words only
grep -w "port" config.yml     # won't match "transport"
```

### grep Practical Examples

```bash
# Find all IP addresses in a log file
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" access.log

# Find all email addresses
grep -oEi "[a-z0-9.+-]+@[a-z0-9.-]+\.[a-z]{2,}" contacts.txt

# Find processes listening on a port
netstat -tlnp | grep -E ":80\b"

# Find files containing a pattern
grep -rl "API_KEY" --include="*.env" .

# Find TODO comments with the author
grep -rnE "TODO\((\w+)\)" src/

# Find non-ASCII characters in source files
grep -P "[^\x00-\x7F]" src/*.js

# Find lines that are longer than 120 characters
grep -nE "^.{121,}$" src/*.py

# Count unique HTTP status codes
grep -oE "HTTP/[0-9.]+ [0-9]{3}" access.log | sort | uniq -c | sort -rn
```

### sed — Stream Editor

```bash
# Basic replacement (first occurrence per line)
sed 's/old/new/' file.txt

# Replace all occurrences
sed 's/old/new/g' file.txt

# In-place editing (modify the file directly)
sed -i '' 's/old/new/g' file.txt          # macOS (BSD sed)
sed -i 's/old/new/g' file.txt             # Linux (GNU sed)

# Using extended regex
sed -E 's/([0-9]{4})-([0-9]{2})-([0-9]{2})/\2\/\3\/\1/g' dates.txt
# "2026-06-17" → "06/17/2026"

# Delete lines matching a pattern
sed '/^#/d' config.conf          # remove comment lines
sed '/^$/d' file.txt             # remove empty lines

# Delete lines between two patterns
sed '/START/,/END/d' file.txt

# Print only matching lines (like grep)
sed -n '/error/p' log.txt

# Replace only on specific lines
sed '5s/old/new/' file.txt       # only line 5
sed '10,20s/old/new/g' file.txt  # lines 10-20

# Multiple replacements
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# Insert text before/after a line
sed '/pattern/i\new line before' file.txt    # GNU sed
sed '/pattern/a\new line after' file.txt     # GNU sed
```

### sed Practical Examples

```bash
# Remove trailing whitespace
sed -E 's/[[:space:]]+$//' file.txt

# Convert tabs to spaces
sed 's/\t/    /g' file.txt

# Add line numbers
sed = file.txt | sed 'N;s/\n/\t/'

# Extract values from key=value pairs
sed -nE 's/^DB_HOST=(.+)/\1/p' .env
# From DB_HOST=localhost → outputs "localhost"

# Remove ANSI color codes
sed -E 's/\x1b\[[0-9;]*m//g' colored_output.txt

# Wrap each line in quotes
sed 's/.*/"&"/' file.txt

# Replace content between markers
sed '/<!-- START -->/,/<!-- END -->/c\<!-- START -->\nNew Content\n<!-- END -->' index.html
```

---

## Common Mistakes and Gotchas

### 1. Forgetting to Escape Dots

```text
Wrong:   192.168.0.1     → matches "192x168y0z1" too
Right:   192\.168\.0\.1  → matches only "192.168.0.1"
```

The dot `.` matches **any** character. Always escape it when matching literal dots in IPs, URLs, filenames.

### 2. Greedy Matching Surprises

```text
Wrong:   <.+>
Text:    <b>Bold</b>
Match:   <b>Bold</b>     ← matches the entire thing

Right:   <.+?>           ← lazy, matches <b> and </b> separately
Better:  <[^>]+>         ← negated class, no backtracking needed
```

The negated character class approach `[^>]+` is actually better than lazy quantifiers because it's more explicit and avoids backtracking.

### 3. `^` and `$` Without Multiline Flag

```text
Pattern:  ^error$
Text:     "line 1\nerror\nline 3"

Without m flag: No match (^ matches start of ENTIRE string)
With m flag:    Matches "error" on line 2
```

### 4. Backslash Escaping in Strings

In most languages, you need to double-escape backslashes in regular strings:

```python
# Wrong — \b is interpreted as backspace character
pattern = "\berror\b"

# Right — raw string, backslash is preserved
pattern = r"\berror\b"
```

```javascript
// Wrong — \d is not a recognized escape, works by accident
const pattern = new RegExp("\d+");

// Right — escaped backslash
const pattern = new RegExp("\\d+");

// Best — use regex literal when possible
const pattern = /\d+/;
```

### 5. Forgetting the Global Flag

```javascript
// Only replaces the first occurrence
"aaa".replace(/a/, "b");      // "baa"

// Replaces all occurrences
"aaa".replace(/a/g, "b");     // "bbb"
```

### 6. Using `.*` When You Don't Need It

```text
Pattern:  .*error.*
```

This is almost always unnecessary. Just search for `error` — regex already searches anywhere in the string. The `.*` at both ends just wastes cycles.

The exception is when you need `^` and `$` anchors and want to match lines containing a word:

```text
Pattern:  ^.*error.*$    (with m flag — match entire lines containing "error")
```

### 7. Alternation Precedence

```text
Wrong:   ^cat|dog$
Means:   (^cat) | (dog$)  ← "cat" at start OR "dog" at end

Right:   ^(cat|dog)$
Means:   Entire string is exactly "cat" or "dog"
```

### 8. Character Class vs. Alternation

For single characters, use character classes — they're faster:

```text
Slow:    (a|b|c|d|e)
Fast:    [abcde]
```

Character classes are optimized internally (often using lookup tables), while alternation requires trying each option.

### 9. Catastrophic Backtracking Patterns

```text
Dangerous:  (a+)+
Text:       "aaaaaaaaaaaaaaaaX"
```

The regex engine tries exponentially many ways to split the "a" characters between the inner `a+` and outer `(...)+`. This can take seconds or crash the engine.

### 10. Not Anchoring Validation Patterns

```text
Wrong:   \d{5}
Text:    "ZIP: 90210-1234"
Match:   "90210"          ← also matches inside longer strings!

Right:   ^\d{5}$          ← matches ONLY a 5-digit string
```

---

## Performance Tips

### 1. Be Specific — Avoid Greedy `.*`

```text
Slow:    ".*"           → backtracks from end of string
Fast:    "[^"]*"        → stops at first quote, no backtracking
```

The negated character class `[^"]*` tells the engine exactly which characters are valid, eliminating backtracking.

### 2. Use Anchors When Possible

```text
Slow:    \d{10}         → engine tries at every position in the string
Fast:    ^\d{10}$       → engine tries once from the start, fails fast
```

### 3. Avoid Catastrophic Backtracking

Patterns with nested quantifiers on overlapping character sets can cause exponential backtracking:

```text
Dangerous patterns:
  (a+)+
  (a|a)+
  (.*a){10}
  (\w+\s*)+

Safe alternatives:
  a+           → flatten nested quantifiers
  a+           → remove redundant alternation
  [^a]*a){10}  → use negated class
  [\w\s]+      → merge into one class
```

### 4. Use Atomic Groups or Possessive Quantifiers

When supported, atomic groups `(?>...)` and possessive quantifiers `*+`, `++` prevent backtracking:

```text
Standard:    \w+\.txt         → backtracks if no match
Possessive:  \w++\.txt        → fails immediately if \w+ consumes the dot
Atomic:      (?>\w+)\.txt     → same effect as possessive
```

### 5. Put Common Alternatives First

```text
Slow:    (Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)
Fast:    (Oct|Nov|Dec|Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep)
         ← If most dates are Oct-Dec, put them first
```

In practice, the engine tries alternatives left to right. Putting common matches first reduces average comparisons.

### 6. Compile Patterns You Reuse

```python
# Slow — recompiles on every call
for line in huge_file:
    if re.search(r"\d{4}-\d{2}-\d{2}", line):
        process(line)

# Fast — compile once
date_pattern = re.compile(r"\d{4}-\d{2}-\d{2}")
for line in huge_file:
    if date_pattern.search(line):
        process(line)
```

### 7. Use Non-Capturing Groups

Capturing groups store match data. If you don't need it, use `(?:...)`:

```text
Capturing:      (https?|ftp)://     → stores "https" in group 1
Non-capturing:  (?:https?|ftp)://   → no storage overhead
```

### 8. Fail Fast with Anchors and Literal Prefixes

The engine can use literal string prefixes to quickly skip non-matching positions:

```text
Slow:    .*error_code=\d+       → tries at every position
Fast:    error_code=\d+          → can skip to "error_code" quickly
Fastest: ^.*?error_code=(\d+)   → explicit anchor, lazy quantifier
```

---

## Mini Cheat Table

A quick-reference card for the patterns you'll use every day.

### Syntax Quick Reference

| Pattern | Meaning |
|---------|---------|
| `.` | Any character (except newline) |
| `\d` | Digit `[0-9]` |
| `\w` | Word character `[a-zA-Z0-9_]` |
| `\s` | Whitespace `[ \t\r\n]` |
| `\D`, `\W`, `\S` | Negated versions of above |
| `[abc]` | Any of a, b, or c |
| `[^abc]` | Not a, b, or c |
| `[a-z]` | Range: a through z |
| `*` | 0 or more |
| `+` | 1 or more |
| `?` | 0 or 1 |
| `{n}` | Exactly n |
| `{n,m}` | Between n and m |
| `*?`, `+?` | Lazy versions |
| `^` | Start of line/string |
| `$` | End of line/string |
| `\b` | Word boundary |
| `(...)` | Capture group |
| `(?:...)` | Non-capturing group |
| `\1` | Backreference to group 1 |
| `a\|b` | a or b |
| `(?=...)` | Positive lookahead |
| `(?!...)` | Negative lookahead |
| `(?<=...)` | Positive lookbehind |
| `(?<!...)` | Negative lookbehind |

### Common Patterns Quick Reference

| What | Pattern |
|------|---------|
| Email (simple) | `[\w.+-]+@[\w-]+\.[\w.-]+` |
| URL | `https?://[\w.-]+(?:/[\w./?%&=#-]*)?` |
| IPv4 | `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}` |
| Date (ISO) | `\d{4}-\d{2}-\d{2}` |
| Time (24h) | `[01]?\d|2[0-3]):[0-5]\d` |
| Hex color | `#[0-9a-fA-F]{3,6}` |
| Phone (US) | `\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}` |
| Slug | `[a-z0-9]+(?:-[a-z0-9]+)*` |
| UUID | `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}` |
| Semver | `v?\d+\.\d+\.\d+` |
| Blank lines | `^\s*$` |
| Trailing spaces | `\s+$` |
| Repeated words | `\b(\w+)\s+\1\b` |
| HTML tag | `<[^>]+>` |

### Vim Regex Quick Reference

| PCRE | Vim (magic) | Vim (very magic `\v`) |
|------|-------------|----------------------|
| `(group)` | `\(group\)` | `(group)` |
| `one\|two` | `one\|two` | `one\|two` |
| `\d+` | `\d\+` | `\d+` |
| `\w{3,5}` | `\w\{3,5}` | `\w{3,5}` |
| `.*?` (lazy) | `.\{-}` | `.{-}` |
| `\b` | `\<` / `\>` | `<` / `>` |
| `\K` (reset match start) | `\zs` | `\zs` |

### Flags Quick Reference

| Flag | JS | Python | Vim | grep | Meaning |
|------|----|--------|-----|------|---------|
| Case-insensitive | `/i` | `re.IGNORECASE` | `\c` | `-i` | Ignore case |
| Global | `/g` | N/A (default) | `g` flag in `:s` | N/A | All matches |
| Multiline | `/m` | `re.MULTILINE` | N/A (default) | N/A | `^$` per line |
| Dotall | `/s` | `re.DOTALL` | `\_. ` | N/A | `.` matches `\n` |
| Verbose | N/A | `re.VERBOSE` | N/A | N/A | Commented regex |

---

## Resources

- [regex101.com](https://regex101.com) — Interactive regex tester with explanation (supports PCRE, JS, Python, Go)
- [regexr.com](https://regexr.com) — Another excellent visual tester
- [Vim regex documentation](https://vimhelp.org/pattern.txt.html) — Official Vim pattern reference
- [Regular-Expressions.info](https://www.regular-expressions.info) — The most comprehensive regex tutorial
- [MDN RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) — JavaScript regex reference
- [Python re docs](https://docs.python.org/3/library/re.html) — Python regex module docs

---

*Regex is a powerful tool, but remember: if you can solve the problem with a simple string method (includes, startsWith, endsWith, split), do that instead. Reach for regex when you need pattern matching, not just string matching.*
