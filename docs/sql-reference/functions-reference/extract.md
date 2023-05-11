---
layout: default
title: EXTRACT (legacy)
nav_exclude: true
description: Reference material for the EXTRACT (legacy) function
parent: SQL functions
---

# EXTRACT (legacy)

{: .warning}
  >You are looking at legacy documentation for Firebolt's deprecated date and timestamp type functions.
  >New types were introduced in DB version 3.19 under the names `PGDATE` and `TIMESTAMPNTZ`, and made generally available in DB version 3.22.
  >
  >If you worked with Firebolt before DB version 3.22, you might still be using the legacy date and timestamp types.
  >Determine which types you are using by executing the query `SELECT EXTRACT(CENTURY FROM DATE '2023-03-16');`.
  >If this query returns an error, you are still using the legacy date and timestamp types and can continue with this documentation, or find instructions to use the new types [here](../../release-notes/release-notes.md#date-and-timestamp-names-available-for-new-data-types).
  >If this query returns a result, you are already using the redesigned date and timestamp types and can use the [EXTRACT](./extract-new.md) function instead.

Retrieves the time unit, such as `year` or `hour`, from a `DATE`, `TIMESTAMP`, or `TIMESTAMPTZ` value.

## Syntax
{: .no_toc}

```sql
EXTRACT(<time_unit> FROM <expression>)
```

## Parameters
{: .no_toc}

| Parameter      | Description                                           | Supported input types                                                                                                                               |
| :------------- | :---------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<time_unit>`  | The time unit to extract from the expression.         | `day`, `dow`, `month`, `week`, `weekiso`, `quarter`, `year`, `hour`, `minute`, `second`, `epoch` (unquoted) |
| `<expression>` | The expression from which the time unit is extracted. | `DATE`, `TIMESTAMP`                                                                                                                  |

### Time Units

| Unit              | Description                                                                                                                                                                                                                                                                                                                                             | Supported input types               | Return type      | Example                                                                                                                                |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `day`             | Extract the day (of the month) field.                                                                                                                                                                                                                                                                                                                   | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER`            | `SELECT EXTRACT(day FROM DATE '2001-02-16');  --> 16`                                                                                  |
| `epoch`           | For `TIMESTAMPTZ`, extract the number of seconds since `1970-01-01 00:00:00 UTC`. For `TIMESTAMP`, extract the number of seconds since `1970-01-01 00:00:00` independent of a time zone. `DATE` expressions are implicitly converted to `TIMESTAMP`.                                                                                                    | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER` | `SELECT EXTRACT(epoch FROM TIMESTAMP '2001-02-16 20:38:40.12');  --> 982355920`                                                 |
| `hour`            | Extract the hour field (0–23).                                                                                                                                                                                                                                                                                                                          | `TIMESTAMP`, `TIMESTAMPTZ`          | `INTEGER`            | `SELECT EXTRACT(hour FROM TIMESTAMP '2001-02-16 20:38:40.12');  --> 20`                                                                |
| `dow`          | Extract the day of the week as Monday (1) to Sunday (7).                                                                                                                                                                                                                                                                                                | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER`            | `SELECT EXTRACT(isodow FROM DATE '2022-10-13');  --> 4`                                                                                |
| `minute`          | Extract the minutes field (0–59).                                                                                                                                                                                                                                                                                                                       | `TIMESTAMP`, `TIMESTAMPTZ`          | `INTEGER`            | `SELECT EXTRACT(minute FROM TIMESTAMP '2022-12-31 23:42:59');  --> 42`                                                                 |
| `month`           | Extract the number of the month within the year (1–12).                                                                                                                                                                                                                                                                                                 | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER`            | `SELECT EXTRACT(month FROM DATE '1000-12-31');  --> 12`                                                                                |
| `quarter`         | Extract the quarter of the year (1–4) that the date is in:<br>`[01, 03] -> 1`<br>`[04, 06] -> 2`<br>`[07, 09] -> 3`<br>`[10, 12] -> 4`                                                                                                                                                                                                                  | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER`            | `SELECT EXTRACT(quarter FROM DATE '1000-10-31');  --> 4`                                                                               |
| `second`          | Extract the second's field, including fractional parts.                                                                                                                                                                                                                                                                                                 | `TIMESTAMP`, `TIMESTAMPTZ`          | `DECIMAL(8, 6)`  | `SELECT EXTRACT(second FROM TIMESTAMP '2001-02-16 20:38:40.12');  --> 40.120000`                                                       |
| `weekiso`            | Extract the number of the ISO 8601 week-numbering week of the year. By definition, ISO weeks start on Mondays and the first week of a year contains January 4 of that year. It is possible for early-January dates to be part of the 52nd or 53rd week of the previous year, and for late-December dates to be part of the first week of the next year. | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`. | `INTEGER`            | `SELECT EXTRACT(week FROM DATE '2005-01-01');  --> 53`<br>`SELECT EXTRACT(week from DATE '2006-01-01');  --> 52`                       |

| `week`            | Extract the number of the week starting with the first day of the year being in week `0`. | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`. | `INTEGER`            | `SELECT EXTRACT(week FROM DATE '2005-01-01');  --> 0`<br>`SELECT EXTRACT(week from DATE '2006-01-07');  --> 2`                       |
| `year`            | Extract the year field.                                                                                                                                                                                                                                                                                                                                 | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`  | `INTEGER`            | `SELECT EXTRACT(year FROM TIMESTAMP '2001-02-16');  --> 2001`                                                                          |

## Remarks
{: .no_toc}

The `EXTRACT` function can be used in the `PARTITION BY` clause of `CREATE TABLE` commands.

```sql
CREATE DIMENSION TABLE test (
  d DATE,
  t TIMESTAMP
)
PARTITION BY EXTRACT(month FROM d), EXTRACT(hour FROM t);
```
