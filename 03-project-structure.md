# 03. Project Structure

Native BRE metadata lives in source control the same way as the rest of your Salesforce project. This chapter explains how to organize it and what to watch out for.

## Where BRE metadata lives

When you retrieve native BRE components with the Salesforce CLI, they appear under:

```
force-app/
  main/
    default/
      expressionSets/
        <ORG>_AutoDecision/
          <ORG>_AutoDecision.expressionSet
          <ORG>_AutoDecision.version/
      decisionMatrix/
        <ORG>_AutoDecisionTier/
          <ORG>_AutoDecisionTier.decisionMatrix
          <ORG>_AutoDecisionTier.version/
```

The exact folder structure depends on your API version and Salesforce CLI version. Use `sf project retrieve start -m ExpressionSetDefinition` to retrieve the actual structure.

## What to commit

Commit everything under `expressionSets/` and `decisionMatrix/`. These are your BRE rules.

Also commit any Apex classes that call BRE, since those contain the invocation logic that references the Expression Set names.

Do not commit draft versions you are not ready to activate. Once activated, a version is immutable, so it is safe to commit.

## The activation problem

Unlike most Salesforce metadata, BRE activation is not part of the deployment. When you deploy an Expression Set to production, it arrives as a draft. Someone must log in and activate it manually.

This is a deliberate design. Activation is a business action, not a technical one. Someone should own the decision to flip a rule live.

Workaround: document the activation steps in your deployment runbook and track them as a post-deployment step.

## Metadata API types

Native BRE uses these metadata types:

```
ExpressionSetDefinition
ExpressionSetDefinitionVersion
DecisionMatrixDefinition
DecisionMatrixDefinitionVersion
```

These are different from the legacy OmniStudio types:

```
vlocity_cmt__IntegrationProcedure__c
vlocity_cmt__DRMatrix__c
vlocity_cmt__DRMatrixRow__c
```

If you are migrating from legacy BRE, Chapter 17 covers this in detail.

## forceignore

If you want to manage BRE rules selectively and avoid retrieving everything, add paths to `forceignore`:

```
**/expressionSets/**
**/decisionMatrix/**
```

## Debugging retrieval issues

If `sf project retrieve start -m ExpressionSetDefinition` returns no results, try:

```bash
sf project retrieve start -m ExpressionSetDefinition --api-version 59.0
```

BRE metadata types were introduced at specific API versions. Using the latest API version helps.

## References

- [Salesforce CLI Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_plugins.meta/sfdx_cli_plugins/cli_project_deploy.htm)
- [Metadata API Developer Guide: BRE Components](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_expressionsetdefinition.htm)
