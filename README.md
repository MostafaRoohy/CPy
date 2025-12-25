# CPyPy

**Project_CPyPy** is a Python formatter that targets a **specific non-PEP8 personal style** optimized for fast visual parsing: aligned columns, explicit block terminators, and strong visual separation of logical parts.

The goal is not “standard formatting”. The goal is to make code **visually structured** so **I** can **feel** the relationships between lines before even read the details.

---

## Why this style exists

### What CPyPy optimizes for
- **Scanability**: see variable groups and dependencies immediately.
- **Stable shape**: diffs stay readable because alignment is consistent.
- **Explicit structure**: blocks are visually closed (`#`), returns are visually boxed (`return (...)`).
- **Separation of concerns**: major logical parts are manually separated by banner lines.

---

## Style showcase (PEP8 vs CPyPy)

### Example 1 — assignments and derived variables

**Typical PEP8**
```py
def compute_features(prices: list[float], window: int) -> dict[str, float]:
    n = len(prices)
    total = 0.0
    count = 0

    for p in prices:
        total += p
        count += 1

    mean = total / count
    last = prices[-1]
    delta = last - mean
    ratio = last / (mean + 1e-9)
    score = (delta * ratio) / (1 + abs(delta))

    return {
        "n": n,
        "mean": mean,
        "last": last,
        "delta": delta,
        "ratio": ratio,
        "score": score,
    }
```

**CPyPy style**
```py
def compute_features(prices:list[float], window:int) -> dict[str,float]:

    n     = len(prices)
    total = 0.0
    count = 0

    for p in prices:

        total += p
        count += 1
    #

    mean  = total/count
    last  = prices[-1]
    delta = last-mean
    ratio = last/(mean+1e-9)
    score = (delta*ratio)/(1+abs(delta))

    return ({
        "n"    : n,
        "mean" : mean,
        "last" : last,
        "delta": delta,
        "ratio": ratio,
        "score": score,
    })
#
```

---

### Example 2 — long call signatures (alignment makes parameters scannable)

**Typical PEP8**

```py
result = backtest(
    candles=df,
    strategy=strategy,
    fee_rate=0.0006,
    slippage=0.0002,
    max_positions=1,
    allow_short=False,
    start_ts=start_ts,
    end_ts=end_ts,
    warmup=500,
)
```

**CPyPy style**

```py
result = backtest(
    candles       = df,
    strategy      = strategy,
    fee_rate      = 0.0006,
    slippage      = 0.0002,
    max_positions = 1,
    allow_short   = False,
    start_ts      = start_ts,
    end_ts        = end_ts,
    warmup        = 500,
)
```

---

### Example 3 — conditional logic (boolean connectors become visible structure)

**Typical PEP8**

```py
if x > 0 and x < 10 and y != 0 and (mode == "fast" or mode == "safe"):
    risk = (x / y) * scale
    return risk
return 0.0
```

**CPyPy style**

```py
if (x > 0  and  x < 10  and  y != 0  and  (mode == "fast"  or  mode == "safe")):

    risk = (x/y) * scale
    return (risk)
#

return (0.0)
#
```

---

### Example 4 — multiple related computations (alignment shows grouping + symmetry)

**Typical PEP8**

```py
open_ = row["open"]
high = row["high"]
low = row["low"]
close = row["close"]

body = abs(close - open_)
upper_wick = high - max(open_, close)
lower_wick = min(open_, close) - low

is_bull = close > open_
is_bear = close < open_
```

**CPyPy style**

```py
open_ = row["open"]
high  = row["high"]
low   = row["low"]
close = row["close"]

body       = abs(close-open_)
upper_wick = high-max(open_, close)
lower_wick = min(open_, close)-low

is_bull = (close > open_)
is_bear = (close < open_)
#
```

---

### Example 5 — imports (aligned `from … import …` improves scanning)

**Typical PEP8**

```py
from dataclasses import dataclass
from pathlib import Path
from typing import Any, Callable, Iterable, Sequence
```

**CPyPy style**

```py
from dataclasses import dataclass
from pathlib     import Path
from typing      import Any, Callable, Iterable, Sequence
#
```

---

### Example 6 — dict “tables” (colon alignment turns it into a readable table)

**Typical PEP8**

```py
TF_TO_SECONDS = {
    "TF_1s": 1,
    "TF_5s": 5,
    "TF_10s": 10,
    "TF_1m": 60,
    "TF_5m": 5 * 60,
    "TF_15m": 15 * 60,
    "TF_1h": 60 * 60,
}
```

**CPyPy style**

```py
TF_TO_SECONDS = ({
    "TF_1s"   : 1,
    "TF_5s"   : 5,
    "TF_10s"  : 10,
    "TF_1m"   : 60,
    "TF_5m"   : 5 * 60,
    "TF_15m"  : 15 * 60,
    "TF_1h"   : 60 * 60,
})
#
```

---

## Usage

### Format

```bash
cpypy format path/to/file_or_dir
```

### Check

```bash
cpypy check path/to/file_or_dir
```

---

## Configuration

CPyPy reads configuration from `pyproject.toml`:

```toml
[tool.cpypy]
enabled_rules = [
  "if_parentheses",
  "return_parentheses",
  "block_end_marker",
  "align_assignments",
]
disabled_rules = [
  "boolean_spacing"
]
```

---

## Rules (will update more and more)

* `if_parentheses`
  Enforce `if ( ... ):`, `elif ( ... ):`, `while ( ... ):`

* `boolean_spacing`
  Enforce double spacing around `and/or` inside conditions: `  and  `, `  or  `

* `return_parentheses`
  Enforce `return (value)` (does not change bare `return`)

* `block_end_marker`
  Enforce standalone `#` terminators (indent-aware)

* `align_assignments`
  Align `=` within contiguous assignment groups

More rules exist/planned:

* `align_imports`, `align_dict_colons`, `typehint_spacing`, etc.

More rule descriptions:

* `doc/cpypy_rules.md`

---

## Documentation

* `doc/project_structure.md` — repository layout and responsibilities
* `doc/vscode_integration.md` — planned editor integration

---
