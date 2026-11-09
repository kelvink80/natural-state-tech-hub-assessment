# Natural State Tech Hub Manager Assessment

Author: Kelvin Kimani Kinyanjui

## Overview

This repository contains my submission for the Natural State Tech Hub Manager take-home assessment.

The assessment consists of:

1. Sensor Network Challenge
2. Deployment Design Challenge

My approach focused on:

- Identifying operational issues from telemetry and infrastructure data.
- Distinguishing device failures from gateway and connectivity issues.
- Prioritising field interventions.
- Designing a scalable monitoring network for a remote conservation environment.
- Proposing monitoring, maintenance and operational improvements.

---

## Repository Contents

| Document | Description |
|-----------|-------------|
| [sensor-network-challenge.md](./sensor-network-challenge.md) | Investigation of telemetry, gateway status, maintenance records and operational findings. |
| [deployment-design-challenge.md](./deployment-design-challenge.md) | Proposed network design for the Laikipia conservancy deployment. |
| [ai-disclosure.md](./ai-disclosure.md) | Summary of AI tools used during the assessment. |
| [investigation_notes.md](./investigation_notes.md) | Notes describing the investigation approach and analysis process. |
| [Network Architecture Diagram](./diagrams/network-architecture.md) | Supporting architecture diagram illustrating data flow from field sensors to NS Analytics. |

---

## Sensor Network Challenge Summary

The investigation identified:

- A probable failure of soil moisture sensor SM-03.
- A prolonged outage affecting SM-08 following physical disruption.
- Legacy firmware and non-standard reporting intervals on SM-05 and SM-07.
- A confirmed gateway reboot event on GW-02.
- Connectivity instability affecting GW-01.
- Opportunities to improve monitoring, alerting and operational visibility.

Recommended improvements include proactive monitoring for:

- Missing telemetry
- Gateway outages
- Connectivity failures
- Firmware compliance
- Battery health
- Sensor anomalies

---

## Deployment Design Challenge Summary

The proposed solution uses:

- LoRaWAN for field communications
- Ridge-mounted gateways with overlapping coverage
- Solar-powered infrastructure
- Cellular backhaul
- ChirpStack LoRaWAN Network Server
- Mosquitto MQTT
- PostgreSQL
- Grafana
- Prometheus and Alertmanager
- Integration with NS Analytics

The design emphasises reliability, maintainability, scalability and cost-effectiveness while meeting the requirement for near-real-time data delivery and one-year retention.

---

## Data

The datasets used for this analysis were supplied as part of the assessment package and are not included in this repository.

All findings and recommendations were derived from the supplied assessment data.

---

## AI Usage

AI tools were used to:

- Review assignment requirements.
- Discuss potential investigation approaches.
- Improve document structure and readability.
- Challenge assumptions and validate reasoning.

All conclusions, recommendations and design decisions were reviewed

natural-state-tech-hub-assessment/

README.md

sensor-network-challenge.md

deployment-design-challenge.md

ai-disclosure.md

investigation_notes.md

diagrams/ network-architecture.md
```

---

## Final Note

My focus throughout the assessment was not only on identifying technical issues, but also on demonstrating how I would operate, maintain and improve a production environmental monitoring network in a remote conservation setting.

The recommendations therefore prioritise:

- Reliability
- Observability
- Maintainability
- Scalability
- Practical field operations
