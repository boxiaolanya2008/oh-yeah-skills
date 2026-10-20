# Code Authoring Rules

These apply to any reply that contains code meant to be saved — a new file, a patch, a snippet the user will paste. They are **independent of the reasoning mode**: a one-line fix delivered in Mode A still follows them.

Four rules. The first two are about what the code says, the last two about how it looks. All four are language-agnostic in shape; only the surface syntax varies.

---

## Rule 1 — Documentation comments, never bare comments

Every symbol another person can call, import, or configure carries a **documentation comment**: modules, classes, functions, methods, exported constants, type definitions, schemas.

The *syntax* follows the host language's documentation tool. The *shape* does not — one summary line, then a blank line, then parameters, return value, and failure modes, in that order. A reader who has the signature but not the call site should not need to open the body.

| Language | Doc comment form | Tag convention |
|----------|------------------|----------------|
| JavaScript / TypeScript | `/** ... */` | JSDoc: `@param`, `@returns`, `@throws`, `@example` |
| Java / Kotlin | `/** ... */` | Javadoc / KDoc |
| C / C++ | `/** ... */` or `///` | Doxygen: `@param`, `@return` |
| C# | `/// <summary>…</summary>` | XML documentation |
| Go | `// Name does …` directly above the declaration | Must begin with the identifier, per Go convention |
| Python | `"""…"""` immediately inside the definition | PEP 257; pick Google or NumPy style per repo and hold it |
| Rust | `///` | rustdoc; Markdown inside is rendered |
| Swift | `///` | `- Parameter name:` / `- Returns:` |
| Ruby | `#` block above the method | YARD: `@param`, `@return` |
| Shell | `#` block above the function | Include a one-line usage string |
| SQL | `/* … */` above the statement or view | |
| PHP | `/** ... */` | PHPDoc |

### Shape

```
/**
 * Retry an async operation with exponential backoff.
 *
 * The operation must be idempotent: a retry may run it more than once.
 *
 * @param {Function} fn - Operation to invoke.
 * @param {number} [attempts=3] - Total attempts, including the first.
 * @returns {Promise<*>} The first successful result.
 * @throws {Error} The last error, once every attempt has failed.
 */
async function retry(fn, attempts = 3) { … }
```

### What belongs in it

- **Why it exists and when to call it.** The signature already states what it accepts.
- **Preconditions the type system cannot express** — ordering, exclusivity, non-empty, units.
- **Side effects** — mutation, I/O, global state, transaction boundaries.
- **Failure modes** — what throws, what returns null, what degrades silently.

### What stays out

- Implementation narration ("loops over the array", "calls the helper").
- History ("changed in v2", "replaces the old approach").
- Anything the signature already says.

### The anti-pattern

```js
// retry
function retry(fn, attempts = 3) { … }
```

`retry` restates the name. A doc comment that only restates the name is worse than none: it occupies the slot where the real information was supposed to go, and it trains readers to skip the slot.

**Bare `//` and `#` are for implementation notes only** — a non-obvious constant, a workaround with a link to the issue, a warning about ordering inside a function body. They are never the sole documentation of a public symbol.

---

## Rule 2 — Group by kind, not by chronology

Never interleave declarations. The file is organized by what things **are**, not by the order they were written or the order they were first needed.

### Canonical order

Skip any group that does not apply. Never reorder the groups that do.

```
1. Module doc comment
2. Imports / requires
3. Constants and configuration
4. Type, interface, enum, schema definitions
5. Module-level state (variables)
6. Private helpers
7. Public functions
8. Classes and methods
9. Entry point / main
```

Within a group, order by dependency (callees before callers) or alphabetically. Pick one and hold it for the whole file.

### The three groupings, stated plainly

**Variables with variables.** All module-level state is contiguous. No function sits between two variable declarations.

```python
# wrong: a constant stranded between two functions
MAX_RETRIES = 10

def connect(): ...

TIMEOUT_S = 3

def send(): ...
```

```python
# right
MAX_RETRIES = 10
TIMEOUT_S = 3

def connect(): ...

def send(): ...
```

**Functions with functions.** All functions are contiguous. A helper is not parked next to the line that first calls it — it lives in the helper group, or immediately before the function that owns it, consistently.

**Statements of the same kind together, inside a function too.** Guard clauses first, then the main sequence, then cleanup. No `if` wedged into the middle of a run of assignments, no validation appearing after the side effect it was supposed to prevent.

```python
def transfer(src, dst, amount):
    # guards, contiguous
    if amount <= 0:
        raise ValueError("amount must be positive")
    if src.balance < amount:
        raise InsufficientFunds(src)

    # main sequence, contiguous
    src.balance -= amount
    dst.balance += amount

    # side effects last
    ledger.record(src, dst, amount)
```

### Why this ordering

It makes the file scannable by question. "What does this module depend on" → the top. "What can I call" → the bottom half. "What is the mutable state" → one contiguous block. Interleaved declarations force a full read to answer any of those.

---

## Rule 3 — Consistent visual form

Blank lines are structure, not decoration. They encode the group boundaries from Rule 2, so they must be applied uniformly or not at all.

| Element | Rule |
|---------|------|
| Between top-level declarations | Two blank lines (one where the language's formatter enforces one) |
| Between logical blocks inside a function | One blank line |
| Immediately inside a block's braces | No blank line |
| Statements | One per line, one declaration per statement |
| Whitespace | No trailing whitespace; never mix tabs and spaces |
| Line length | Cap at the language default — 80 for Go/Rust/Python, 100 for JS/TS — and break at argument boundaries, never mid-expression |
| Alignment | Align runs of related assignments and table-like literals; never align unrelated lines |

Alignment is the one place where "looks good" has a mechanical test: **if two lines are aligned, changing one of them should not require realigning the other.** If it does, they were not related and should not have been aligned.

---

## Rule 4 — Let the formatter win

If the project has a formatter configuration — `.prettierrc`, `gofmt`, `black`, `rustfmt`, `clang-format`, `.editorconfig` — its output **is** the definition of "beautiful". Do not hand-format against it, and do not impose a different style because it reads better to you.

Two consequences that are easy to get wrong:

- **Never reformat lines you did not change.** A diff that touches unrelated whitespace hides the real change and costs the reviewer more than the fix saved.
- **Match the surrounding file, not the ideal.** If the existing code uses single quotes, two-space indent, and no trailing commas, new code uses single quotes, two-space indent, and no trailing commas — even if Rule 3 would have chosen otherwise. Consistency inside a file outranks consistency with a style guide.

If there is no configuration and no surrounding code, Rule 3 is the default.

---

## Before and After

The same module, written twice.

### Before

```python
import requests, json
TIMEOUT = 3
def fetch(url):
    # get data
    r = requests.get(url, timeout=TIMEOUT)
    return json.loads(r.text)
RETRIES = 2
class Client:
    def __init__(self, base):
        self.base = base
    def get(self, path):
        return fetch(self.base + path)
def _backoff(n):
    return 2 ** n
```

Six problems, all from the four rules: imports on one line with a stray constant, a bare comment standing in for documentation, `RETRIES` stranded between a function and a class, a class between functions, no doc comments anywhere, and `_backoff` defined after its conceptual home.

### After

```python
"""HTTP client for the internal inventory service.

Exposes a thin synchronous wrapper over `requests`. All calls honour the
module-level timeout and retry policy.
"""

import json
import time

import requests

TIMEOUT_S = 3
MAX_RETRIES = 2


def _backoff(attempt):
    """Return the delay before the given retry attempt, in seconds.

    Args:
        attempt: Zero-based retry index.

    Returns:
        Delay in seconds, doubling per attempt.
    """
    return 2 ** attempt


def fetch(url):
    """Fetch and decode a JSON document.

    Args:
        url: Absolute URL to request.

    Returns:
        The decoded JSON payload.

    Raises:
        requests.HTTPError: If the response status is 4xx or 5xx.
        ValueError: If the body is not valid JSON.
    """
    response = requests.get(url, timeout=TIMEOUT_S)
    response.raise_for_status()
    return json.loads(response.text)


class Client:
    """Client bound to a single service base URL.

    Attributes:
        base: Base URL prepended to every request path.
    """

    def __init__(self, base):
        """Bind the client to a base URL.

        Args:
            base: Scheme and host, with no trailing slash.
        """
        self.base = base

    def get(self, path):
        """Request a path relative to the base URL.

        Args:
            path: Path beginning with a slash.

        Returns:
            The decoded JSON payload.
        """
        return fetch(self.base + path)
```

The diff is larger, but every line of it is answerable: the imports are sorted and one per line, the constants are contiguous, the helper precedes the functions that use it, the class is last, and every public symbol says something the signature does not.

---

## Checklist

Run this before returning any code.

- [ ] Every public symbol has a doc comment in the host language's convention.
- [ ] No doc comment merely restates the name.
- [ ] Doc comments state preconditions, side effects, or failure modes — not implementation steps.
- [ ] Imports → constants → types → state → private helpers → public functions → entry.
- [ ] No variable stranded between two functions.
- [ ] No helper parked next to its first call site.
- [ ] Guard clauses precede the main sequence; side effects come last.
- [ ] Two blank lines between declarations, one between logical blocks, none inside braces.
- [ ] No trailing whitespace, no mixed indentation, one statement per line.
- [ ] Formatter configuration respected; untouched lines left untouched.
