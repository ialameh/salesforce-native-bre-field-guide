# 17. Migration from Legacy BRE

This chapter covers migrating from the legacy OmniStudio/Vlocity BRE path to native Salesforce BRE. The two runtimes are completely different. Migration is a full rebuild, not an upgrade.

## What changed

| Legacy path | Native path |
|-------------|-------------|
| `vlocity_cmt.IntegrationProcedureService` | BRE REST API or Apex caller |
| `vlocity_cmt__IntegrationProcedure__c` | Expression Set |
| `vlocity_cmt__DRMatrix__c` | Decision Matrix |
| Integration Procedure as orchestration layer | Expression Set as orchestration layer |
| OmniScript / FlexCard calling IP | Apex / Flow calling Expression Set |

## Migration steps

### 1. Audit existing legacy assets

Run these queries to understand what legacy BRE assets exist in your org:

```bash
sf data query -o <ORG> --query "SELECT Id, Name FROM vlocity_cmt__IntegrationProcedure__c LIMIT 10"
sf data query -o <ORG> --query "SELECT Id, Name FROM vlocity_cmt__DRMatrix__c LIMIT 10"
sf data query -o <ORG> --query "SELECT Id, Name FROM vlocity_cmt__DRMatrixRow__c LIMIT 10"
```

If these return no results, the legacy path may already be gone.

### 2. Identify callers

Find every Apex class, Integration Procedure, and Omniscript that calls `vlocity_cmt.IntegrationProcedureService`. These need to be updated to call native BRE instead.

```bash
grep -r "vlocity_cmt.IntegrationProcedureService" force-app/
grep -r "IntegrationProcedureService" force-app/
```

### 3. Build the native BRE structure

Create new Expression Sets and Decision Matrices that replicate the logic from the legacy Integration Procedures and matrices. The rule logic is the same. The runtime is different.

### 4. Update the Apex callers

Replace calls to `vlocity_cmt.IntegrationProcedureService` with calls to the BRE REST API or a custom `SalesforceBREClient` callable class.

The response contract can stay the same. The input mapping may need to change if the new Expression Set has different input and output variable names.

### 5. Update Integration Procedure wrappers

If you have Integration Procedures that act as thin wrappers around Apex (calling Apex as a Remote Action), update the Apex class to call native BRE instead of the legacy path.

If the Integration Procedures contain decision logic themselves, move that logic into an Expression Set.

### 6. Update permission sets

Legacy BRE and native BRE have different permission sets. The Rule Engine Designer permission set exists in both, but the execution permission sets may differ. Check and reassign.

### 7. Test end-to-end

Run simulation on all new Expression Sets. Run Apex tests. Run integration tests through the full flow.

## What to watch for

### Metadata type mismatch

The old loading documents and CSV import workflows were written for `vlocity_cmt__DRMatrix__c`. These do not work for `DecisionMatrixDefinition`. Rebuild the matrices in the native BRE UI.

### Input and output variable names

If you change the Expression Set structure, the input and output variable names change. Update the Apex caller accordingly.

### Expression Set naming

The old Integration Procedure was named `silverchef_AutoDecision`. The new Expression Set can have the same name or a different one. Keep the Apex caller configurable via Custom Metadata so the name is not hardcoded.

## When to use an Integration Procedure as a wrapper

If you have OmniScripts or FlexCards that call Integration Procedures, and you do not want to change the UI layer, keep the Integration Procedure as a thin wrapper. It calls Apex, which calls native BRE. This avoids changing the UI layer while adopting the new BRE runtime.

## References

- [Salesforce Industries Developer Guide: Business Rules Engine](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/business_rules_engine.htm)
- [Salesforce Developer Blog: Migrating from Calculation Procedures to BRE](https://developer.salesforce.com/blog)
