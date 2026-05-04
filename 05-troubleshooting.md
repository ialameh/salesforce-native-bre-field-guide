# 05. Troubleshooting

When a native BRE rule does not behave, the error usually surfaces at the caller level. Apex throws an HTTP error, the Flow shows a fault, or the Integration Procedure returns null. None of these tell you the rule itself is the problem. This chapter gives you a systematic way to find the real cause.

## The layered diagnosis order

Always work from the outside in. Each step eliminates one layer.

### Step 1. Is Apex reaching the Expression Set?

Check the Apex debug log. Look for the HTTP call to `/services/data/.../async/expressionsets/`. If the call is made, note the request body and the response status code.

- 200: the call succeeded. The problem is in the output parsing.
- 400: the inputs are malformed. Check the request body against the Expression Set input variables.
- 404: the Expression Set name is wrong or the name is not active.
- 500: the Expression Set crashed internally.

### Step 2. Does the Expression Set return anything?

Go to the BRE UI and open the Expression Set. Click Simulate. Enter the exact inputs from the Apex caller. Click Evaluate.

- If simulation returns a result: the rule is working. The problem is in the Apex caller.
- If simulation returns no match: the inputs do not match any row in the active version.
- If simulation throws an error: the rule has a configuration problem.

### Step 3. Is the correct version active?

In the BRE UI, open the Expression Set. Look at the Versions panel. Confirm the version you tested in simulation is the one marked Active.

### Step 4. Check data types

If the caller is passing "123" (text) and the Expression Set input is Number, coercion may work or may not. Verify the data type matches.

### Step 5. Check the Expression Set step chain

If the Expression Set calls a matrix, open the matrix separately and simulate it. If the matrix returns no match, the Expression Set has no data to work with.

## Common symptoms and their causes

### Symptom: HTTP 404 on Expression Set invocation

Causes:
1. The Expression Set name is wrong. Check the DeveloperName in Setup.
2. The Expression Set is not active. Check the Versions panel.
3. The API version in the URL is wrong. Try v59.0 or your org's current API version.

### Symptom: No match when rows exist

Causes, in order of likelihood:
1. The draft is not activated. The active version has different rows.
2. Input mapping in Apex passes the wrong field names.
3. Data type mismatch. Text versus Number.
4. Leading or trailing spaces in text values.

### Symptom: Rule returns wrong output values

Causes:
1. Multiple rows match and the wrong row wins. Check row ordering.
2. A different version is active.
3. Apex is reading the wrong output variable name from the response.

### Symptom: HTTP 500 on invocation

Causes:
1. The Expression Set has a broken step (division by zero, null reference).
2. A called matrix is inactive.
3. The Expression Set exceeds nesting depth limits.

## Tools for deeper investigation

### Simulation

The fastest tool. Always start here. Simulation runs against the draft, not the active version, so it is safe to use on unactivated changes.

### Debug logs

Enable debug logging for the running user. Set the log category for "Callout" to DEBUG to see HTTP request and response details.

### BRE Execution Overview

In Setup under Business Rules Engine settings, there is an Execution Overview view. It shows recent invocations: which Expression Set, when, and the user.

## References

- [Salesforce Developer Guide: BRE Expression Set Connect API](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/connect_resources_bre_expression_set.htm)
