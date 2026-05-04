# 00. Quickstart

This is the fastest path I know to a working native BRE Expression Set and Decision Matrix. Fifteen minutes if you already have a BRE-enabled org and the Salesforce CLI installed. Half an hour if you do not.

We will build a simple rule that looks up a decision outcome and Maximum Allowance factor based on region, company score, and individual score. The point is not the business logic itself. The point is that you walk through every layer once, so the rest of the field guide makes sense in your bones.

## What you need before you start

- A Salesforce org with the Industries BRE enabled. This appears in Setup under "Business Rules Engine". If you do not see it, open a Salesforce support case.
- Salesforce CLI version 2.x or later. Check with `sf --version`.
- A code editor. Anything that handles XML and JSON is fine.
- Confirm your org has native BRE (not legacy OmniStudio). Run this query:

```bash
sf data query -o <ORG> --use-tooling-api --query "SELECT Id, DeveloperName, MasterLabel FROM ExpressionSetDefinition LIMIT 5"
```

If this returns results, you have native BRE. If it returns an error, you may have legacy OmniStudio or no BRE at all.

## Step 1. Create a Decision Matrix

A Decision Matrix does a single-row exact-match lookup.

1. In Setup, search for "Decision Matrices" under the Business Rules Engine section.
2. Click New.
3. Name it `<ORG>_AutoDecisionTier`.
4. Add input columns:
   - `Region` (Text)
   - `CompanyScore` (Number)
   - `IndividualScore` (Number)
5. Add output columns:
   - `Outcome` (Text)
   - `MAFactor` (Number, scale 4)
   - `ReasonCode` (Text)
6. Add rows:

| Region | CompanyScore | IndividualScore | Outcome | MAFactor | ReasonCode |
|--------|-------------|----------------|---------|---------|-----------|
| AU | 700-999 | 700-999 | Approved | 1.0000 | |
| AU | 600-699 | 600-699 | ConditionallyApproved | 0.8000 | COND_MEDIUM_SCORE |
| AU | 0-599 | 0-999 | Referred | 0.0000 | REF_LOW_COMPANY_SCORE |
| NZ | 700-999 | 700-999 | Approved | 1.0000 | |
| NZ | 0-699 | 0-999 | Referred | 0.0000 | REF_LOW_SCORE |

7. Click Activate.

## Step 2. Create an Expression Set

An Expression Set orchestrates the matrices and does the final calculation.

1. In Setup, search for "Expression Sets" under the Business Rules Engine section.
2. Click New.
3. Name it `<ORG>_AutoDecision`.
4. Add an input variable for each value the Expression Set will receive:
   - `Region` (Text)
   - `CompanyScore` (Number)
   - `IndividualScore` (Number)
5. Add an output variable:
   - `FinalOutcome` (Text)
   - `ComputedMA` (Number)
   - `ReasonCode` (Text)
6. Add a step: Decision Matrix Lookup. Select `<ORG>_AutoDecisionTier` as the matrix. Map inputs and outputs.
7. Click Save, then Activate.

## Step 3. Write the Apex caller

Create `force-app/main/default/classes/SalesforceBREClient.cls`:

```apex
public with sharing class SalesforceBREClient implements Callable {
    public Object call(String action, Map<String, Object> input, Map<String, Object> options) {
        if (action != 'runBRE') {
            throw new BRECallException('Unsupported action: ' + action);
        }

        // Build the BRE input from the caller's payload
        Map<String, Object> breInput = new Map<String, Object>{
            'Region' => (String) input.get('region'),
            'CompanyScore' => (Decimal) input.get('companyScore'),
            'IndividualScore' => (Decimal) input.get('individualScore')
        };

        // Call the Expression Set via the BRE API
        Map<String, Object> result = callExpressionSet(
            '<ORG>_AutoDecision',
            breInput
        );

        return result;
    }

    private Map<String, Object> callExpressionSet(String expressionSetName, Map<String, Object> input) {
        // REST API call to BRE Expression Set
        // In practice, use the Connect API endpoint:
        // POST /services/data/v59.0/async/expressionsets/{name}/invoke
        HttpRequest req = new HttpRequest();
        req.setEndpoint(Url.getOrgDomainUrl().toExternalForm()
            + '/services/data/v59.0/async/expressionsets/'
            + expressionSetName + '/invoke');
        req.setMethod('POST');
        req.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
        req.setHeader('Content-Type', 'application/json');
        req.setBody(JSON.serialize(input));

        Http h = new Http();
        HttpResponse res = h.send(req);

        if (res.getStatusCode() != 200) {
            throw new BRECallException('BRE call failed: ' + res.getBody());
        }

        return (Map<String, Object>) JSON.deserializeUntyped(res.getBody());
    }
}

public class BRECallException extends Exception {}
```

This is the pattern. The real implementation uses the BRE REST API or an invocable method. The key point is that Apex builds the input, calls the Expression Set, and receives the output.

## Step 4. Test with simulation

1. Open the Expression Set in the BRE UI.
2. Click Simulate.
3. Enter Region: AU, CompanyScore: 750, IndividualScore: 800.
4. Click Evaluate.
5. Confirm the output shows the Approved row from the matrix.

## Step 5. Test the Apex caller

Write a simple test class that calls `SalesforceBREClient` with known inputs and asserts known outputs.

## What comes next

Read Chapter 1 (The Mental Model) to understand why native BRE is structured the way it is, and Chapter 4 (Starting Checklist) before you build your first real Expression Set.

## References

- [Salesforce Industries Developer Guide: Business Rules Engine](https://developer.salesforce.com/docs/atlas.en-us.industries_reference.meta/industries_reference/business_rules_engine.htm)
- [Salesforce Help: Expression Sets](https://help.salesforce.com/s/articleView?id=ind.expression_sets.htm&language=en_US&type=5)
- [Salesforce Developer Blog: Connect API for BRE Expression Sets](https://developer.salesforce.com/blog)
