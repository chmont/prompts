# Azure Client Status Dashboard

## Overview

This project defines a **standard Azure Managed Grafana dashboard template** for monitoring Azure client environments.

Each **client corresponds to a single Azure subscription**. The dashboard provides a **viewer-friendly operational status page** that summarizes environment context, infrastructure health, and alert conditions.

The goal is to create a **consistent dashboard template** that works across multiple client subscriptions while adapting to the telemetry actually available in each environment.

This dashboard is designed for:

Operations engineers
Support teams
Platform administrators

The dashboard focuses on **operational awareness rather than deep troubleshooting**.

Primary questions the dashboard answers:

What environment am I looking at
How large is the environment
Is anything currently broken
Where should I investigate
What recently changed

---

# Discovery Process

Before designing dashboard panels, telemetry availability was analyzed across several client subscriptions.

A discovery query was used to determine which Log Analytics tables contain data.

Query used

```
search *
| where TimeGenerated >= ago(24h)
| summarize Count = count() by _SubscriptionId, $table
| order by _SubscriptionId, Count desc
```

This query identifies all active log tables for each subscription in the last 24 hours.

This discovery step was critical to ensure the dashboard only uses **data sources that actually exist across environments**.

---

# Telemetry Observed Across Subscriptions

## Infrastructure Telemetry (Common)

These tables appear in nearly all subscriptions and are safe to use in the dashboard.

InsightsMetrics
Perf
Heartbeat

These represent infrastructure and platform monitoring data.

### InsightsMetrics

Azure Monitor platform metrics pipeline.

Typical sources:

Azure Monitor Agent
VM Insights
AKS monitoring
Azure platform metrics

Typical usage:

Platform metric trends
Infrastructure activity indicators

---

### Perf

Performance counters collected from monitored machines.

Common metrics include:

CPU usage
Memory usage
Disk activity
Network utilization

Typical usage:

Infrastructure performance panels
Capacity monitoring
Resource utilization

---

### Heartbeat

Agent heartbeat telemetry indicating monitored resources are reporting to Azure Monitor.

Typical usage:

Detect offline machines
Verify monitoring coverage
Monitor agent health

Example insights:

Machines reporting telemetry
Machines missing heartbeats
Last heartbeat timestamp

---

## System Logs (Moderate Coverage)

Event

This table contains Windows or Linux system event logs.

Usage examples:

System warnings
System errors
Event volume trends

However, some environments generate minimal event data.

---

## Application Telemetry (Rare)

Only one subscription contained application telemetry.

Tables observed

AppAvailabilityResults
AppRequests
AppMetrics

These correspond to Application Insights data.

Capabilities

Synthetic availability tests
HTTP request telemetry
Application performance metrics

Because this telemetry is not widely deployed across clients, it **cannot be relied on for the standard dashboard template**.

---

# Subscription Telemetry Map

## Subscription
Shinogi
64ede6fc-b18b-44a9-a687-bb1ee3eca66f

Tables

InsightsMetrics
Perf
Heartbeat
AppMetrics

Environment type

Infrastructure monitoring
Limited application metrics

---

## Subscription
HEALI
80937a4c-8e10-46a3-ab2e-e099988941e7

Tables

InsightsMetrics
Perf
Event
Heartbeat

Environment type

Infrastructure monitoring
System logs present

---

## Subscription
GaineCorp
45490e68-d204-46b9-8efc-4ddf70037bc4

Tables

AppAvailabilityResults
AppMetrics
AppRequests

Environment type

Application Insights telemetry environment

---

## Subscription
Denver Health
7c6b69a6-b304-42a3-9912-0596d84c7e32

Tables

InsightsMetrics
Perf
Heartbeat

Environment type

Infrastructure monitoring only

---

## Subscription
Biogen-Prod
d5d8395f-a77e-4e91-8c2a-98012a664a30

Tables

Perf
InsightsMetrics
Event
Heartbeat

Environment type

Infrastructure monitoring
System logs

---

## Subscription
AzBlue
f58381de-1d89-4b68-a03f-3c9622ed40ea

Tables

InsightsMetrics
Perf
Heartbeat
Event

Environment type

Infrastructure monitoring
System logs

---

## Subscription
Amgen-Prod
ee1a7d09-de14-4727-baa1-dfd5684966c9

Tables

InsightsMetrics
Perf
Heartbeat
Event

Environment type

Infrastructure monitoring
System logs

---

# Metrics That Can Be Used Reliably

These telemetry sources appear across most environments.

## Infrastructure Metrics

CPU usage
Memory usage
Disk usage
Network throughput

Source

Perf

---

## Platform Metrics

Platform metric activity
Infrastructure signal metrics

Source

InsightsMetrics

---

## Monitoring Coverage

Agent health
Machine reporting status
Offline resources

Source

Heartbeat

---

## System Activity

System errors
Event log trends

Source

Event

---

## Azure Environment Inventory

Resource counts
Service counts
Resource inventory

Source

Azure Resource Graph

---

## Alerts

Active alerts
Alert severity counts
Affected resources

Source

Azure Resource Graph

AlertsManagementResources

---

# Metrics That Cannot Be Used Reliably

These depend on Application Insights being deployed.

Availability tests
Request latency
Request error rates
Application throughput

Tables

AppAvailabilityResults
AppRequests
AppMetrics

Because these appear in only one subscription, they should **not be part of the base dashboard template**.

---

# Dashboard Design

The dashboard is organized into rows that progressively move from **context to diagnostics**.

---

# Row 1 — Environment Context

Purpose

Provide quick orientation about the environment.

Panels

Environment Overview (text panel)

Displays

Subscription
Resource groups
Region context

Resources Monitored (stat)

Counts all Azure resources in the subscription.

Services Monitored (stat)

Counts core services such as

VMs
AKS clusters
App Services
Databases
Storage

Last Change (stat)

Shows the most recent deployment or infrastructure change.

---

# Row 2 — Environment Health Summary

Purpose

Provide an immediate health snapshot.

Panels

Critical Alerts (stat)

Active high severity alerts.

Warning Alerts (stat)

Active medium severity alerts.

Machines Reporting (stat)

Machines sending heartbeat telemetry.

Last Heartbeat (stat)

Most recent heartbeat time.

Active Alerts (table)

List of active alerts and affected resources.

---

# Row 3 — Environment Inventory

Purpose

Show infrastructure components being monitored.

Panel

Monitored Resources (table)

Columns

Resource name
Resource type
Resource group
Region

Data source

Azure Resource Graph

---

# Row 4 — Service Activity

Purpose

Show operational system signals.

Panels

Heartbeat activity timeline
System event volume
Metric activity signals

Sources

Heartbeat
Event
InsightsMetrics

---

# Row 5 — Infrastructure Health

Purpose

Monitor infrastructure performance.

Panels

Average VM CPU
Memory utilization
Disk usage
Network throughput

Source

Perf

---

# Row 6 — Capacity and Risk

Purpose

Identify capacity pressure or risk areas.

Panels

CPU headroom
Memory pressure
Top utilization resources

Source

Perf

---

# Row 7 — Change Awareness

Purpose

Correlate health with infrastructure changes.

Panels

Recent changes (table)
Availability vs deployment timeline

Source

Azure Activity Logs

---

# Key Design Principles

Subscription scoped monitoring
Viewer friendly dashboards
Minimal reliance on Grafana variables
Infrastructure telemetry first
Graceful degradation if application telemetry is missing

---

# Future Improvements

Potential future enhancements include:

Application Insights integration
Cost monitoring panels
AKS cluster specific dashboards
Service dependency mapping
Incident correlation panels

---


