# 11. Observability

Native BRE sits behind the Apex caller layer. This chapter covers what you can see natively and what you need to build yourself.

## What you can see natively

### Execution Overview

In Setup under Business Rules Engine settings, the Execution Overview view shows recent invocations: which Expression Set fired, when, and which user. This is useful for spot-checking that a rule is being called.

The detail level is limited. You see the Expression Set name and timestamp. You do not see inputs, outputs, or duration.

### Debug logs

Enable debug logs for the running user. Set "Callout" to DEBUG to see HTTP details of the BRE REST API call. Set "Apex Code" to DEBUG for the Apex caller logic.

To enable:
1. Open Developer Console for the target user.
2. Click Debug, then Change Log Levels.
3. Add Logger Categories for "Callout" and "Apex Code" set to DEBUG.

Debug logs are verbose. Use them for one-off investigations, not ongoing monitoring.

## What you need to build

### Structured logging

For production monitoring, log each BRE invocation to a custom object. Capture:

- Expression Set name
- Version activated
- Input values (sanitised, no PII)
- Output values (sanitised)
- Timestamp
- Duration (milliseconds)
- Result (success, no_match, error)

This turns BRE from a black box into a queryable dataset.

### Alerting on no-match rates

If a rule that normally returns matches starts returning no-match at a high rate, something changed. Build a report that flags when the no-match rate exceeds a threshold.

## Performance monitoring

BRE contributes to overall transaction time. Measure the time spent in the HTTP call to the BRE API. If the Expression Set lookup takes more than a few hundred milliseconds, investigate the matrix row count.

## References

- [Salesforce Help: BRE Integrations](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_integrations.htm&language=en_US&type=5)
