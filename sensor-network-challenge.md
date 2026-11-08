## Executive Summary

I analysed six weeks of telemetry, gateway status data, device inventory records and maintenance logs.

The investigation identified:

1. A probable soil moisture sensor failure (SM-03).
2. A major outage affecting SM-08.
3. Legacy firmware and incorrect reporting intervals on SM-05 and SM-07.
4. A confirmed reboot event on GW-02.
5. Significant gateway connectivity instability affecting GW-01.
6. Opportunities to improve monitoring and operational visibility.

No evidence was found of widespread GPS collar failures.

---

## Methodology

To avoid false conclusions, I separated findings into:

- Device-level faults
- Gateway-level faults
- Backhaul connectivity issues
- Environmental causes
- Configuration and firmware issues

I validated observations by cross-referencing:

- Telemetry patterns
- Maintenance records
- Device inventory metadata
- Gateway status logs

---

## Finding 1: SM-03 Probable Device Failure

### Evidence

SM-03 stopped reporting on 1 April while other fixed sensors continued to report through the remainder of the dataset.

Battery voltage fell as low as 3.32 V.

The maintenance log records storm damage near Plot 3, but notes that the node was not inspected.

### Impact

Loss of soil moisture monitoring at Restoration Plot 3.

### Recommended Action

Priority 1 site visit:

- Battery inspection
- Solar charging verification
- Enclosure inspection
- Antenna inspection
- Rejoin procedure if hardware remains healthy

---

## Finding 2: SM-08 Prolonged Outage Following Physical Disruption

### Evidence

SM-08 experienced the largest reporting gap observed among the environmental monitoring nodes, with a maximum gap of approximately 56 hours between telemetry messages. This is significantly higher than the reporting behaviour observed from comparable soil moisture sensors.

The maintenance log records that SM-08 was knocked over by elephants and subsequently reinstalled on a shorter post after the original mounting post was broken.

### Impact

The outage resulted in the loss of soil moisture observations from Restoration Plot 8 for more than two days. Extended gaps reduce confidence in trend analysis and may affect restoration monitoring activities that rely on continuous environmental measurements.

### Assessment

The timing and nature of the maintenance event strongly suggest that the outage was caused by physical disruption rather than a network-wide connectivity problem.

This highlights wildlife interaction as a significant operational risk for field-deployed infrastructure within the conservancy.

### Recommended Action

- Inspect current mounting arrangement and antenna placement.
- Review RSSI and packet success rates following reinstallation.
- Replace temporary mounting structures with wildlife-resistant installations.
- Introduce post-maintenance verification checks to confirm normal telemetry transmission immediately after field repairs.
- Flag any future telemetry gaps exceeding two reporting intervals for investigation.

---

## Finding 3: SM-05 Operating on Legacy Firmware and Non-Standard Reporting Interval

### Evidence

The device inventory shows SM-05 running firmware version 1.0.9, while most soil moisture monitoring nodes operate on firmware version 2.3.1.

Telemetry records indicate that SM-05 reports approximately every 60 minutes, whereas the majority of soil moisture sensors transmit every 15 minutes.

### Impact

Although the device remains operational, reduced reporting frequency lowers temporal resolution and may affect the quality of environmental monitoring and analysis.

Differences in firmware and reporting configuration also increase operational complexity by creating inconsistent behaviour across the fleet.

### Assessment

The node appears healthy from a connectivity and battery perspective. The issue is more likely related to configuration drift or legacy firmware than an active hardware fault.

### Recommended Action

- Upgrade SM-05 to the approved firmware version.
- Verify transmission interval settings after upgrade.
- Confirm consistency with the standard soil moisture monitoring profile.
- Introduce automated firmware compliance checks for all deployed devices.

---

## Finding 4: SM-07 Operating on Legacy Firmware and Non-Standard Reporting Interval

### Evidence

SM-07 is also running firmware version 1.0.9 while the remainder of the soil moisture sensor fleet primarily operates on firmware version 2.3.1.

Telemetry behaviour mirrors SM-05, with reporting occurring approximately once per hour rather than every 15 minutes.

### Impact

Data collection remains functional, but monitoring granularity is reduced and operational consistency across the network is diminished.

### Assessment

Finding the same behaviour on two devices running the same legacy firmware suggests a firmware or configuration-related issue rather than two independent hardware failures.

### Recommended Action

- Upgrade firmware to the approved production version.
- Verify device configuration after upgrade.
- Confirm reporting frequency matches deployment standards.
- Include firmware auditing within routine maintenance procedures.

---

## Finding 5: GW-01 Connectivity Instability

### Evidence

Gateway status records show numerous periods where GW-01 was not fully connected to its backhaul network. Approximately 101 hourly status records indicate degraded availability or connectivity interruptions.

Maintenance records also note that GW-01 was found offline before later recovering automatically without intervention.

### Impact

Gateway connectivity issues have the potential to affect all devices dependent on the gateway and may lead to delayed data delivery to downstream systems.

Unlike individual sensor failures, gateway problems can have site-wide consequences.

### Assessment

The evidence suggests an infrastructure or backhaul reliability issue rather than a sensor problem.

Possible contributing factors include:

- Cellular network instability
- Gateway modem issues
- Intermittent power-related behaviour
- Environmental or antenna-related connectivity degradation

### Recommended Action

- Review gateway and modem logs.
- Investigate cellular signal quality trends.
- Verify antenna condition and mounting.
- Implement automated gateway-availability monitoring.
- Generate alerts when a gateway remains disconnected for more than 15 minutes.
- Validate store-and-forward buffering behaviour during connectivity interruptions.

---

## Finding 6: GW-02 Reboot Event

### Evidence

GW-02 experienced a clear reset of its uptime counter on 21 March 2026, indicating that the gateway restarted during the observation period.

The maintenance log contains a corresponding entry stating that the field team restarted GW-02 after reports of missing data.

### Impact

Although the gateway resumed operation, unplanned restarts can indicate underlying infrastructure or software issues and may temporarily interrupt data collection.

### Assessment

Telemetry and maintenance records independently confirm that the restart occurred.

Because the field team reported missing data before restarting the gateway, the reboot should be treated as an operational incident rather than routine maintenance.

### Recommended Action

- Review gateway system logs around the restart window.
- Examine power-system performance and battery history.
- Verify gateway firmware version and known issues.
- Investigate cellular connectivity immediately before the restart.
- Create an automatic alert for unexpected gateway reboots.
- Record future restart events as formal incidents for trend analysis and root-cause investigation.

---

## Prioritised Remediation Plan

| Priority | Item | Reason |
|-----------|--------|----------|
| P1 | SM-03 | Complete data loss |
| P1 | SM-08 | Multi-day outage |
| P1 | GW-01 Connectivity | Infrastructure-wide impact |
| P2 | GW-02 Reboot Analysis | Stability concern |
| P3 | SM-05 Firmware | Operational consistency |
| P3 | SM-07 Firmware | Operational consistency |

---

## Monitoring and Alerting Recommendations

| Alert | Trigger |
|---------|----------|
| Missing telemetry | Two missed reporting intervals |
| Gateway offline | 15 minutes |
| Gateway disconnected | 15 minutes |
| Battery low | < 3.6 V |
| Firmware compliance | Version mismatch |
| Sensor anomaly | Values outside expected range |

### Alerting Channels

- Email
- Microsoft Teams
- SMS for critical failures

---

## Conclusion

The investigation identified a combination of device failures, configuration issues and infrastructure instability. The highest-priority actions are restoration of service for SM-03, verification of SM-08 following physical damage, and investigation of recurring GW-01 connectivity interruptions.

Beyond the individual faults identified, the greatest opportunity for improvement is the implementation of proactive monitoring and alerting. Automated detection of missing telemetry, gateway outages, firmware non-compliance and power-related issues would significantly reduce operational risk and improve long-term network reliability.
