# PromQL Examples

This file contains common PromQL query examples for reference.

## Basic Queries

### Get all values of a metric
```promql
http_requests_total
```

### Filter by label
```promql
http_requests_total{job="api", status="200"}
```

### Multiple label filters
```promql
http_requests_total{job="api", status=~"2..", environment="production"}
```

### Count time series
```promql
count(http_requests_total)
```

## Rate Calculations

### Requests per second
```promql
rate(http_requests_total[5m])
```

### Requests with offset
```promql
rate(http_requests_total[5m] offset 1h)
```

### Comparing to previous period
```promql
rate(http_requests_total[5m]) / rate(http_requests_total[5m] offset 1w)
```

## Aggregations

### Sum all series
```promql
sum(http_requests_total)
```

### Sum by job
```promql
sum by (job) (http_requests_total)
```

### Sum excluding job
```promql
sum without (job) (http_requests_total)
```

### Average over time
```promql
avg_over_time(http_requests_total[5m])
```

### Maximum over time
```promql
max_over_time(cpu_usage[10m])
```

## Error Rates

### Error rate percentage
```promql
sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100
```

### Error count by status
```promql
sum by (status) (rate(http_requests_total{status=~"5.."}[5m]))
```

## Latency

### 50th percentile
```promql
histogram_quantile(0.50, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

### 90th percentile
```promql
histogram_quantile(0.90, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

### 99th percentile
```promql
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

### Average latency
```promql
rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])
```

## Resource Usage

### Memory by pod
```promql
sum by (pod) (container_memory_usage_bytes)
```

### CPU by namespace
```promql
sum by (namespace) (rate(container_cpu_usage_seconds_total[5m]))
```

### Disk usage
```promql
disk_usage_bytes / disk_total_bytes * 100
```

## Availability

### Uptime percentage
```promql
sum(up{job="my-service"}) / count(up{job="my-service"}) * 100
```

### Instances up vs down
```promql
sum(up{job="my-service"}) by (instance)
```

## Changes and Deltas

### Increase over 5 minutes
```promql
increase(http_requests_total[5m])
```

### Changes in value
```promql
changes(process_resident_memory_bytes[5m])
```

### Number of resets
```promql
resets(http_connections_total[1h])
```

## Prediction

### Linear prediction
```promql
predict_linear(disk_free_bytes[1h], 4*3600)
```

### Rate of change
```promql
deriv(process_open_fds[5m])
```

## Sorting

### Top 5 by value
```promql
topk(5, http_requests_total)
```

### Bottom 5
```promql
bottomk(5, http_requests_total)
```

## Boolean Operations

### Count errors
```promql
count(http_requests_total > 400)
```

### Conditional alerting
```promql
rate(http_requests_total{status="500"}[5m]) > 0.05
```

## Subqueries

### Max rate over hour with 1-minute resolution
```promql
max_over_time(rate(http_requests_total[5m])[1h:1m])
```

## Counter Rate Patterns

### Correct counter rate
```promql
rate(http_requests_total[5m])
```

### Increase over time window
```promql
increase(http_requests_total[1h])
```

### Irate for fast counters
```promql
irate(http_requests_total[5m])
```

## Gauge Patterns

### Current value
```promql
node_memory_MemAvailable_bytes
```

### Delta from start
```promql
node_memory_MemAvailable_bytes - node_memory_MemAvailable_bytes offset 24h
```

### Min/Max in window
```promql
min_over_time(node_memory_MemAvailable_bytes[1h])
max_over_time(node_memory_MemAvailable_bytes[1h])
