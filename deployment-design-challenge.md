# Deployment Design Challenge

## Overview

This design proposes a resilient, solar-powered environmental monitoring network for a 30 km² conservancy in Laikipia. The solution prioritises reliability, low operating costs, ease of maintenance and scalability while ensuring that data reaches NS Analytics within one hour and is retained for at least one year.

The proposed architecture uses LoRaWAN for field communications, solar-powered gateway infrastructure on the ridge, cellular backhaul to the Tech Hub and a central monitoring platform hosted on Tech Hub servers.

---

# 1. Sensors, Communications and Reporting Frequency

## Soil Moisture Monitoring (12 Restoration Plots)

**Recommended Sensor:** SenseCAP S2105 or equivalent LoRaWAN soil moisture sensor

**Communication Method:** LoRaWAN

Soil moisture changes relatively slowly compared to weather conditions. A reporting interval of every 15 minutes provides sufficient resolution for restoration monitoring while minimising battery consumption.

| Parameter | Value |
|------------|---------|
| Number of sensors | 12 |
| Reporting interval | 15 minutes |
| Messages per day | 96 per sensor |

This produces approximately 1,152 readings per day across all restoration plots, which is well within LoRaWAN network capacity.

---

## Weather Monitoring (3 Locations)

**Recommended Sensor:** Barani MeteoHelix IoT Pro or equivalent weather station

**Communication Method:** LoRaWAN

Weather conditions can change rapidly and influence soil moisture, vegetation growth and grazing practices.

| Parameter | Value |
|------------|---------|
| Number of stations | 3 |
| Reporting interval | 15 minutes |

The reporting interval aligns with soil moisture data, simplifying later analysis and correlation.

---

## Water Level Monitoring (2 River Locations)

**Recommended Sensor:** Milesight EM500-UDL ultrasonic distance sensor

**Communication Method:** LoRaWAN

River levels generally change slowly except during rainfall events.

| Parameter | Value |
|------------|---------|
| Number of sensors | 2 |
| Reporting interval | 15 minutes |

This interval enables timely detection of flow events while maintaining long battery life.

---

## Cattle Tracking (20 GPS Collars)

**Recommended Device:** Digital Matter Oyster3 or equivalent GPS tracking collar

**Communication Method:** LoRaWAN

GPS tracking generates more data than environmental sensing and relies on battery-powered devices mounted on animals.

| Parameter | Value |
|------------|---------|
| Number of collars | 20 |
| Reporting interval | 60 minutes |
| Event-based updates | Optional |

Hourly reporting provides sufficient visibility into herd movements while preserving battery life.

Higher reporting frequencies should only be enabled during intensive monitoring studies because they significantly increase power consumption and network traffic.

---

# 2. Gateway Location and Backhaul Design

## Gateway Placement

I would deploy two industrial LoRaWAN gateways on elevated points along the northern ridge.

### Gateway 1 (Western Ridge)

Purpose:

- Cover western restoration plots.
- Monitor the western river section.
- Provide overlapping coverage and redundancy.

### Gateway 2 (Eastern Ridge)

Purpose:

- Cover eastern restoration plots.
- Track livestock movement across the conservancy.
- Provide redundancy for fixed monitoring stations.

Placing gateways on the ridge maximises line-of-sight coverage while also taking advantage of the strongest available cellular signal.

### Coverage Strategy

The two gateways would intentionally provide overlapping coverage.

Benefits include:

- Increased packet reception probability.
- Improved resilience during gateway outages.
- Reduced risk of coverage gaps.
- Better support for mobile GPS collars.

---

## Backhaul Connectivity

### Primary Backhaul

**4G Cellular Connectivity**

The scenario notes that usable 4G coverage exists on the ridge. Cellular connectivity therefore offers the best balance between reliability, cost and operational complexity.

### Data Flow

```text
Sensors
    ↓
LoRaWAN
    ↓
Gateways
    ↓
4G Cellular
    ↓
Tech Hub
    ↓
NS Analytics
```

### Connectivity Failure Handling

When cellular connectivity becomes unavailable:

1. Gateways continue receiving sensor traffic.
2. Data is stored locally in gateway buffers.
3. Once connectivity returns, queued messages are forwarded automatically.
4. Monitoring alerts are generated when connectivity loss exceeds defined thresholds.

This store-and-forward approach prevents data loss during temporary carrier outages.

Because mobile coverage is known to be patchy within the conservancy, gateways should be configured to retain several days of telemetry locally. This ensures that temporary backhaul outages do not result in permanent data loss.

### Gateway Power Systems

Unlike field sensor nodes, gateways operate continuously and support cellular communications.

For each gateway installation I would deploy:

- 50-100 W solar panel
- 40-100 Ah battery bank
- MPPT charge controller

This provides sufficient capacity to maintain operation throughout extended periods of poor weather.

---

# 3. Power Budget for a Remote LoRaWAN Node

## Assumptions

Example soil moisture node:

| State | Current | Duration |
|---------|---------|---------|
| Sleep | 0.05 mA | 23.95 h/day |
| Sensor Reading | 15 mA | 20 s/day |
| LoRa Transmission | 120 mA | 30 s/day |

### Daily Consumption

Sleep:

```text
0.05 mA × 23.95 h
= 1.20 mAh
```

Sensor Operation:

```text
15 mA × (20 ÷ 3600)
= 0.08 mAh
```

Transmission:

```text
120 mA × (30 ÷ 3600)
= 1.00 mAh
```

Base Consumption:

```text
≈ 2.3 mAh/day
```

To account for battery ageing, temperature effects, charging inefficiencies, seasonal variations in solar generation and operational uncertainty, I would apply a conservative engineering safety factor and size the system for:

```text
40 mAh/day
```

---

## Battery Sizing

### Required Autonomy

Design target:

```text
5 consecutive cloudy days
```

Minimum capacity:

```text
40 mAh/day × 5 days
= 200 mAh
```

Environmental monitoring systems should be intentionally oversized to provide adequate operational margin.

### Recommended Battery

For a typical soil moisture monitoring node:

**6 Ah LiFePO₄ rechargeable battery**

This provides substantial reserve capacity while remaining lightweight, compact and suitable for long-term field deployment.

---

## Solar Panel Sizing

### Assumptions

- Approximately 4 peak sun hours per day.
- Charging and conversion losses.
- Multiple cloudy days annually.
- Long-term battery degradation.

### Recommended Solar Panel

**5 W solar panel**

A 5 W panel provides significantly more energy than the node consumes under normal operating conditions and enables rapid battery recovery following poor weather.

### Node Power System Summary

| Component | Specification |
|------------|---------------|
| Solar Panel | 5 W |
| Battery | 6 Ah LiFePO₄ |
| Charge Controller | Integrated low-power controller |
| Autonomy Target | ≥ 5 days |

---

# 4. Data Platform Architecture

## Network Architecture

A network architecture diagram is provided in the `diagrams` folder as a supporting artefact.

The architecture consists of:

- LoRaWAN field sensors and GPS collars
- Two ridge-mounted gateways with overlapping coverage
- Cellular backhaul to the Tech Hub
- ChirpStack, MQTT and PostgreSQL services hosted at the Tech Hub
- Integration with NS Analytics through secure APIs

---

# 5. Device Management and Field Operations

## Asset Register

Every device should have a record containing:

- Device ID
- Device type
- Serial number
- GPS coordinates
- Installation date
- Firmware version
- Gateway assignment
- Maintenance history
- Warranty information
- Operational status

I would store this information within PostgreSQL or an asset management platform such as Snipe-IT.

---

## Routine Field Checks

Field technicians should verify:

- Solar panel cleanliness
- Battery voltage and charging trends
- Charge controller status
- Antenna condition
- Enclosure integrity
- Mounting stability
- Firmware version compliance
- Recent telemetry reception
- Gateway connectivity status

Routine inspections should occur monthly and immediately following major storms or wildlife-related incidents.

---

# 6. Equipment Costs and Risk Management

## Approximate Equipment Costs

| Item | Qty | Estimated Cost (USD) |
|--------|-----|-----|
| Soil moisture sensors (including node power systems) | 12 | $3,600 |
| Weather stations | 3 | $2,400 |
| Water level sensors | 2 | $1,000 |
| GPS collars | 20 | $5,000 |
| LoRaWAN gateways | 2 | $3,000 |
| Gateway solar systems (panels, batteries and controllers) | 2 | $1,500 |
| Mounting hardware and protective enclosures | - | $1,000 |
| Servers and networking | - | $4,000 |

**Estimated Total:** ~$21,500

*Figures are planning estimates and would be refined during procurement.*

---

## Key Risks and Mitigations

| Risk | Mitigation |
|--------|-----------|
| Wildlife damage | Steel mounts, protective enclosures and wildlife-resistant installations |
| Theft | Tamper-resistant installations, asset tagging and GPS-recorded inventory |
| Lightning | Surge protection, grounding and proper mast design |
| Power failure | Appropriately sized solar systems, minimum five-day battery autonomy and battery health monitoring |
| Cellular outages | Local buffering, automatic retransmission and gateway connectivity alerts |
| Hardware failure | Spare equipment stock, firmware management and automated monitoring alerts |

---

# 7. Field Team Organisation and Supplier Management

## Technician Responsibilities

Field technicians should be assigned defined inspection routes and preventative maintenance schedules.

Responsibilities include:

- Routine inspections
- Device replacements
- Battery testing
- Firmware upgrades
- Fault verification
- Incident response

A ticketing system should be used to record faults, corrective actions and maintenance history.

---

## Managing Repeated Supplier Failures

If a supplier's equipment repeatedly failed in the field:

1. Record all incidents.
2. Calculate failure rates by model and firmware version.
3. Escalate findings to the supplier.
4. Request root-cause analysis and corrective actions.
5. Compare performance against alternative vendors.
6. Pilot replacement devices in the field.
7. Update procurement standards if reliability remains unacceptable.

Procurement decisions should be driven by measured field performance rather than manufacturer specifications alone.

---

# Conclusion

This design provides a cost-effective and operationally practical monitoring network for a remote conservancy environment. The combination of LoRaWAN, solar-powered infrastructure, overlapping gateway coverage, local buffering and automated monitoring provides resilience against common field challenges including power interruptions, wildlife damage and connectivity outages.

The proposed architecture is scalable, based on proven open-source technologies and can be maintained by a small field operations team while meeting the conservancy's requirements for near-real-time visibility and long-term data retention.
