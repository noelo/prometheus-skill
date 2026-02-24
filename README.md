# Prometheus Skill

A collection of skills and configurations for working with Prometheus monitoring, including PromQL query generation and Thanos query layer deployment.

## Contents

- [promql-skill](./promql-skill/promql-skill/SKILL.md) - A skill for generating Prometheus Query Language (PromQL) queries from natural language descriptions

## PromQL Skill

The PromQL skill helps you:

- Create PromQL queries from requirements
- Explain or debug existing queries
- Learn Prometheus metrics, functions, and best practices
- Optimize query performance

### Key Features

- Prometheus data model overview (metrics, time series, samples)
- Metric types: Counter, Gauge, Histogram, Summary
- PromQL syntax: Vector types, label matchers, time specifications
- Operations: Arithmetic, comparisons, logical/set, aggregations
- Key functions: rate/change, aggregation over time, histograms, prediction
- Best practices and common query patterns

### Reference

See [examples.md](./promql-skill/promql-skill/references/examples.md) for common PromQL query examples including:

- Basic queries and filtering
- Rate calculations
- Aggregations
- Error rates
- Latency percentiles
- Resource usage
- Availability monitoring

## Thanos Query Configuration

The repository includes a Kubernetes 

- deployment configuration for Thanos Querier in [thanos-q2.yaml](./thanos-q2.yaml).
- service configuration for Thanos Querier in [thanos-querier-svc.yaml](./thanos-querier-svc.yaml).

This deployment:
- Runs Thanos Querier for Prometheus query federation
- Configures gRPC with TLS for secure communication
- Connects to Prometheus via DNS service discovery
- Includes resource limits and security best practices

[CAUTION] This querier has no auth or access control enabled. Do not use this in a production environment


# Uses [claude-skills-mcp](https://github.com/noelo/claude-skills-mcp) to serve the skills

## Configure the skill

```JSON
  config.json: |-
    {
      "skill_sources": [
        {
          "type": "github",
          "url": "https://github.com/noelo/prometheus-skill",
          "comment": "Prometheus query generation"
        },
```

# Configure the Prometheus MCP Server

```
oc apply -f prometheus-mcp-server.yml
```

## Configure the MCP server to connect to the Custom Thanos Querier

Set the _PROMETHEUS_URL_ environment variable to connect to the service 


