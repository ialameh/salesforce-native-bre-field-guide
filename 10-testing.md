# 10. Testing

The testing strategy for native BRE has three layers. Each layer catches different kinds of problems.

## Layer 1: Simulation

Simulation runs inside the BRE UI against the draft. It is the fastest test and requires no code.

Run simulation for every input combination that maps to a business scenario, plus edge cases: empty, zero, maximum, minimum, and no-match.

Simulation tests only the rule definition, not the Apex caller.

## Layer 2: Apex unit test

Call the Apex BRE client from a test method. This tests the full call chain from Apex to Expression Set to response parsing.

```apex
@IsTest
static void testAutoDecision_Approved() {
    Test.startTest();

    Map<String, Object> input = new Map<String, Object>{
        'region' => 'AU',
        'companyScore' => 750,
        'individualScore' => 800
    };

    Map<String, Object> result = SalesforceBREClient.call('runBRE', input, null);

    Test.stopTest();

    System.assertEquals('Approved', result.get('outcome'));
}
```

This layer catches input mapping errors, response parsing errors, and permission issues.

## Layer 3: Integration test

Exercise the full flow from trigger or UI through to record update. This layer catches issues that only appear under load or with real data.

## Test data requirements

BRE rules do not require special test data beyond the rows you define in the matrix or table. The rule reads its own rows.

However, the Apex caller may query Salesforce data to build the input map. Ensure your test data includes the records the caller needs.

## What BRE testing cannot cover

Simulation cannot detect performance problems under load, permission issues for specific profiles, or concurrency issues. These require integration testing and profile-based testing.

## References

- [Salesforce Developer Documentation: Testing Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing.htm)
