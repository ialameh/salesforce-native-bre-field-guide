# Salesforce Native BRE Field Guide

A practical handbook for developers and architects building on the Salesforce Industries Business Rules Engine (BRE). Written by people who hit these problems in production and wrote down what they learned.

The aim is simple: keep you out of the kind of trouble that costs a day or two and produces an error message that names the wrong layer.

## Who this is for

- Developers who are wiring BRE Expression Sets and Decision Matrices into Apex, Flow, or Integration Procedures and need a checklist that prevents the obvious failure modes.
- Architects who are designing the decision logic layer and need to know which seams require deployment discipline, which require manual activation, and which silently diverge between environments.
- Tech leads who are setting up the team's first native BRE project and want a baseline that survives the first round of "it works in my org but not in yours".

It assumes you know what a Decision Matrix is, what an Expression Set does, and what a business rule is meant to solve. It does not assume you have used OmniStudio or legacy Vlocity.

## Two BRE runtimes to know before you start

This guide covers **Salesforce native BRE**, which uses `ExpressionSetDefinition`, `DecisionMatrixDefinition`, and `DecisionMatrixDefinitionVersion`. This is different from legacy OmniStudio BRE, which uses `vlocity_cmt.IntegrationProcedureService` and `vlocity_cmt__DRMatrix__c`.

If your org shows Decision Tables or Expression Sets in Setup under "Business Rules Engine", you are looking at native BRE. If you are looking at OmniStudio in the App Launcher, you may be dealing with the legacy path. Both can coexist. This guide is about the native path only.

## How to read it

Pick the chapter that matches what you are doing right now.

### Quickstart

| Chapter | Read it when |
|---------|--------------|
| [00. Quickstart](./00-quickstart.md) | You want a working Expression Set and Decision Matrix in 15 minutes, end to end. |

### Foundations

| Chapter | Read it when |
|---------|--------------|
| [01. The Mental Model](./01-mental-model.md) | You are starting on native BRE and want the layered architecture in your head. |
| [02. Key Concepts](./02-key-concepts.md) | You want a concise definition of every term you will encounter. |
| [03. Project Structure](./03-project-structure.md) | You are setting up source control for BRE metadata. |
| [04. Starting Checklist](./04-starting-checklist.md) | You are about to build a new Expression Set or Matrix. Run through this first. |

### Operations

| Chapter | Read it when |
|---------|--------------|
| [05. Troubleshooting](./05-troubleshooting.md) | The rule is not firing and you need a structured way to find the broken layer. |
| [06. Release and Activation](./06-release-and-activation.md) | You are promoting rules from sandbox to production. |
| [07. Anti-Patterns](./07-anti-patterns.md) | You want to know what to avoid before you do it. |
| [08. Glossary](./08-glossary.md) | You see a term and want one paragraph of context. |

### Design and Engineering

| Chapter | Read it when |
|---------|--------------|
| [09. Security](./09-security.md) | You are designing rules that touch sensitive data. |
| [10. Testing](./10-testing.md) | You want a real testing strategy, not just a simulation pass. |
| [11. Observability](./11-observability.md) | You need to know what your rules are doing in production. |
| [12. Decision Matrices](./12-decision-matrices.md) | You need a single-row exact-match lookup. |
| [13. Decision Tables](./13-decision-tables.md) | You need a multi-row lookup with ranges. |
| [14. Expression Sets](./14-expression-sets.md) | You need chained calculations and conditional logic. |
| [15. Performance and Limits](./15-performance-and-limits.md) | The rule is slow, or you want to design within the budgets. |

### Reference and Migration

| Chapter | Read it when |
|---------|--------------|
| [16. FAQ](./16-faq.md) | You have a quick question and want a quick answer. |
| [17. Migration from Legacy BRE](./17-migration-guides.md) | You are migrating from `vlocity_cmt` Integration Procedures or Decision Matrices. |
| [18. CI/CD Recipes](./18-cicd-recipes.md) | You want concrete pipeline configurations. |

### Worked Material

| Folder | What's in it |
|--------|--------------|
| [Cookbook](./cookbook) | End-to-end worked examples with all files. |
| [Templates](./templates) | Copy-paste skeletons for permission sets, scratch org definitions, and metadata manifests. |
| [Case Studies](./case-studies) | Anonymised real incidents and how they were resolved. |

## The thirty-second version

If you only have time to remember three things, remember these.

1. Native BRE uses `ExpressionSetDefinition` and `DecisionMatrixDefinition`. Legacy OmniStudio BRE uses `vlocity_cmt.IntegrationProcedureService`. These are not the same runtime. Know which one your org has before you build.

2. Authoring state is not runtime state. Saving in the BRE UI edits the draft. Simulation runs against the draft. Production runs against the activated version. Activate after every meaningful change.

3. Apex calls BRE. BRE does not call Apex. The Expression Set or Matrix is the rule engine. Apex is the orchestration layer. Do not put business logic inside an Integration Procedure just because it can call Apex.

## Project status

This guide is at v0.1.0. All chapters are drafted. Diagrams and cookbook examples are in progress.

## License

Released under CC BY 4.0. Salesforce, Business Rules Engine, Industries, FSC, and related marks are trademarks of Salesforce, Inc.
