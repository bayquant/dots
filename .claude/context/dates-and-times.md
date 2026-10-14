# Dates and Times

## Pick the type by meaning

| The value is... | Type | Timezone |
|---|---|---|
| A calendar day (trade date, expiry, a daily bar's label) | `datetime.date` | none; a date has no timezone |
| An instant (tick, order time, intraday bar close) | `datetime.datetime` | always aware, in UTC |

- Never use a naive `datetime`.
- Never use a `date` where an instant is meant.
- Never use a midnight `datetime` to stand in for a `date`: it silently picks a timezone.

## Signatures

1. A parameter holding a calendar day is typed `str | date`. A parameter holding an instant is typed `str | datetime`. Never `date | datetime` in one parameter.
2. The `str` form is ISO 8601:
   - Date: `2024-03-01`
   - Instant: `2024-03-01T14:30:00Z` or `2024-03-01T09:30:00-05:00` (the offset is required; seconds and fractions are optional)
3. Normalize on entry. The first lines of the function call `to_date` or `to_datetime` (below); after that the function only ever sees `date` or `datetime`.
4. Strings never travel inward. Dataclasses, config objects, and return values hold `date` or `datetime`.
5. A malformed string fails at function entry with `ValueError`; the type checker cannot catch it, so the helpers are the only guard.

## Helpers

One pair per project, in a shared module, so every function converts the same way.

```python
def to_date(value: str | date) -> date:
    # datetime is a subclass of date, so reject it before anything else.
    if isinstance(value, datetime):
        raise TypeError("expected a date, got a datetime")
    if isinstance(value, str):
        return date.fromisoformat(value)
    return value


def to_datetime(value: str | datetime) -> datetime:
    if isinstance(value, str):
        value = datetime.fromisoformat(value)
    if value.tzinfo is None:
        raise ValueError("datetime must be timezone-aware")
    return value.astimezone(UTC)
```

## Crossing between date and datetime

State the timezone every time. For market days it is the exchange's timezone (for example `America/New_York`), not UTC.

| Conversion | How |
|---|---|
| `date` to `datetime` | `datetime.combine(day, time.min, tzinfo=tz)` |
| `datetime` to `date` | `instant.astimezone(tz).date()` |

## Comparisons

Never compare a `date` with a `datetime`: `==` is silently `False` and `<` raises `TypeError`. Convert one side first.

## Libraries and DataFrames

- Convert to a library's native type only at the call that needs it (pandas, polars, Nautilus, a vendor client).
- Inside a DataFrame, let the library store its native type (`pl.Date`, `pl.Datetime`, pandas `datetime64`).
- Do not type signatures as `pd.Timestamp` or `pl.Datetime`: that couples interfaces to one library.

## Output

Serialize with `.isoformat()`. Instants come out as `...+00:00`. Convert to local or exchange time only at display time.
