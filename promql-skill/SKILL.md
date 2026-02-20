---
name: promql-skill
description: >
  Generates Prometheus Query Language (PromQL) queries from natural language descriptions.
  Use when the user wants to: (1) Create PromQL queries from requirements, (2) Explain or debug existing queries,
  (3) Learn Prometheus metrics, functions, and best practices, or (4) Optimize query performance.
---

# PromQL Query Generation

Generate syntactically correct PromQL queries from natural language descriptions. Explain the generated queries and how they address the user's requirements. Offer alternative queries when appropriate, with explanations of tradeoffs. Help debug and refine existing PromQL queries. Provide contextual information about Prometheus metrics, functions, and best practices.

## Prometheus Data Model

**Metrics**: Named measurements with optional HELP and TYPE

**Time Series**: Metrics with unique label combinations

**Samples**: Tuples of (timestamp, value) for each time series

### Metric Types

| Type | Description | Naming Convention |
|------|-------------|-------------------|
| Counter | Monotonically increasing values | `_total` suffix |
| Gauge | Values that can go up or down | No specific suffix |
| Histogram | Observations bucketed by values | `_bucket`, `_sum`, `_count` suffixes |
| Summaries | Pre-computed quantiles | Custom suffixes |

## PromQL Syntax

### Vector Types

- **Instant Vector**: Single most recent sample per time series
- **Range Vector**: Multiple samples over time, specified with `[duration]` syntax
- **Scalar**: Single numeric value
- **String**: Single string value (rarely used)

### Label Matchers

| Operator | Type | Example |
|----------|------|---------|
| `=` | Exact match | `{job="service"}` |
| `!=` | Negative match | `{job!="service"}` |
| `=~` | Regex match | `{job=~"api.*"}` |
| `!~` | Negative regex | `{job!~"test.*"}` |

### Time Specifications

Valid units: `ms`, `s`, `m`, `h`, `d`, `w`, `y`

- Range vectors: `metric[5m]`
- Offset modifier: `metric offset 1h`
- Subqueries: `function(metric[5m])[1h:10m]`

### Operations

**Arithmetic**: `+`, `-`, `*`, `/`, `%`, `^`

**Comparisons**: `==`, `!=`, `>`, `<`, `>=`, `<=`

**Logical/Set**: `and`, `or`, `unless`

**Aggregations**: `sum`, `avg`, `min`, `max`, `count`, `stddev`, `stdvar`, `topk`, `bottomk`, `count_values`

**Group modifiers**: `by`, `without`

**Vector matching**: `on`, `ignoring`, `group_left`, `group_right`

### Key Functions

| Category | Functions |
|----------|-----------|
| Rate/change | `rate()`, `irate()`, `increase()`, `changes()`, `delta()` |
| Aggregation over time | `<aggr>_over_time()` |
| Histograms | `histogram_quantile()` |
| Prediction | `predict_linear()`, `deriv()` |
| Resets/changes | `resets()`, `changes()` |

## Best Practices

1. **Use rate() for counters**: Always use `rate()` when working with counters
   ```promql
   rate(http_requests_total[5m])
   ```

2. **Choose appropriate time windows**:
   - Too short: Insufficient data points
   - Too long: Averaging out spikes
   - Consider scrape interval and alerting needs

3. **Label cardinality awareness**: Be careful with high cardinality label combinations

4. **Subquery resolution**: Specify appropriate resolution
   ```promql
   max_over_time(rate(http_requests_total[5m])[1h:1m])
   ```

5. **Staleness handling**: Be aware of the 5-minute staleness window

6. **Avoid unnecessary complexity**: Use the simplest query that meets requirements

## Common Query Patterns

### Request Rate
```promql
rate(http_requests_total{job="service"}[5m])
```

### Error Rate
```promql
sum(rate(http_requests_total{job="service", status=~"5.."}[5m])) / sum(rate(http_requests_total{job="service"}[5m]))
```

### Latency Percentiles
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket{job="service"}[5m])) by (le))
```

### Resource Usage
```promql
sum(container_memory_usage_bytes{namespace="production"}) by (pod)
```

### Availability
```promql
sum(up{job="service"}) / count(up{job="service"})
```

For more examples, see [examples.md](references/examples.md).

## Response Format

For each query request, include:

1. **PromQL Query**: The complete, executable query
2. **Explanation**: How the query works and addresses the requirement
3. **Assumptions**: Any assumptions made about metrics or environment
4. **Alternatives**: Alternative approaches when relevant
5. **Limitations**: Note any limitations of the proposed query

## Advanced Patterns

### Service Level Objectives (SLOs)

- Error budgets
- Burn rate calculations
- Multi-window alerting

### Capacity Planning

- Growth prediction
- Trend analysis
- Saturation metrics

### Comparative Analysis

- Current vs historical performance
- A/B testing support
- Cross-environment comparison

## Important Notes

Remember that PromQL is designed for time series data and operates on a pull-based model with periodic scraping. Account for these characteristics when designing queries.
