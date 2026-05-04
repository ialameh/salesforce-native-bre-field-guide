# 04. Starting Checklist

Run through this before you build a new Expression Set or Decision Matrix. It takes five minutes and prevents the most common mistakes.

## Pre-build checklist

### 1. Confirm native BRE is enabled

In Setup, search for "Business Rules Engine". If you do not see it, open a Salesforce support case.

Run this to confirm from CLI:

```bash
sf data query -o <ORG> --use-tooling-api --query "SELECT Id, DeveloperName FROM ExpressionSetDefinition LIMIT 1"
```

If this returns results, native BRE is available. If not, you may have legacy OmniStudio or no BRE at all.

### 2. Confirm the calling context

Know what will call the rule before you design it. The inputs, data types, and output mapping all depend on the caller.

- Apex: you write a REST call or use a custom Callable class.
- Flow: use the BRE Action elements from Flow Builder.
- Integration Procedure: use as a thin wrapper, not as the decision logic home.

If you are unsure, start with Apex calling the BRE REST API directly. It is the most controllable path.

### 3. Decide: Decision Matrix or Decision Table

Use a Decision Matrix when:
- You need a single exact-match lookup.
- The inputs are all discrete values.
- Performance matters and the lookup table is large.

Use a Decision Table when:
- You need range conditions.
- Multiple rows can match.
- You need to return more than one result row.

### 4. Define inputs before you build

Write down the exact input columns: names, data types, required or optional. Get these from the Apex developer. If the caller maps a text value to a number column, the rule silently returns no match.

### 5. Define outputs before you build

Write down the exact output columns. Get these from the caller developer. If the caller does not need a column, do not include it.

### 6. Estimate row count

If your table will have more than 100,000 rows, reconsider the architecture. Can you split into multiple matrices? Can you use ranges in a table instead?

## Build-time checklist

### 7. Name things correctly

Use a naming convention from the start. BRE names appear in error messages and logs. A name like `Matrix_v1` tells you nothing. A name like `GoldMemberElectronicsDiscount` does.

### 8. Set data types correctly

Text, Number, Currency, Boolean, Date. Mismatches are the most common cause of silent no-match results.

### 9. Test with simulation before activation

Enter realistic values in the BRE UI Simulate tab. Enter edge cases. The simulation is fast and catches errors before they reach production.

### 10. Do not skip simulation

Simulation runs against the draft. Activating and redeploying takes much longer. Catch errors in simulation.

## Post-activation checklist

### 11. Verify activation

Check the Versions panel in the BRE UI. Confirm the version you just activated is the one showing as Active.

### 12. Update the Apex caller

After activation, the output variable names are fixed. Update the Apex code that parses the BRE response if the names changed.

### 13. Add a test case

Write an Apex test class that calls the BRE with known inputs and asserts known outputs.

### 14. Document the rule

Add a comment to the Apex caller referencing the Expression Set name and version. Future maintainers need to know where the decision logic lives.

## References

- [Salesforce Help: Get Started with Business Rules Engine](https://help.salesforce.com/s/articleView?id=ind.get_started_with_business_rules_engine.htm&language=en_US&type=5)
- [Salesforce Help: Business Rules Engine Default Limits](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_default_limits.htm&language=en_US&type=5)
