# 02. Key Concepts

This chapter defines every term you will encounter when working with native BRE. Keep this list handy.

## Expression Set

An Expression Set is the orchestration layer in native BRE. It chains together matrices, tables, formulas, and conditional logic into a logical flow. Think of it as a mini-flow inside BRE.

Expression Sets are what you invoke from Apex or Flow. You do not invoke matrices directly. You put matrices inside an Expression Set and invoke the Expression Set.

## Decision Matrix

A Decision Matrix is a single-row exact-match lookup. You give it a combination of input values, and it returns the matching output row.

If multiple rows match, the first match wins. You control row priority by ordering rows in the matrix.

## Decision Table

A Decision Table is a multi-row lookup that supports range conditions. Unlike a matrix, a table can return multiple matching rows and can handle ranges on input columns.

## Expression Set Definition

The metadata object that represents an Expression Set. The API name is `ExpressionSetDefinition`. You retrieve it with `sf project retrieve start -m ExpressionSetDefinition`.

## Expression Set Version

The activated version of an Expression Set. The metadata object is `ExpressionSetDefinitionVersion`. Only active versions respond to invocations.

## Decision Matrix Definition

The metadata object that represents a Decision Matrix. The API name is `DecisionMatrixDefinition`. Versions are stored in `DecisionMatrixDefinitionVersion`.

## Input Variable

A defined input field on an Expression Set. Has a name, a data type, and a label. The caller must supply a value for each input variable.

## Output Variable

A defined output field on an Expression Set. The Expression Set returns these after evaluation. The caller receives them as key-value pairs.

## Condition

A row-level matching rule within a Decision Table. Defines which input values or ranges match a particular output.

## Activation

The act of publishing a draft version as the live version. An activated version is immutable. You cannot edit an active version directly.

## Version

A numbered snapshot of an Expression Set or Matrix. Every save creates a draft. Every activation creates an active version.

## Invocation

The act of calling an Expression Set from Apex, Flow, or the REST API. The invocation passes input values and receives output values.

## Object and Field Alias

A business-friendly name that maps to a Salesforce object and field. BRE uses aliases to make rules readable. For example, an alias `CreditCheck.CompanyScore` might map to `Credit_Check_Response__c.Score__c`.

Aliases are configured in Setup under Business Rules Engine settings.

## BRE REST API

The Salesforce Connect API endpoint for invoking Expression Sets. The endpoint is:

```
POST /services/data/v59.0/async/expressionsets/{expressionSetName}/invoke
```

## Callable Apex

Apex interface that allows a class to be called dynamically. Used to build a BRE client that can be swapped between native BRE and other runtimes.

## Decision Explainer

A feature that explains which rule rows contributed to a decision outcome. Useful for audit and for user-facing explanations of why a decision was made.

## References

- [Salesforce Help: Get Started with Business Rules Engine](https://help.salesforce.com/s/articleView?id=ind.get_started_with_business_rules_engine.htm&language=en_US&type=5)
- [Salesforce Help: Business Rules Engine Default Limits](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_default_limits.htm&language=en_US&type=5)
