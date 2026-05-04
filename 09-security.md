# 09. Security

BRE rules operate on data that passes through them. The security model controls who can create and edit rules, and what data the rules can access at runtime.

## Permission sets

Native BRE has permission sets for both design and execution.

**Rule Engine Designer** -- Grants the ability to create, edit, and activate rules in the BRE UI. Assign this to developers and architects.

**BRE Execution** -- Grants the ability to invoke Expression Sets at runtime. Assign this to the Salesforce user that Apex runs as, or to the specific users who trigger decisioning.

Both are required for an end-to-end flow. Designers need the designer permission. Runtime callers need the execution permission.

## Field-level security

BRE rules respect field-level security on input data. If a caller passes a field that the running user cannot read, the rule receives null or an error.

Test rules under the same user context that runs them in production.

## Row-level security

BRE rules do not enforce row-level security directly. If an Expression Set looks up Account data and the caller does not apply a filter, the rule can return data from Accounts the running user should not see.

Apply row-level security filters in the Apex caller before building the BRE input payload.

## Audit trail

Native BRE does not have a built-in detailed audit log for each invocation. If you need a detailed audit trail, build it into the Apex caller.

Log the input values, output values, Expression Set name, version, timestamp, and user to a custom object. This is especially important for rules that affect financial decisions.

## What BRE does not secure

BRE does not encrypt data at rest or in transit. It does not have built-in data loss prevention. Handle sensitive data in the Apex caller before it reaches BRE.

## References

- [Salesforce Help: BRE Permission Sets](https://help.salesforce.com/s/articleView?id=ind.bre_permission_sets.htm&language=en_US&type=5)
