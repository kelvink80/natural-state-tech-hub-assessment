# Natural State Tech Hub Manager Assessment

Author: Kelvin Kimani Kinyanjui

## Overview

This repository contains my submission for the Natural State Tech Hub Manager take-home assessment.

The assessment consists of two components:

1. Sensor Network Challenge
2. Deployment Design Challenge

The work has been completed using the supplied datasets, maintenance records, and device inventory information. My approach focused on identifying operationally significant issues, distinguishing device failures from infrastructure failures, prioritising remediation activities, and proposing practical improvements for long-term reliability and maintainability.

---

## Repository Structure

```text
.
├── README.md
├── sensor-network-challenge.md
├── deployment-design-challenge.md
├── ai-disclosure.md
│
├── data/
│   ├── devices.csv
│   ├── gateway_status.csv
│   ├── maintenance_log.csv
│   └── telemetry.csv
│
├── analysis/
│   ├── investigation_notes.md
│   ├── exploratory_analysis.ipynb
│   └── validation_queries.sql
│
└── diagrams/
    ├── proposed-architecture.png
    └── monitoring-workflow.png
```

---

## Assessment Approach

Rather than analysing datasets in isolation, I approached the assignment as a real-world operational investigation.

The objectives were:

- Identify device-level failures.
- Identify gateway and infrastructure issues.
- Determine whether observed problems were isolated or systemic.
- Correlate telemetry data with maintenance activities.
- Prioritise field interventions.
- Recommend monitoring and operational improvements.
- Design a scalable monitoring network suitable for remote conservation environments.

To achieve this, I reviewed and correlated:

- Device inventory records
- Telemetry data
- Gateway status logs
- Maintenance history

This allowed findings to be validated across multiple data sources before conclusions were reached.

---

## Sensor Network Challenge Summary

The investigation identified several operational issues affecting network performance and reliability.

### Key Findings

| Priority | Finding | Classification |
|----------|----------|----------|
| P1 | SM-03 stopped reporting and shows signs of power or hardware failure | Device Failure |
| P1 | SM-08 experienced a prolonged outage likely linked to physical damage | Device Availability |
| P1 | GW-01 experienced significant connectivity interruptions | Infrastructure |
| P2 | GW-02 experienced a confirmed restart event | Infrastructure |
| P3 | SM-05 operating on legacy firmware and abnormal reporting interval | Configuration |
| P3 | SM-07 operating on legacy firmware and abnormal reporting interval | Configuration |
| Positive | Multi-gateway packet reception demonstrates useful coverage overlap and redundancy | Network Design |

A detailed breakdown, supporting evidence, impact assessment, and recommended remediation actions can be found in `sensor-network-challenge.md`.

---

## Operational Recommendations

The analysis indicates that the highest-value improvement would be the introduction of proactive monitoring and alerting.

Recommended alerts include:

| Alert | Threshold |
|---------|---------|
| Missing telemetry | Two missed reporting intervals |
| Gateway offline | 15 minutes |
| Backhaul disconnected | 15 minutes |
| Low battery voltage | Below configured threshold |
| Firmware non-compliance | Version mismatch |
| Sensor anomaly detection | Values outside expected operating range |

Recommended notification channels:

- Email
- Microsoft Teams
- SMS for critical events

The objective is to reduce dependence on users discovering data gaps manually and instead detect issues automatically before data loss becomes significant.

---

## Deployment Design Challenge Summary

The proposed deployment architecture was designed around the following requirements:

- Remote operation
- Low power consumption
- High reliability
- Cost efficiency
- Ease of maintenance
- Scalability

The design includes:

- LoRaWAN-connected environmental sensors
- Solar-powered field infrastructure
- Multiple gateways for coverage and redundancy
- Cellular backhaul
- Local data processing at the Tech Hub
- Integration with NS Analytics

Core software components include:

- ChirpStack
- MQTT (Mosquitto)
- PostgreSQL
- Grafana
- Prometheus
- Alertmanager

Full design details are available in `deployment-design-challenge.md`.

---

## Asset Lifecycle and Maintenance Management

The proposed operational model includes maintaining a central asset inventory containing:

- Device identifier
- Device type
- GPS location
- Firmware version
- Installation date
- Gateway assignment
- Maintenance history
- Warranty information
- Operational status

Routine field procedures include:

- Physical inspections
- Solar system checks
- Battery verification
- Antenna and enclosure inspections
- Firmware verification
- Telemetry validation

This supports traceability, preventative maintenance, and long-term network sustainability.

---

## Risk Management

The primary risks identified for a remote sensor network are:

- Wildlife damage
- Theft and vandalism
- Power system failures
- Severe weather
- Cellular network interruptions
- Hardware supplier quality issues

Mitigation measures are discussed within the deployment design document.

---

## AI Usage

AI tools were used to assist with:

- Reviewing assignment requirements
- Discussing investigation approaches
- Refining report structure
- Improving clarity and readability

All analysis, findings, prioritisation decisions, architectural recommendations, and final conclusions were reviewed and validated before inclusion in this submission.

A complete AI usage disclosure is provided in `ai-disclosure.md`.

---

## Final Note

My focus throughout the assessment was not only on identifying technical issues, but also on demonstrating how I would operate and support a production sensor network in a conservation environment.

The recommendations therefore prioritise:

- Reliability
- Observability
- Maintainability
- Scalability
- Practical field operations

with the goal of maximising data availability while minimising operational overhead.
