---
name: python-code-format
description: Use when you are generating more than 15 lines of code, writing Python functions/classes/modules, importing modules, generating .py files or producing any Python code, or when the user says "注释" (comments) / "规范" (conventions) and the code being edited is Python code
---

# Python Code Format Convention Skill

When generating any Python code, the following format must be strictly followed. Core principle: **anyone who sees the code should immediately understand what it does without having to think**. The biggest cost of code is not writing it, but reading, understanding, and modifying it repeatedly afterwards; the essence of a comment is to save the reader the time of "inferring the code's intent", not to restate the syntax. If a plain solution works, do not introduce unnecessary complexity.

## Module File Header

The beginning of the file must have the following header. Replace the `{}` placeholders according to context (e.g., `{date}` → current date):

```python
# -*- coding: utf-8 -*-
# @Time    : {date, format YYYY-mm-DD}
# @Author  : {author name}
# @File    : {file name (with extension)}
# @Software: {IDE name, e.g., VSCode, PyCharm}
# @Description: {what this code does}
```

When filling in the author name, ask the user what the author name should be, offering these choices:

- The AI model's name
- A custom name provided by the user

Then replace the `{author name}` placeholder according to the user's choice.

## Blank Line Rules

- Between the file header and subsequent code: 1 line; between the end of the import section and subsequent code: 2 lines
- Between top-level functions/classes: 2 lines; between methods inside a class: 1 line (PEP 8)
- Logical blocks inside a function may be separated by 1 blank line; do not overuse

## Naming Rules

- **Utils utility modules**: PascalCase, e.g., `DateTimeUtils.py`; **other functional modules**: snake_case, e.g., `business_model.py`
- **Class names** PascalCase; **functions/methods/variables** snake_case
- **Boolean variables**: `is_`/`has_`/`can_`/`should_` prefixes (✅ `is_active` ❌ `active`)
- **Private members**: single underscore `_name`; double underscore only when needed to avoid attribute conflicts with subclasses
- **Collection variables**: use plurals (✅ `users` ❌ `user_list`; a type suffix is redundant, unless multiple collection types must be distinguished in the same scope)
- **Constants**: UPPER_SNAKE_CASE, defined together after imports and before functions/classes; never scatter magic numbers inside functions—any literal used more than once must be extracted into a constant; when there are more than 10 constants, extract them into a standalone `constants.py`/`config.py`

```python
# ------------ Constants ------------
DEFAULT_TIMEOUT_SECONDS = 30 # Third-party API docs recommend a timeout of no more than 30 seconds
```

## Enums

When a fixed set of values appears repeatedly, `enum.Enum` must be used instead of string/integer constants. Class name PascalCase, members UPPER_SNAKE_CASE; use `auto()` when the concrete value doesn't matter; members need inline comments:

```python
class TaskStatus(Enum):
    """Task status enum"""
    PENDING = auto() # Waiting to run
    RUNNING = auto() # Currently running
```

## Import Rules

- **All imports are grouped by project functional module**; group names are customized per the project's actual features (e.g., `common`, `database`, `api-connector`, `business`, `tools`)—do not mechanically apply fixed names
- Group comment format: `# ------------ group name ------------`, with one blank line between groups
- **Within a group, order by visual length**: shorter total line length goes higher (so `import xxx` usually comes before `from ... import xxx`); wrapped multi-line import blocks count as the longest and go last within the group
- **This ordering only applies within a group**: different groups are not compared against or affected by each other
- Prefer `from ... import` whenever possible; when importing many objects, wrap lines and align
- One blank line between the end of the import section and subsequent code

Import example:

```python
# ------------ common ------------
import os
import sys
from datetime import datetime
from typing import (
    Any,
    Dict,
    Optional
)

# ------------ tools ------------
from utils import DateTimeUtils
from utils import unicode_normalizer
```

## Type Annotations

- Parameters and return values of public functions/methods **must** be annotated; private functions are recommended to be annotated
- Class attributes/instance attributes should preferably declare their types using annotation syntax (rather than only describing them in the docstring)
- Use `typing`'s `Optional`/`Union`/`Any` for complex types; for circular references use `from __future__ import annotations` or string forward references

## Function Docstrings

Every function must have a docstring, in this order: one-line summary → detailed description (optional) → `Args:` → `Returns:` → `Raises:` → `Example:`

```python
def func_name(arg: str) -> int:
    """What this function does

    Args:
        arg (str): What this parameter does

    Returns:
        int: What is returned

    Raises:
        ValueError: Raised when arg is an empty string

    Example:
        >>> func_name("test")
        1
    """
```

- `Raises:` is mandatory when the function explicitly `raise`s; exceptions thrown only by built-in functions/libraries by default may be omitted
- `Example:` must provide runnable code and its expected output
- Exceptions: `@property` skips `Args:`/`Raises:`; `__init__` skips `Returns:`; for `main()`, `Returns:`/`Raises:`/`Example:` are optional

## Class Definitions

- Class variables and instance variables need comments explaining their purpose
- Class methods need complete docstrings just like regular functions

## Error Handling

- Prefer built-in exceptions (`ValueError`, `TypeError`, etc.); define custom exception classes only when business logic must branch by exception type
- When re-raising after catching, you must use `raise ... from e` to preserve the exception chain
- Only catch the specific exception types you expect; bare `except:` or broad `except Exception:` is **forbidden** (except as a last-resort catch-all at the outermost entry point)
- Resources such as files/network/database **must** be managed with `with`, not manually closed with try/finally; custom resource classes implement `__enter__`/`__exit__`; simple cases may use `contextlib.contextmanager`

## String Conventions

- **Double quotes throughout** (including dict keys and f-strings); single quotes may wrap content only when the content itself contains double quotes
- **Prefer f-strings**; use `str.format()` when the same template is reused in multiple places; **`%` formatting is forbidden**
- Do not put complex expressions inside f-strings; assign to a variable first, then reference it

## Region Comments

When code exceeds 8-10 lines, use paired region comments, with the dashes fixed at 28 `-`:

```python
# region ---------------------------- What this block does ----------------------------
...
# endregion ---------------------------- What this block does ----------------------------
```

## Important/Warning Comments

For code that must be modified with caution, has major impact, or is a necessary evil, wrap it with paired `===` separator lines. Keep single lines from getting too long; wrap text as needed:

```python
# ==========================================================================================================
# Describe how the following steps work; wrap the text when one line gets too long
# ==========================================================================================================
func_name(arg=3)
# ==========================================================================================================
```

## Comment Punctuation

Comments must use **English half-width punctuation** throughout (`()` `,` `:` `;` `.` `!` `?`); Chinese full-width punctuation is forbidden. A space must follow punctuation, and spaces go between English parentheses and surrounding text.

- ✅ `# 处理数据 (支持多种格式) 并返回结果` (half-width parentheses with spaces)
- ❌ `# 处理数据（支持多种格式）并返回结果` (full-width parentheses)

## Inline Comments

- Format: `code # comment text` (one space before and after `#`); using lots of spaces to align multiple inline comments is **forbidden**
- Delete code that is no longer used; do not keep it around as comments

Four scenarios that **must be commented** (explain "why", not "what"):

- Non-obvious literals (magic numbers, special strings, regexes): ✅ `timeout = 30 # Third-party API docs recommend a timeout of no more than 30 seconds` ❌ `# Set timeout to 30 seconds`
- Operations with side effects/external state dependencies (file I/O, network, database, global variable modification)
- Core algorithm steps / non-intuitive constructs: ✅ `idx = max(0, min(idx, len(arr) - 1)) # Clamp the index to the valid range to prevent out-of-bounds`
- Ambiguous type conversions or operations (implicit conversion, bitwise operations, precision-sensitive floats)

**Should NOT be commented**: self-explanatory naming (❌ `user_name = "Alice" # Set the user name`), pure assembly/pass-through calls, standard getters/setters/properties.

**Density**: at least one comment per 3-8 lines of code, but not on every line; the test is whether someone unfamiliar with the module can grasp the intent of each block within 3 seconds; spread comments across key points rather than piling them in one place.

## `__all__` and Entry-Point Conventions

- Modules used via `from xxx import *` must define `__all__` (alphabetical order, public symbols only); it may be omitted when no such usage exists
- Directly executable files must end with `if __name__ == "__main__":`, whose block only calls `main()`; entry logic is encapsulated in `main()`
- Command-line arguments are parsed with `argparse` inside `main()`; do not expose bare `sys.argv`

## File Encoding

- Keep the `# -*- coding: utf-8 -*-` declaration (backward compatibility and editor recognition)
- When reading/writing external files, **explicitly specify `encoding="utf-8"`**; do not rely on the system default encoding
- Use `utf-8-sig` for CSVs exported from Excel to strip the BOM; when compatibility with legacy encodings such as GBK is needed, add a comment explaining why

## Function Length and Complexity

- A single function should be ≤50 lines (excluding docstring/blank lines); beyond that, consider splitting into single-responsibility sub-functions
- Cyclomatic complexity should be ≤10; refactor when `if/for/while` nesting exceeds 3 levels
- Not a hard rule: it may be exceeded when splitting would break logical cohesion, but a region comment must explain why

## Occam's Razor Principle

Prefer the plainest, most intuitive solution unless there is clear performance data or correctness evidence. Complexity should be introduced only after it is "proven necessary", not laid down in advance "in case it's needed someday". Dimensions for judging "simpler": fewer lines of code, less abstraction, fewer dependencies, shorter execution paths.

- **Data structures**: if built-in types solve it, don't pull in third-party libraries (❌ importing pandas to get the max of one CSV column; ✅ stdlib `csv` + a generator expression)
- **Implementation path**: choose the most straightforward logic that can be understood at a glance (❌ `reduce(add, filter(lambda x: x > 0, nums), 0)`; ✅ `sum(x for x in nums if x > 0)`)
- **Abstraction level**: interfaces, base classes, and strategy patterns with only one implementation/one caller are unnecessary noise; introduce them when a second real use case appears
- **Function splitting**: don't split a sub-function that is called from only one place and is ≤3 lines of logic; before splitting ask two questions—does it have an independent, meaningful name? will it be reused? If both answers are "no", leave it where it is

## Code Quality Checks

- Tooling combo: **Ruff** (linter + formatter, replaces flake8/isort/black) + **Mypy/Pyright** (type checking); ensure no new errors before committing
- Recommended `pyproject.toml` configuration: ruff `line-length = 120`, `select = ["E", "F", "I", "N", "W", "UP"]`, `quote-style = "double"`; mypy `strict = true`

## Execution Requirements and Comprehensive Example

Any generated code must **fully apply** all of the formats above; comments should be easy to understand and sufficient in number; avoid meaningless blank lines but keep grouping blank lines; use region comments to lower the cost of understanding complex code. Illustration of the combined effect (module named `CSVUtils.py`):

```python
# -*- coding: utf-8 -*-
# @Time    : 2026-04-26
# @Author  : AI Assistant
# @File    : CSVUtils.py
# @Software: VSCode
# @Description: Utility module providing CSV file reading, returning a list of dicts

# ------------ common ------------
import os
import csv
from typing import (
    Dict,
    List
)


def read_csv(file_path: str, delimiter: str = ",") -> List[Dict[str, str]]:
    """Read a CSV file and return a list of dicts, converting each row into a dict

    Args:
        file_path (str): Full path to the CSV file
        delimiter (str): Delimiter used by the file, comma by default

    Returns:
        List[Dict[str, str]]: List of dicts, one per row, keyed by column names

    Raises:
        FileNotFoundError: Raised when no file exists at the given path
        RuntimeError: Raised when CSV parsing fails

    Example:
        >>> read_csv("example.csv")
        [{'姓名': '张三', '年龄': '25'}]
    """
    # region ---------------------------- Parameter validation and path handling ----------------------------
    file_path = os.path.normpath(file_path) # Remove '.' and '..' from the path to avoid isfile misjudging
    if not os.path.isfile(file_path): # isfile rather than exists, to rule out a directory being passed in
        raise FileNotFoundError(f"CSV file not found: {file_path}")
    # endregion ---------------------------- Parameter validation and path handling ----------------------------

    # region ---------------------------- Open the file and read its contents ----------------------------
    rows_data = [] # List holding the final result
    try:
        # utf-8-sig automatically skips the BOM, preventing a \ufeff prefix in column names
        with open(file_path, mode="r", encoding="utf-8-sig") as f:
            for row in csv.DictReader(f, delimiter=delimiter):
                rows_data.append(dict(row)) # Copy into a plain dict to avoid external mutation of the reader cache
    except csv.Error as e:
        raise RuntimeError(f"CSV parsing failed: {e}") from e
    # endregion ---------------------------- Open the file and read its contents ----------------------------
    return rows_data
```
