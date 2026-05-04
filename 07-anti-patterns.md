# 07. Anti-Patterns

This chapter is a list of things that go wrong with native BRE and how to avoid them. Use it when you are debugging or when you want to write a rule that does not end up on this list.

## Nesting expression sets more than three levels deep

Expression Sets can call other Expression Sets. The limit is three levels. When you hit the limit, Salesforce throws a runtime error.

If you need more than three levels, chain the Expression Sets sequentially in the Apex caller instead of nesting them.

## Activating without simulation

Activating a rule without running the simulator first is like deploying code without running tests. Always simulate first.

## Skipping the output variable mapping

The Expression Set returns output variables. If the Apex caller does not read the correct key from the response, the result is silently dropped.

Always check the exact output variable names in the Expression Set definition and match them in Apex.

## Mixing legacy and native BRE

Do not mix `vlocity_cmt.IntegrationProcedureService` with native BRE in the same caller. Pick one path and stick with it.

If you are migrating from legacy BRE, Chapter 17 has the full picture.

## Building logic inside an Integration Procedure

An Integration Procedure can call Apex. But do not put decision logic inside an IP. The IP should be a thin wrapper that collects data, calls Apex, and returns the result.

Decision logic belongs in the Expression Set or in Apex. Not in an IP.

## Not handling the no-match case

Decision Matrices return no match if no row conditions are met. The caller receives an empty result.

Handle the no-match case explicitly. In Apex, check if the result is null or empty before proceeding. Provide a fallback outcome or a meaningful error.

## Decision Tables above 100,000 rows

Performance degrades above 100,000 rows. If a table grows beyond this, split it by a high-cardinality dimension.

## Forgetting permission set assignment

BRE requires permission sets for execution. Users need a BRE execution permission set, not just the Rule Engine Designer permission.

If a rule works for an admin but not for a standard user, check the permission set difference.

## References

- [Salesforce Help: Business Rules Engine Default Limits](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_default_limits.htm&language=en_US&type=5)
