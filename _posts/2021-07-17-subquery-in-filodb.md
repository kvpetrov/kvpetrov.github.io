## Introducing Subquery in FiloDB

Subquery was introduced in Prometheus 2.7 and we started implementing the feature in FiloDB in 2020. The current integration branch is mostly feature complete though some of its functionality depends on the new ANTLR based parser. In this post I will describe:
* the feature itself
* motivation to implement subquery in FiloDB
* limitations of the approach.

---

### What is subquery
In its essence subquery allows to run a PromQL expression multiple times and generate a range vector with an output of each individual expression.
Imagine you have a simple query 
```
sum(rate(Counter0{_ws_="aci-telemetry", _ns_="Card-5k-MCP-EAST-0"}[1m]))
```
If you invoke such a query with a range query API given a particular start/end/step over grafana, you would see a graph showing a per minute rate of Counter0. You can eyeball max min and can even approximate an average, however, if you want the exact number computed and presented to you, you are out of luck if subquery feature is not available for you.
Pre-2019 PromQL allows one to run functions on the range vectors similar to the one we just generated but ...

### Motivation
Very often subqueries are used for alerting when we cannot use the metrics as is but need to transform it into smoother version of itself.

### Limitations
#### Performance
The most straitforward way to implemenet subquery is to run multiple simple queries exactly as many times are there are steps in the subquery lookback.

```
for (int i=start; i<end; i+=step) {
  result += run_query(subquery, i)
}
return result
```
#### Unimplemented

quantile_over_time function is not implemented yet.
