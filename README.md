# Monitoring-Observability-Log-Aggregation-DevOps-Essentials
## If I'll find any other feature to be useful - it'll surely be added!
## What is it?
Monitoring is a systematic collection and analysis of data \(e.g. metrics\) in real time to assess the health of infrastructure, applications and services. It's purpose is detection of occurring issues \(e.g. server crashes, CPU limit exceeded\) and rapid response. Example of Monitoring tool is Prometheus.

Observability is an ability to understand the state of a system based on its outputs: metrics, logs and traces. It's purpose is complex debugging of systems and applications. Example of a tool linked to observability is Grafana - visualization tool that allows for correlating data.

Log Aggregation is a centralization and processing of logs from various sources \(servers, applications, containers\) in one place. It's purpose is improving analysis, search, and event correlation \(e.g. tracking transactions across microservices\). Example of Log Aggregation tool is Loki.
## Metrics
Metrics are numerical data collected in real time, describing the state of the system \(e.g. CPU usage, number of HTTP requests, response time\). 

They allow for detecting trends, anomalies and problems \(e.g. downtime, overloads\) and the basis for defining alerts and SLAs \(Service Level Agreements\).
### Golden Signals
Golden Signals are key metrics recommended by Google SRE for monitoring any system:
* **Latency** - request handling time
* **Traffic** - system load \(e.g. number of requests per second\)
* **Errors** - number of failed operations
* **Saturation** - resource utilization \(e.g. CPU, memory\)

They allow for quickly assessing the health of your system and are universal for all systems.
## Alerts
Alerts are automatic notifications triggered when the system detects an anomaly \(e.g. high error rate, resource overload\) to quickly respond to issues before they impact end users.

See how they are managed in different tools in below tools chapters.
## SLA/SLO/SLI
## Prometheus
Prometheus is a monitoring tool that scrapes metrics from applications and infrastructure by HTTP requests \(e.g. from ```/metrics``` endpoint\). \
Tool uses Time-Series Database \(TSDB\) that stores data in time series format \(e.g. http_requests_total{status="200"} 1500 @ timestamp\). \
Metrics are labeled \(e.g. instance, job, env\), which allows filtering and grouping. \
Prometheus uses PromQL - Metrics Analysis Query Language. \
To export data from monitored resources tool uses Exporters. \
To manage alerts Prometheus uses Alertmanager.
### Exporters
Exporters are special tools \(or libraries\) that collect metrics from systems, applications or protocols that do not support the native Prometheus format, and expose them in a format that Prometheus understands \(OpenMetrics\). \
Examples of data sources:
* operating systems \(e.g. CPU, disk usage\)
* databases \(e.g. PostgreSQL, Redis\)
* cloud services \(e.g. AWS S3\)
* protocols \(e.g. HTTP, ICMP\)

First, Exporters collect the data, then transform it into Prometheus format, then make it available via an HTTP endpoint for periodic scraping by Prometheus.

There are many ready-made exporters \(e.g. Node Exporter for OS metrics, Blackbox Exporter for monitoring services availability\) but you can create your own exporter using Prometheus libraries. Check for ready-made exporters [here](https://prometheus.io/docs/instrumenting/exporters/).
### PromQL
PromQL is a query language used in Prometheus to retrieve time series metrics, transform and aggregate data \(e.g., calculate averages, sums, percentiles\) and create alerts and dashboards \(e.g., in Grafana\).

Metrics are used in form name + labels \(e.g. ```http_requests_total{status=<status>, path=<path>}```\). Labels can be used with logic operators \(e.g. ```!=, =~```\). \
For metrics data, a time interval from which data is to be retrieved can be specified \(e.g. ```rate(http_requests_total[5m])``` - average requests per second over the last 5 minutes\). \
Metrics can be of types:
* **Counter** - growing value \(e.g. number of requests\)
* **Gauge** - momentary value \(e.g. CPU usage\)
* **Histogram/Summary** - distribution of values ​\(e.g. response time\)

Most important functions and operators:
* **rate()** - calculates the increment per second for counters \(e.g. requests/sec\)
* **increase()** - calculates the total growth in a given time window
* **sum() by()** - aggregates values ​​and groups by labels
* **histogram_quantile()** - calculates percentiles for histograms \(e.g. \<x\>th percentile of response time\)
* **avg(), min(), max()** - basic statistical functions
* **time(), timestamp()** - current time in seconds and samples timestamp

#### Best practises
* **Avoid ```rate()``` on gauges: ```rate()``` is for counters - use ```delta()``` for gauges**
* **Optimize time ranges** - too short window \(e.g. [1m]\) → noise in the data, too long window \(e.g. [1h]\) → delays in detecting anomalies
* **Optimize labels** - redundant labels slow down queries
### Alerts
Alerts are defined in ```prometheus.yml```. If condition is fulfilled, alert is sent to Alertmanager.

Example alert:
```
groups:
- name: <group_name>
  rules:
  - alert: <alert_name>
    expr: <promql_query>
    for: <time>  # Alert is triggered after <time>
    labels:
      severity: <severity>
    annotations:
      summary: <summary>
      description: <description>
```
### Alertmanager
Alertmanager is a tool that manages alerts in Prometheus.

Alerts can be grouped by combining related alerts into one message \(e.g. 100 high CPU alerts in a cluster → one notification\). \
Alerts can be inhibited when another, more important alert is active \(e.g. ignore CPU alerts if the entire cluster is offline\). \
Alerts can be silenced temporarily \(e.g. during planned maintenance\). \
Alert notifications can be routed to different receivers based on labels \(e.g. team=backend, env=prod\).

Example Alertmanager configuration:
```
global: # Global settings
  <key>: <value>

route:
  group_by: [<labels>] # Grouping by labels
  group_wait: <time> # Wait <time> to collect related alerts
  group_interval: <time> # Grouped Alert Sending Frequency
  receiver: <receiver_name_1>
  routes:
  - match:
      severity: <severity>
    receiver: <receiver_name_2>

receivers:
- name: <receiver_name_1>
  <receiver_configs>:
  - <key>: <value>
- name: <receiver_name_2>
  <receiver_configs>:
  - <key>: <value>

inhibit_rules:
- source_match: # Alert that invokes inhibition
    severity: <source_severity>
  target_match: # Alerts to inhibit
    severity: <target_severity>        
  equal: [<labels>]
```
To apply configuration in Prometheus add below to ```prometheus.yml```:
```
alerting:
  alertmanagers:
  - static_configs:
      - targets: [<alertmanager_endpoint>]
```
## Grafana
Grafana is a visualization tool \(metrics, logs and traces\) in form of panels and dashboards. \
It uses Data Sources to collect source data for its plots. \
It visualises alerts from sources. \
### Data sources
Data sources are connections to an external data storage system \(e.g. Prometheus, Loki, database\) from which Grafana retrieves information for visualization on dashboards.

To define data source use:
```
Type: <type>
URL: <data_source_url>
Access: <access>
Auth: <auth> # If needed
```
### Panels and dashboards
Panels are single dashboard components \(e.g. chart, table, statistics\) that displays data based on a query to the source. \
Dashboards are sets of panels that visualize data from various sources \(Prometheus, Loki, databases, etc.\).

Panels can be of types:
* **Time Series** - visualization of metrics over time \(e.g. resource usage, number of requests\)
* **Stat** - displaying a single value \(e.g. current number of errors, average response time\)
* **Table** - table with structured data \(e.g. SQL query results\)
* **Heatmap** - visualization of the distribution of values ​​\(e.g. response time percentiles\)
* **Logs** - displaying logs \(from Loki, Elasticsearch, etc.\) with filtering options
### Alerts
Alerts can be based on any data source and be sent directly from Grafana \(without Alertmanager\).

Define alerts in panel.
## Loki
Loki is a log aggregation tool that uses agents \(named Promtail\) to send it data collected by them from monitored sources. \
Tool only indexes labels \(e.g. pod_name, namespace\), not log content – ​​saves resources. \
Loki uses LogQL - PromQL-like query language. \
Tool is integrated with Grafana.
### Promtail
### LogQL
### Alerts
Loki can send alerts to Alertmanager when it detects defined patterns in logs. Component that periodically evaluates rules is named Loki Ruler.

Example rule:
```
groups:
  - name: <group_name>
    rules:
      - alert: <alert_name>
        expr: <logql_query>
        for: <time>  # Alert is triggered after <time>
        labels:
          severity: <severity>
        annotations:
          summary: <summary>
          description: <description>
```
To integrate Loki with Alertmanager use:
```
ruler:
  alertmanager_url: <alertmanager_endpoint>
  external_url: <loki_endpoint>
  rule_path: <path_to_rules>
```
