---
name: polars
description: >
  How to write production-grade Polars code: reusable expression functions,
  frame functions with configurable column names, validation, lazy
  evaluation, NaN and null handling, window and group operations, and
  determinism. Use this whenever the user is writing, reviewing, refactoring
  or optimizing Polars code (pl.DataFrame, pl.LazyFrame, pl.Expr), asks how to
  structure a Polars function, how to handle required column names, or why a
  Polars pipeline is slow, eager or non-deterministic, even if they don't
  mention best practices.
---

# Polars Conventions

Verified against Polars 2.0.0. Anything marked **convention** is this user's
choice, not a Polars requirement.

## Core principles

1. **Think in expressions, not frames.** Reusable logic is a `pl.Expr`, not a
   function that takes a DataFrame and a string column name.
2. **Never leave the engine mid-pipeline.** `.to_numpy()`, `map_groups` and
   `rolling_map` with a Python function disable parallelism and optimization.
   Reach for them only when no expression exists.
3. **Stay lazy.** Library functions return a `LazyFrame` and never call
   `.collect()`. The caller collects once.
4. **Multi-column expressions replace Python loops.** `pl.col(["a", "b"])` or
   `cs.numeric()` with `.name.keep()`, `.name.suffix()`, not a list
   comprehension of `pl.col(f).alias(f)`.
5. **Use the built-ins.** `is_finite()`, `fill_nan(None)`, `clip()`,
   `over(..., order_by=)`.

## Function layers

### Layer 1: expression functions take expressions

```python
def zscore(x: pl.Expr, *, by: str | pl.Expr | Sequence[str]) -> pl.Expr:
    x = x.fill_nan(None)
    return (x - x.mean().over(by)) / x.std().over(by)
```

- Bakes in no column names, so there is nothing to assert.
- Works on several columns at once: `zscore(pl.col("a", "b"), by="date").name.suffix("_z")`.
- Inherits its output name from the input. The caller names it with
  `.alias()` or `.name.*`. Never hardcode an output name inside it.
- Composes by nesting: `zscore(-pl.col("mkt_cap").log(), by="date")`.

### Layer 2: frame functions take names as keyword-only parameters

```python
def factor_sze(
    df: pl.DataFrame | pl.LazyFrame,
    *,
    date: str = "date",
    symbol: str = "symbol",
    mkt_cap: str = "market_cap",
    out: str = "sze_score",
) -> pl.LazyFrame:
    _require(df, date, symbol, mkt_cap)
    return df.lazy().select(
        pl.col(date, symbol),
        zscore(-pl.col(mkt_cap).log(), by=date).alias(out),
    )
```

- Defaults give the canonical schema. Users with other names override them.
- Keyword-only (`*`) prevents positional mix-ups between several strings.
- More than about four names: group them in a `@dataclass(frozen=True)` and
  pass `cols=Cols()`.
- Always return `LazyFrame`, whatever the input type (convention).
- A function that preserves its input type can use
  `TypeVar("FrameT", pl.DataFrame, pl.LazyFrame)` instead of a union return.

### Layer 3: validate once, at the public boundary

```python
def _require(df: pl.DataFrame | pl.LazyFrame, *cols: str) -> None:
    schema = df.collect_schema()
    if missing := [c for c in cols if c not in schema]:
        raise ValueError(f"missing columns {missing}; have {schema.names()}")
```

- `collect_schema()` runs no data and works on both frame types.
- Raise `ValueError`, not `AssertionError`.
- Validate in the public entry point only, not in every internal helper.
- For a function made only of `pl.col` references, Polars' own
  `ColumnNotFoundError` already lists the valid columns. Validate when you
  want the failure at call time, or when you also check dtypes.

## Rules

| Do | Don't | Why |
|---|---|---|
| `pl.col(cols).cast(pl.Float64)` | `[pl.col(f).cast(float).alias(f) for f in cols]` | One expression, one plan node |
| `x.is_finite()` | `x.abs() == np.inf`, `x.cast(str) == "NaN"` | Clear and type-safe. NaN and inf both fail `is_finite` |
| `x.is_nan()` | `x == np.nan` | Works in Polars (NaN equals NaN) but reads like a bug |
| `x.fill_nan(None)` then `.mean()`, `.std()` | `x.drop_nulls().drop_nans().mean()` | Aggregations skip nulls natively |
| `c.clip(c.quantile(p, "linear").over(g), c.quantile(1 - p, "linear").over(g))` | `group_by().map_groups()` with numpy | About 35x faster on 1M rows and preserves row order |
| `x.forward_fill().over(g, order_by=t)` | `.sort(t)` then `.over(g)` | `over(order_by=)` states the ordering the window needs |
| `-pl.col("x").log()` | `pl.col("x").log().alias("y") * -1` | Alias after the arithmetic, not inside it |
| `df.collect_schema().names()` | `lazy_df.columns` | `.columns` on a LazyFrame resolves the schema and warns |
| `df.partition_by("date", maintain_order=True)` | `df.filter(pl.col("date") == dt)` in a loop | One pass instead of one scan per group |
| `if cond: raise ValueError(...)` | `assert cond` | `assert` is stripped under `python -O` |

## NaN, null and inf

- Polars distinguishes `NaN` (a float value) from `null` (missing). Decide
  which one your function treats as missing and say so in the docstring.
- `fill_nan(None)` turns NaN into null. The output then has null where the
  input had NaN. If downstream code relies on NaN being preserved, keep it
  (for example a normalization that deliberately passes NaN through).
- Forward fill only fills null. Convert NaN and inf to null first.
- `when/then/otherwise` with a null condition falls through to `otherwise`, so
  an explicit `is_null()` branch is redundant.

## Determinism

- `group_by().map_groups()` returns rows in a nondeterministic order within
  groups in Polars 2.0. A test that sorts only on the group key can flake.
  Prefer an expression with `.over()`, which preserves input row order.
- `sort` is not stable by default. Sort on a total order (`["group", "value"]`)
  or pass `maintain_order=True` where order matters.
- In tests, always sort on a unique key before `assert_frame_equal`.

## Things to avoid

- Hardcoded `pl.col("date")` inside reusable functions.
- `try/except AttributeError` or `ColumnNotFoundError` around a lazy chain.
  Lazy plans fail at `collect()`, so the `try` rarely wraps the real error and
  the rewrap hides Polars' message.
- `.collect()` followed by `.lazy()` inside a library function.
- `@pl.api.register_expr_namespace` unless shipping a library with a large API.
- Python UDFs (`map_elements`, `map_batches`, `rolling_map`) in a hot path.
  When one is unavoidable, keep it isolated and note why.

## Review checklist

1. Does each reusable function take and return `pl.Expr`, or take names as
   keyword-only parameters with defaults?
2. Is there any `.collect()`, `.to_numpy()` or Python loop over columns or
   groups that an expression could replace?
3. Are NaN, null and inf each handled on purpose and documented?
4. Is validation done once at the boundary with `collect_schema()`, raising
   `ValueError`?
5. Is row order deterministic, and do tests sort on a unique key?
6. Does the output name come from the caller, not from inside the function?

## Sources

- https://docs.pola.rs/api/python/stable/reference/expressions/index.html
- https://docs.pola.rs/user-guide/lazy/using/
- https://docs.pola.rs/user-guide/expressions/window-functions/
