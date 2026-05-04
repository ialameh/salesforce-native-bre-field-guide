# 16. FAQ

Short answers to the questions that come up most often in native BRE projects.

## Q: BRE is not visible in my org. How do I enable it?

Open a Salesforce support case and ask them to enable the Salesforce Industries Business Rules Engine license for your org. It is not visible by default in all org editions. Developer Edition orgs have it automatically when the Industries cloud is enabled.

## Q: Can I invoke a Decision Matrix directly from Apex without an Expression Set?

Not directly. Expression Sets are the invocation target. Put the matrix inside an Expression Set and invoke the Expression Set.

## Q: Can BRE listen to record changes?

No. BRE does not have a trigger model. Use a Flow trigger, Platform Event, or Apex trigger to detect record changes, and have that caller invoke BRE.

## Q: The rule works in simulation but not in production. Why?

Three likely causes:
1. The draft is not activated. Production runs the old active version.
2. The Apex caller passes the wrong field names in the input map.
3. The calling user does not have the BRE execution permission set.

See Chapter 5 (Troubleshooting) for the full diagnosis path.

## Q: How do I call BRE from Apex?

Use the BRE REST API:

```apex
HttpRequest req = new HttpRequest();
req.setEndpoint(Url.getOrgDomainUrl().toExternalForm()
    + '/services/data/v59.0/async/expressionsets/<Name>/invoke');
req.setMethod('POST');
req.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
req.setHeader('Content-Type', 'application/json');
req.setBody(JSON.serialize(inputMap));
HttpResponse res = new Http().send(req);
```

## Q: Can I use BRE with Salesforce Flow?

Yes. BRE Action elements are available in Flow Builder. The action element passes inputs and receives outputs the same way as Apex.

## Q: How do I version-control my BRE rules?

Retrieve with `sf project retrieve start -m ExpressionSetDefinition` and `sf project retrieve start -m DecisionMatrixDefinition`. Commit the files to git.

## Q: How do I roll back a rule?

Open the rule in the BRE UI, go to the Versions panel, and activate the previous version. No deployment needed.

## Q: My Decision Table has 500,000 rows. Is that a problem?

Yes. Performance degrades above 100,000 rows. Split the table or move some logic to Apex.

## Q: Can one Expression Set call another?

Yes. The nesting limit is 3 levels. If you need more than 3 levels, chain them sequentially in the Apex caller.

## Q: How is native BRE different from legacy OmniStudio BRE?

Native BRE uses `ExpressionSetDefinition` and `DecisionMatrixDefinition`. Legacy OmniStudio uses `vlocity_cmt.IntegrationProcedureService` and `vlocity_cmt__DRMatrix__c`. The metadata types, invocation methods, and runtime are completely different. Do not mix them.

## Q: Can community users run BRE rules?

Yes, if they are assigned a BRE execution permission set. They do not need the Rule Engine Designer permission. They also need read access to the data the rules operate on.
