# 15. Performance and Limits

Native BRE has hard limits set by Salesforce. This chapter documents both the enforced limits and the performance-affecting thresholds.

## Documented limits

| Limit | Value |
|-------|-------|
| Expression Set nesting depth | 3 levels |
| Decision Table input columns | 30 |
| Decision Table output columns | 5 |
| Decision Table rows | 1,000,000 |

## Performance-affecting thresholds

| Scenario | Threshold | Effect beyond threshold |
|----------|-----------|------------------------|
| Decision Table rows | 100,000 | Noticeable latency increase |
| Expression Set steps | 50 | Slower, harder to debug |
| BRE API call duration | 500ms | Governor limit pressure |

## What causes slow rules

### Large Decision Tables

The primary cause of BRE performance problems is Decision Tables with too many rows. The lookup scans rows until it finds a match. If a table has 500,000 rows, the scan is slow.

### Nested Expression Sets

Each level of nesting adds overhead. Three levels of nesting at 50 steps each means 150 evaluations. A sequential chain in the Apex caller does the same work without the nesting overhead.

### Repeated invocations in a loop

If Apex calls BRE inside a loop, each invocation counts toward the transaction's governor limits. Bulk-design: call BRE once with a collection, not N times with individual values.

## Bulk design for BRE

Collect all input values for all records. Call BRE once with the collection. Receive the collection of results. Distribute results back to each record.

This requires that the Expression Set supports collection inputs. Most do. The specific configuration depends on the rule design.

## Monitoring in production

Use the Execution Overview in Setup to check which rules are being called and how often. Set up a dashboard that shows average execution time per rule per day.

## References

- [Salesforce Help: Business Rules Engine Default Limits](https://help.salesforce.com/s/articleView?id=ind.business_rules_engine_default_limits.htm&language=en_US&type=5)
- [Salesforce Help: Decision Table Data Limits](https://help.salesforce.com/s/articleView?id=ind.reference_decision_table_data_limits.htm&language=en_US&type=5)
