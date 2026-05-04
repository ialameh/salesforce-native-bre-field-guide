# 01. The Mental Model

Native BRE is a decision logic engine. It lives inside Salesforce, it is declarative, and it runs when something calls it. The thing that calls it is usually Apex, a Flow, or an Integration Procedure acting as a wrapper. BRE is never the trigger. It is always the responder.

Understanding this changes how you debug it.

## The three layers

Native BRE has three distinct layers.

### Layer 1. The definition

This is what you build in the BRE UI. A Decision Matrix, a Decision Table, or an Expression Set. You configure input columns, output columns, rows, conditions, and formulas. None of this runs anything until something calls it.

The definition lives in the database as `ExpressionSetDefinition` or `DecisionMatrixDefinition` records. When you save, you are saving a draft. When you activate, you are publishing a version.

### Layer 2. The activation

Activation creates an immutable version snapshot. The metadata objects are `ExpressionSetDefinitionVersion` and `DecisionMatrixDefinitionVersion`. You can have multiple versions side by side, but only one is live at a time per environment.

This versioning model matters for debugging. When something goes wrong, you need to know which version was active at the time.

### Layer 3. The invocation

Something in your org calls BRE. The caller passes input values and receives output values. The caller is responsible for mapping inputs to the BRE action and mapping outputs back to the calling context.

The most common callers are:

- Apex using the BRE REST API or a custom Callable implementation
- Flow using the BRE Action elements
- Integration Procedure as a wrapper calling Apex which calls BRE

## What native BRE is not

Native BRE is not legacy OmniStudio. The metadata types are different:

| This guide covers | Legacy path (not covered here) |
|---|---|
| `ExpressionSetDefinition` | `vlocity_cmt.IntegrationProcedureService` |
| `DecisionMatrixDefinition` | `vlocity_cmt__DRMatrix__c` |
| `DecisionMatrixDefinitionVersion` | `vlocity_cmt__IntegrationProcedure__c` |

The legacy path uses Integration Procedures to call OmniStudio Decision Matrices. Native BRE uses Expression Sets to call Decision Matrices directly from Apex or Flow.

## The call chain for native BRE

```
Apex (or IP acting as wrapper)
  -> BRE REST API or custom Apex caller
    -> Expression Set
      -> Decision Matrix lookup
        -> returns output values
    -> Expression Set returns result
  -> Apex receives output
  -> updates records, writes audit
```

Each step in this chain is a potential failure point.

## What this means for debugging

When a rule returns the wrong result, check in this order:

1. Are the inputs mapped correctly? (Apex caller)
2. Is the correct version active? (BRE UI, Versions panel)
3. Does the row exist in the active version? (BRE UI, simulate tab)
4. Are the data types correct? (Text versus Number, scale, precision)
5. Does the calling user have BRE execution permissions? (Permission set assignment)

Most "the rule is not working" issues are actually input mapping issues in the Apex caller layer.

## Versioning semantics

When you edit an active Expression Set, Salesforce creates a new draft. The active version continues to run until you explicitly activate the new draft. This means two things:

1. You can prepare changes in a sandbox without affecting production traffic.
2. If you forgot to activate, the system keeps running the old version silently. There is no warning.

After you activate, the new version becomes the default for all new sessions. Existing sessions that are already running keep their snapshot.

## References

- [Salesforce Industries Reference: Business Rules Engine](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/business_rules_engine.htm)
- [Salesforce Developer Guide: Expression Set Connect API](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/connect_resources_bre_expression_set.htm)
