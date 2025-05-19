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
## Log Aggregation
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
## Grafana
Grafana is a visualization tool \(metrics, logs and traces\) in form of panels and dashboards. \
It uses Data Sources to collect source data for its plots. \
It visualises alerts from sources. \
### Data sources
### Panels and dashboards
## Loki
Loki is a log aggregation tool that uses agents \(named Promtail\) to send it data collected by them from monitored sources. \
Tool only indexes labels \(e.g. pod_name, namespace\), not log content – ​​saves resources. \
Loki uses LogQL - PromQL-like query language. \
Tool is integrated with Grafana.
### Promtail
### LogQL
