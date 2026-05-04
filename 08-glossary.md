# 08. Glossary

**Activation** -- Publishing a draft version of an Expression Set or Matrix as the live version. An activated version is immutable.

**BRE** -- Business Rules Engine. The Salesforce Industries decision logic engine. Uses `ExpressionSetDefinition`, `DecisionMatrixDefinition`, and `DecisionMatrixDefinitionVersion`.

**Caller** -- The Apex class, Flow, or Integration Procedure that invokes a BRE Expression Set.

**Decision Matrix** -- A single-row exact-match lookup. Returns one output row for a given combination of inputs. Does not support ranges.

**Decision Table** -- A multi-row lookup that supports range conditions. Returns one or more output rows for a given set of inputs.

**Draft** -- The current working version of a rule. Editable. Does not respond to invocations until activated.

**Expression Set** -- The orchestration layer in native BRE. Chains matrices, tables, formulas, and conditional logic. Invoked from Apex or Flow.

**Expression Set Definition** -- The metadata object that represents an Expression Set. API name: `ExpressionSetDefinition`.

**Expression Set Version** -- The activated version of an Expression Set. API name: `ExpressionSetDefinitionVersion`.

**Input Variable** -- A defined input field on an Expression Set. The caller passes values for each input variable.

**Legacy BRE** -- The old OmniStudio/Vlocity path. Uses `vlocity_cmt.IntegrationProcedureService`, `vlocity_cmt__DRMatrix__c`, and Integration Procedures. Not covered in this guide.

**Native BRE** -- The current Salesforce Industries BRE path. Uses `ExpressionSetDefinition` and `DecisionMatrixDefinition`. What this guide covers.

**Output Variable** -- A defined output field on an Expression Set. Returned to the caller after evaluation.

**Simulation** -- The test mode in the BRE UI. Evaluates inputs against the draft version without activation.

**Version** -- A numbered snapshot of a rule. Every save creates a draft. Every activation creates an active version.
