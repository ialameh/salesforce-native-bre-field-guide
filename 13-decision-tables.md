# 13. Decision Tables

A Decision Table is a multi-row lookup that supports range conditions. You give it input values, it evaluates each row, and returns all matching rows. It is more flexible than a Decision Matrix but slower at scale.

Use Decision Tables when your lookup involves range conditions or when multiple rows can match simultaneously.

## When to use a Decision Table

Use when:
- You need range conditions.
- Multiple rows can match and you need all of them.
- One or more inputs are numeric ranges rather than discrete values.

Do not use when:
- All inputs are exact-match discrete values. Use a Decision Matrix.
- The table will exceed 100,000 rows.

## Designing a Decision Table

### Columns

Decision Tables have input columns and output columns. There is a limit of 30 input columns and 5 output columns per table.

### Rows

Each row has a condition and a set of output values.

Conditions can be:
- Exact match: `CustomerTier = "Gold"`
- Range: `DistanceMiles > 100 AND DistanceMiles <= 500`
- Pattern: STARTS WITH, CONTAINS, ENDS WITH

### Output rows returned

By default, a Decision Table returns all rows that match. Configure the table to return only the first match if you prefer matrix-like behavior.

## The no-match case

If no row matches, the table returns an empty result. Handle this case in the Expression Set or in the Apex caller.

## Performance at scale

Decision Tables slow down as row count increases. Above 100,000 rows, performance degrades noticeably. Split large tables by a high-cardinality dimension.

## References

- [Salesforce Help: Decision Table Data Limits](https://help.salesforce.com/s/articleView?id=ind.reference_decision_table_data_limits.htm&language=en_US&type=5)
- [Salesforce Help: Business Rules Engine Default Limits](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_default_limits.htm&language=en_US&type=5)
