---
title: Request JSON schema
kind: documentation
---


Depending of what you want to graph the `requests` parameter takes different values: 

{{< tabs >}}
{{% tab "Metric Query" %}}

```
{
  "requests" : [
    "q": "<METRIC_QUERY>"
  ]
}
```

{{% /tab %}}
{{% tab "APM Query" %}}

```
{
  "requests" : [
    "apm_query": "<APM_QUERY>"
  ]
}
```

{{% /tab %}}
{{% tab "Log Query" %}}

```
{
  "requests" : [
    "log_query": "<APM_QUERY>"
  ]
}
```

{{% /tab %}}
{{< /tabs >}}

The general format for a series is:

```json
"requests": [
  {
    "q": "function(aggregation method:metric{scope} [by {group}])"
  }
]
```

The `function` and `group` are optional.

A Series can be further combined together via binary operators (+, -, /, *):

```
metric{scope} [by {group}] operator metric{scope} [by {group}]
```

#### Functions

You can apply functions to the result of each query.

A few of these functions have been further explained in a series of examples. Visit this page for more detail: [Examples][1]

#### Aggregation Method

In most cases, the number of data points available outnumbers the maximum number that can be shown on screen. To overcome this, the data is aggregated using one of 4 available methods: average,  max, min, and sum.

#### Metrics

The metric is the main focus of the graph. You can find the list of metrics available to you in the [Metrics Summary][2]. Click on any metric to see more detail about that metric, including the type of data collected, units, tags, hosts, and more.

## Scope

A scope lets you filter a Series. It can be a host, a device on a host
or any arbitrary tag you can think of that contains only alphanumeric
characters plus colons and underscores (`[a-zA-Z0-9:_]+`).

Examples of scope (meaning in parentheses):

```
host:my_host                      (related to a given host)
host:my_host, device:my_device    (related to a given device on a given host)
source:my_source                  (related to a given source)
my_tag                            (related to a tagged group of hosts)
my:tag                            (same)
*                                 (wildcard for everything)
```

#### Groups

For any given metric, data may come from a number of hosts. The data is normally aggregated from all these hosts to a single value for each time slot. If you wish to split this out, you can by any tag. To include a data point separated out by each host, use {host} for your group.

#### Arithmetic

You can apply simple arithmetic to a Series (+, -, * and /). In this
example we graph 5-minute load and its double:

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "system.load.5{intake} * 2"
    },
    {
      "q": "system.load.5{intake}"
    }
  ]
}
```

You can also add, substract, multiply and divide a Series. Beware that
Datadog does not enforce consistency at this point so you *can* divide
apples by oranges.

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "metric{apples} / metric{oranges}"
    }
  ]
}
```


## Stacked Series

{{< img src="graphing/graphing_json/slice-n-stack.png" alt="slice and stack" responsive="true" >}}

In the case of related Time Series, you can easily draw them as stacked areas by using the following syntax:

```json
"requests": [
  {
    "q": "metric1{scope}, metric2{scope}, metric3{scope}"
  }
]
```

Instead of one query per chart you can aggregate all queries into one and concatenate the queries.

## Slice-n-Stack

A useful visualization is to represent a metric shared across
hosts and stack the results. For instance, when selecting a tag that
applies to more than 1 host you see that ingress and egress traffic is nicely stacked to give you the sum as well as the split per host. This is useful to spot wild swings in the distribution of network traffic.

Here's how to do it for any metric:

```json
"requests" [
  {
    "q": "system.net.bytes_rcvd{some_tag, device:eth0} by {host}"
  }
]
```

Note that in this case you can only have 1 query. But you can also split by device, or a combination of both:

```json
"requests" [
  {
    "q": "system.net.bytes_rcvd{some_tag} by {host,device}"
  }
]
```

to get traffic for all the tagged hosts, split by host and network device.

#### Examples

Here is an example using the `rate()` function, which takes only a single metric as a parameter.  Other functions, with the exception of `top()` and `top_offset()`, have identical syntax.

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "rate(sum:system.load.5{role:intake-backend2} by {host})",
      "stacked": false
    }
  ]
}
```

Here is an example using the `top()` function:

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "top(avg:system.cpu.iowait{*} by {host}, 5, 'max', 'desc')",
      "stacked": false
    }
  ]
}
```

This shows the graphs for the five series with the highest peak `system.cpu.iowait` values in the query window.

To look at the hosts with the 6th through 10th highest values (for example), use `top_offset` instead:

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "top_offset(avg:system.cpu.iowait{*} by {host}, 5, 'max', 'desc', 5)",
      "stacked": false
    }
  ]
}
```

Here is an example using the `week_before()` function:

```json
{
  "viz": "timeseries",
  "requests": [
    {
      "q": "sum:haproxy.count_per_status{status:available} - week_before(sum:haproxy.count_per_status{status:available})"
    }
  ]
}
```

## APM / Log query

```
_APM_OR_LOG_QUERY = {
    "type": "object",
    "properties": {
        "index":    {"type": "string"},
        "compute":  {
            "type": "object",
            "properties": {
                "aggregation": {"type": "string"},
                "facet":       {"type": "string"},
                "interval":    {"type": "integer"}
            },
            "required": ["aggregation"],
            "additionalProperties": False
        },
        "search":   {
            "type": "object",
            "properties": {
                "query": {"type": "string"}
            },
            "required": ["query"],
            "additionalProperties": False
        },
        "group_by": {
            "type":  "array",
            "items": {
                "type": "object",
                "properties": {
                    "facet": {"type": "string"},
                    "limit": {"type": "integer"},
                    "sort":  {
                        "type": "object",
                        "properties": {
                            "aggregation": {"type": "string"},
                            "order":       {"enum": ["asc", "desc"]},
                            "facet":       {"type": "string"}
                        },
                        "required": ["aggregation", "order"],
                        "additionalProperties": False
                    }
                },
                "required": ["facet"],
                "additionalProperties": False
            }
        }
    },
    "required": ["index", "compute"],
    "additionalProperties": False
}
```
[1]: /graphing/functions
[2]: https://app.datadoghq.com/metric/summary