## Availability SLI
### The percentage of successful requests over the last 5m

```
sum (rate(prometheus_http_requests_total{code=~"2.*"}[5m]))/ sum (rate(prometheus_http_requests_total{}[5m]))
```

## Latency SLI
### 90% of requests finish in these times

```
histogram_quantile(0.95, sum(rate(prometheus_http_request_duration_seconds_bucket[5m])) by (le, verb)) 
```

## Throughput
### Successful requests per second

```
sum(rate(prometheus_http_requests_total{code=~"2.."}[5m]))
```

## Error Budget - Remaining Error Budget
### Goal of 80% remaining error error budget

```
1 - ((1 - (sum(increase(prometheus_http_requests_total{code=~"2.*"}[7d])) by (verb)) /  sum(increase(prometheus_http_requests_total{}[7d])) by (verb)) / (1 - .80))
```
