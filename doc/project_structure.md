## Repository layout

Project_CPyPy/

```
pyproject.toml
README.md

cpypy/

    __init__.py
    __main__.py
    cli.py
    config.py

    engine/
        __init__.py
        diff.py
        formatter.py
        pipeline.py

    model/
        __init__.py
        block.py
        line.py

    render/
        __init__.py
        renderer.py

    rule/
        __init__.py
        base.py
        if_parentheses.py
        boolean_spacing.py
        return_parentheses.py
        block_end_marker.py
        align_imports.py
        align_assignments.py
        align_dict_colons.py
        typehint_spacing.py

    util/
        __init__.py
        text.py

doc/
    cpypy_rules.md
    vscode_integration.md
    project_structure.md

example/

script/

test/
    fixture/
        in/
        out/
    test_rule/
        test_if_parentheses.py
        test_boolean_spacing.py
        test_return_parentheses.py
        test_block_end_marker.py
        test_alignment.py
```

---

## Top-level files

### `pyproject.toml`
Project metadata and packaging configuration. Also hosts formatter configuration under a `[tool.cpypy]` section (later).

### `README.md`
Quickstart, installation, CLI usage, and basic examples.

---

## `cpypy/` package

This is the formatter implementation.

### `cpypy/__init__.py`
Package metadata and public exports.

### `cpypy/__main__.py`
Enables:
```bash
python -m cpypy ...
```

Usually calls the CLI entrypoint.

### `cpypy/cli.py`

Command-line interface:

* `cpypy format <path>`
* `cpypy check <path>`
* `cpypy diff <path>`

Responsibilities:

* expand file/directory targets (recursive `*.py`)
* handle stdin/stdout (optional)
* decide exit codes (important for CI)
* delegate actual formatting to `engine/formatter.py`

### `cpypy/config.py`

Loads formatter configuration (initially from defaults; later from `pyproject.toml`).
Defines:

* enabled rules list / rule order
* per-rule parameters (when needed)

---

## `cpypy/engine/`

Execution orchestration: format/check/diff workflows, not rule logic.

### `engine/formatter.py`

High-level public API:

* `format_text(text) -> formatted_text`
* `format_file(path) -> formatted_text`
* format multiple paths and return results (`changed`, etc.)

This file is the bridge between CLI and the formatting pipeline.

### `engine/pipeline.py`

The deterministic formatting pipeline:

1. parse text into internal model (`model/line.py`, optionally `model/block.py`)
2. apply rules in a stable order (`rule/`)
3. render back to text (`render/renderer.py`)

Pipeline ordering is critical, because some rules depend on earlier normalization.

### `engine/diff.py`

Produces diffs for `cpypy diff` (unified diff output).
Used in CI-friendly workflows.

---

## `cpypy/model/`

Internal representation used by rules. Keep this small and stable.

### `model/line.py`

Represents a source line with useful structure, typically:

* raw text
* indentation
* content (without indent)
* classification (blank/comment/code) if needed later

Most early rules can be line-based.

### `model/block.py`

Optional block/indent stack model.
Used for indentation-aware rules like:

* standalone `#` block-end marker insertion/normalization
* detecting block boundaries reliably

---

## `cpypy/render/`

Converts the internal model back into final text.

### `render/renderer.py`

Renders `Line` (and optional `Block`) structures to a final string.
Goals:

* deterministic output
* preserve newline style consistently (or normalize by policy)
* ensure idempotence (`format(format(x)) == format(x)`)

---

## `cpypy/rule/`

Rule implementations (one module per rule). Each rule should:

* be deterministic and idempotent
* avoid unsafe transformations unless explicitly configured
* touch only what it owns (minimal diffs)

### `rule/base.py`

Defines:

* `Rule` interface / protocol
* context object (config, feature flags, etc.)
* common helpers shared by rules

### Implemented rule modules

* `if_parentheses.py`
  Enforces: `if ( ... ):`, `elif ( ... ):`, `while ( ... ):`

* `boolean_spacing.py`
  Enforces double spacing around boolean operators inside conditions: `  and  `, `  or  `

* `return_parentheses.py`
  Enforces: `return (value)` (must not touch `return` with no value)

* `block_end_marker.py`
  Enforces standalone `#` block terminators (indent-aware)

* `align_imports.py`
  Aligns import columns (e.g., `import X  as Y`, `from X   import Y`)

* `align_assignments.py`
  Aligns `=` across contiguous assignment groups

* `align_dict_colons.py`
  Aligns dict literal colons in simple/line-based cases

* `typehint_spacing.py`
  Enforces typehint spacing conventions (e.g., `x:int`, `ddof:int=1`, aligned attribute annotations)

---

## `cpypy/util/`

Small utilities that are not rules and not pipeline logic.

### `util/text.py`

Text helpers:

* whitespace operations
* safe string slicing helpers
* line ending helpers, etc.

---

## `doc/`

Project documentation.

* `cpypy_rules.md`
  Human description of each rule, examples, and known limitations.

* `vscode_integration.md`
  How to run CPyPy from VS Code (tasks, format-on-save, extension ideas).

* `project_structure.md`
  This file.

---

## `example/`

Small example files demonstrating:

* input style vs output style
* rule behavior edge cases
* CLI usage examples

---

## `script/`

Developer scripts:

* format whole repo
* run tests
* regenerate fixtures
* local release helpers (later)

---

## `test/`

Testing strategy is fixture-based (golden files) + targeted unit tests.

### `test/fixture/in/` and `test/fixture/out/`

Golden tests:

* each input file in `in/` has a corresponding expected formatted file in `out/`

### `test/test_rule/`

Unit tests per rule:

* `test_if_parentheses.py`
* `test_boolean_spacing.py`
* `test_return_parentheses.py`
* `test_block_end_marker.py`
* `test_alignment.py`

Minimum test contracts:

* deterministic output
* idempotence
* minimal diff (rule only touches its scope)
