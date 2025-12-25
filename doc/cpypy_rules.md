Rules I can infer from the 4 “99% your style” modules you uploaded (treated as formatting rules, not coding/architecture rules):

## A) File layout and sectioning

1. **File starts with a standalone `#` line.**
2. **Major sections are separated by “banner blocks”**:

   * 3 lines of a long `########...` separator
   * then 1 titled separator line: `########... <TITLE>`
   * then a standalone `#`
3. **You use at least two separator lengths** (a “long” one ~99 chars and a “mid” one ~65 chars) for major vs minor sections.
4. **Imports are grouped into labeled sections** like `Standard Modules`, `My Modules`, etc., each using the banner pattern.
5. **Separating logical parts with long `#` banners is the user’s responsibility** (manual structure; formatter should not invent new sections).

## B) Imports formatting

6. **Import lines are column-aligned using extra spaces**, e.g. `import numpy  as np`, `from enum   import Enum`.
7. **Blank lines exist between import sub-groups** (stdlib vs third-party vs internal), plus the banner headers.
8. **A standalone `#` often closes the import region** (import block terminator).

## C) Class / function header spacing

9. **Always a blank line after `class X:`** before the first content.
10. **Usually a blank line after `def foo(...):`** before the first statement.

    * Exception: when a docstring starts immediately, you may place the docstring right away.

## D) Type-hint spacing rules

11. **Function parameter annotations use no space after `:`**

    * `def f(x:int, y:pd.DataFrame) -> ...:`
12. **Default argument values have no spaces around `=`**

    * `ddof:int=1`, not `ddof: int = 1`
13. **Return type arrow is spaced**: `) -> Type:`
14. **Class/dataclass attribute annotations use spaces around `:` and are aligned in columns**

    * `ser_open      : pd.Series`

## E) Alignment rules (your “columns” style)

15. **Assignments are column-aligned** using multiple spaces before `=`:

    * `meaned    = ...`
16. **Object attribute assignments confirm the same alignment**:

    * `self.param_ruler                 = ...`
17. **Keyword arguments in function calls are aligned and spaced around `=`** (PEP8-opposed but consistent in your style):

    * `labels       = ...`
18. **Dict entries often align keys/colons/values** using extra spaces:

    * `"TF_5m"  : 5 * 60,`

## F) Control-flow formatting

19. **Parentheses around `if` conditions**:

    * `if ( ... ):`
20. **Parentheses around `elif` conditions**:

    * `elif ( ... ):`
21. **Parentheses around `while` conditions**:

    * `while ( ... ):`
22. **No parentheses around `for` headers** (standard `for x in y:`).
23. **Inside boolean-heavy conditions, you often use double-spacing around boolean operators**:

    * `  and  `, `  or  ` (especially in long expressions)

## G) Return / expression wrapping

24. **Return values are wrapped in parentheses**:

    * `return (value)`
25. **You frequently wrap right-hand-side expressions in parentheses** even when not required:

    * `ser_timestamp = (df['timestamp'])`

## H) Multi-line signatures and calls

26. **Multi-line `def` signatures indent parameters to a fixed visual column** (not the default hanging indent), keeping a clean vertical edge.
27. **Multi-line calls put one argument per line**, aligned, with commas per line; trailing-comma usage varies (positional arg lists sometimes keep a trailing comma before `)`).

## I) Block-end markers

28. **Standalone `#` lines act as explicit block terminators**, used heavily after:

    * `if/elif/else` blocks
    * `for/while` blocks
    * `try/except/finally` blocks
    * `def/class` blocks (often at the end)
29. **The block-end `#` keeps the same indentation level** as the block it closes.

## J) Line length

30. **No strict PEP8 line-length discipline** (you allow very long lines when you choose, especially in lambdas/conditions).

If you want, I can convert this into a “machine-readable” style spec (a checklist + regex-able rules), and also classify each rule as:

* “autopep8 impossible”
* “autopep8 maybe via ignores/args”
* “needs custom formatter / AST-based rewriter”
