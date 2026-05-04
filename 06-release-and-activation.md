# 06. Release and Activation

Activation is the step most likely to be forgotten in a deployment. The rule works in your sandbox because you are looking at the draft. It silently fails in production because the draft was never activated. This chapter covers the full release process.

## The activation lifecycle

Every Expression Set and Decision Matrix has a lifecycle:

1. Draft: you are editing it in the BRE UI.
2. Saved: the draft is stored but not active.
3. Activated: the draft becomes the live version.
4. Superseded: a newer version is activated; the old one remains retrievable but is not running.

You can have one active version at a time. You can have multiple drafts.

## Activation in the BRE UI

1. Open the Expression Set or Matrix in the BRE UI.
2. Click the Activate button.
3. Confirm the version number.

## Activation is manual

You cannot activate via the Salesforce CLI. This means:

- When you deploy to production, rules arrive as drafts.
- Someone must log in and activate each one manually.
- There is no way to automate activation as part of a CI pipeline.

This is deliberate. Activation is a business action. Someone should own the decision to flip a rule live.

## Version rollback

To roll back:

1. Open the rule in the BRE UI.
2. Go to the Versions panel.
3. Find the version you want to reactivate.
4. Click Activate on that version.

## Pre-deployment checklist

Before you deploy a rule to a higher environment:

- [ ] Rule is activated in the source org.
- [ ] All rows are populated.
- [ ] Input and output variable names match the caller's expectations.
- [ ] Simulation was run with realistic values.
- [ ] A test class covers the rule invocation.
- [ ] The calling Apex has been updated if output variable names changed.

## Post-deployment checklist

After deploying a rule to a new environment:

- [ ] Activate the rule in the target org.
- [ ] Run simulation again in the target org.
- [ ] Confirm the calling Apex has the correct Expression Set name.
- [ ] Run the Apex test suite.

## Change management

Treat rule changes like code changes. Review, test, deploy, activate, monitor. Assign someone ownership of the activation step in your deployment runbook.

## References

- [Salesforce Help: Get Started with Business Rules Engine](https://help.salesforce.com/s/articleView?id=ind.get_started_with_business_rules_engine.htm&language=en_US&type=5)
