# 12. Decision Matrices

A Decision Matrix is a single-row exact-match lookup. You give it a combination of input values, and it returns the matching row. The match is exact. There are no ranges, no conditions, no iteration. If more than one row matches, the first match wins.

Decision Matrices are the simplest native BRE construct. Use them when all inputs are discrete values and you need a direct lookup.

## When to use a Decision Matrix

Use when:
- Every input is an exact value.
- You need the fastest possible lookup for a large table.
- You have one output or a fixed set of outputs that always apply together.

Do not use when:
- You need range conditions. Use a Decision Table.
- Multiple rows can match and you need all of them.

## Designing a Decision Matrix

Start with inputs. Define every input column: name, data type, required or optional.

Then define outputs. Define every output column: name, data type, scale for numbers.

Then add rows. Each row is one combination of inputs and their outputs. Row order matters. Put the most specific rows at the top.

## Input and output variable mapping

In the Expression Set, you add a step that calls the matrix. Map Expression Set input variables to matrix input columns. Map matrix output columns to Expression Set output variables.

In the Apex caller, you pass a map keyed by the Expression Set input variable names. You receive a map keyed by the Expression Set output variable names.

## Common mistakes

### Assuming a row exists when it does not

The matrix returns no match if no row matches. The caller receives an empty result. Always handle the no-match case.

### Forgetting that text comparisons are case-sensitive

If the input is "gold" and the row is "Gold", they do not match.

## Performance

Decision Matrices are the fastest native BRE construct. Lookup is a direct index scan.

For very large matrices, split into multiple smaller matrices by one input dimension.

## References

- [Salesforce Developer Guide: DecisionMatrixDefinition](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_decisionmatrixdefinition.htm)
