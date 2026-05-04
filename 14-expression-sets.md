# 14. Expression Sets

An Expression Set is the orchestration layer in native BRE. It chains together matrices, tables, formulas, and conditional logic. It is what you invoke from Apex or Flow. You do not invoke matrices directly.

## When to use an Expression Set

Use an Expression Set when:
- You need to call multiple matrices or tables in sequence.
- You need conditional logic based on intermediate results.
- You need to compute a value from multiple inputs without a lookup table.

Do not use an Expression Set when:
- A single matrix handles the entire logic. Call the matrix directly if your platform supports it, or use a simple Expression Set with one matrix step.

## Structure of an Expression Set

An Expression Set is made of steps. Each step has:
- A name
- A type: Expression, Lookup, Conditional Branch
- Inputs and outputs
- A target (for conditional branches)

Steps run in order. The output of one step can be the input of the next.

## Calling matrices from an Expression Set

Add a step of type "Decision Matrix Lookup". Select the matrix. Map the input variables to the matrix input columns. Map the matrix output columns to Expression Set output variables.

## Expression Set actions

Each step does one of three things:

**Assignment** -- Computes a value and stores it in a variable.

**Lookup** -- Calls a Decision Matrix or Decision Table and stores the result.

**Conditional Branch** -- Evaluates a condition and routes to a different step.

## Nesting depth

Expression Sets can call other Expression Sets. The nesting limit is 3 levels. If you hit the limit, chain them sequentially in the Apex caller instead.

## Input and output variables

Define input variables on the Expression Set for the values the caller must provide. Define output variables for the values the Expression Set returns.

## Debugging Expression Sets

Use the Simulate tab in the BRE UI. Enter input values and step through the evaluation to see what each step produces.

## Common mistakes

### Exceeding nesting depth

Nesting more than 3 Expression Sets. Solve it by moving part of the chain to the Apex caller.

### Making the set do too much

One Expression Set that handles pricing, discounting, shipping, and tax is unmaintainable. Break it into focused pieces and chain them in the Apex caller.

## References

- [Salesforce Help: Expression Sets](https://help.salesforce.com/s/articleView?id=ind.expression_sets.htm&language=en_US&type=5)
- [Salesforce Developer Guide: BRE Expression Set Connect API](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/connect_resources_bre_expression_set.htm)
