# Deployment Design Challenge


## Overview

This design proposes a resilient, solar-powered environmental monitoring network for a 30 km² conservancy in Laikipia. The solution prioritises reliability, low operating costs, ease of maintenance, and scalability, while ensuring that data reaches NS Analytics within one hour and is retained for at least one year.

The proposed architecture uses LoRaWAN for field communications, solar-powered gateway infrastructure on the ridge, cellular backhaul to the Tech Hub, and a central monitoring platform hosted on the Tech Hub servers.

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

The reporting interval aligns with soil moisture data, making later analysis and correlation simpler.

---

## Water Level Monitoring (2 River Locations)

**Recommended Sensor:** Milesight EM500-UDL ultrasonic distance sensor

**Communication Method:** LoRaWAN

River levels change relatively slowly except during rainfall events.

| Parameter | Value |
|------------|---------|
| Number of sensors | 2 |
| Reporting interval | 15 minutes |

This interval allows rapid detection of flow events while maintaining long battery life.

---

## Cattle Tracking (20 GPS Collars)

**Recommended Device:** Digital Matter Oyster3 or equivalent GPS tracking collar

**Communication Method:** LoRaWAN

GPS tracking generates significantly more data than environmental sensors and operates on battery power carried by the animal.

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
- Monitor western river section.
- Provide overlap redundancy.

### Gateway 2 (Eastern Ridge)

Purpose:

- Cover eastern restoration plots.
- Track livestock movement across the conservancy.
- Provide redundant coverage for fixed sensors.

Placing gateways on the ridge maximises line-of-sight coverage and takes advantage of the strongest available cellular signal.

### Coverage Strategy

The two gateways intentionally provide overlapping coverage.

Benefits include:

- Increased packet reception probability.
- Resilience during gateway outages.
- Reduced risk of blind spots.
- Improved mobility support for GPS collars.

---

## Backhaul Connectivity

### Primary Backhaul

**4G Cellular Connectivity**

The scenario specifically notes that usable 4G coverage exists on the ridge. This makes cellular connectivity the most practical and cost-effective primary backhaul method.

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
2. Data is stored locally in a buffer.
3. Once connectivity returns, queued messages are forwarded automatically.
4. Monitoring alerts are generated if connectivity loss exceeds defined thresholds.

This store-and-forward approach prevents data loss during temporary carrier outages.


Gateway installations would use significantly larger power systems, typically:

- 50–100 W solar panel
- 40–100 Ah battery bank

This ensures continuous operation of the gateway, cellular modem and networking equipment throughout extended periods of poor weather.

---


# 3. Power Budget for a Remote LoRaWAN Node

## Assumptions

Example soil moisture node:

| State | Current | Duration |
|---------|---------|---------|
| Sleep | 0.05 mA | 23.95 h/day |
| Sensor reading | 15 mA | 20 s/day |
| LoRa transmission | 120 mA | 30 s/day |

### Daily Consumption

Sleep:

```text
0.05 mA × 23.95 h
= 1.20 mAh
```

Sensor operation:

```text
15 mA × (20/3600)
= 0.08 mAh
```

Transmission:

```text
120 mA × (30/3600)
= 1.00 mAh
```

Total:

```text
≈ 2.3 mAh/day
```

Applying a generous safety factor for environmental losses and aging:

```text
20 mAh/day design allowance
```

---


## Battery Sizing

### Required Autonomy

To maintain operation during extended periods of poor weather, I assume a design target of:

```text
5 consecutive cloudy days
```

Using the estimated design consumption of approximately:

```text
40 mAh/day
```

the minimum required battery capacity is:

```text
40 mAh/day × 5 days
= 200 mAh
```

In practice, environmental monitoring systems are intentionally oversized to account for:

- battery aging
- temperature effects
- charging losses
- seasonal reductions in solar generation
- delayed maintenance visits

### Recommended Battery

For a typical remote soil moisture monitoring node, I would deploy:

**6 Ah LiFePO₄ rechargeable battery**

This provides significant operating margin while remaining compact, lightweight and suitable for long-term field deployment.

---

## Solar Panel Sizing

### Assumptions

- Approximately 4 peak sun hours per day
- Charging and conversion losses
- Multiple cloudy days each year
- Future battery capacity degradation

### Recommended Solar Panel

**5 W solar panel**

A 5 W panel provides substantially more energy than the node consumes on a typical day and allows the battery to recover quickly following periods of poor weather.

This sizing balances reliability, cost, transportability and ease of maintenance.

### Node Power System Summary

| Component | Specification |
|------------|---------------|
| Solar Panel | 5 W |
| Battery | 6 Ah LiFePO₄ |
| Charge Controller | Integrated low-power controller |
| Autonomy Target | ≥ 5 days |

### Gateway Power Systems

LoRaWAN gateways have significantly higher power requirements than sensor nodes due to continuous operation of the gateway, processor, network interfaces and cellular modem.

For gateway installations, I would typically deploy:

| Component | Specification |
|------------|---------------|
| Solar Panel | 50–100 W |
| Battery Bank | 40–100 Ah |
| Charge Controller | MPPT Controller |

This provides sufficient reserve capacity to maintain operations through extended periods of poor weather and intermittent solar generation.



# 4. Data Platform Architecture

## Edge Layer

### Sensors

- Soil moisture sensors
- Weather stations
- Water level sensors
- GPS collars

### Communications

- LoRaWAN uplinks

### Gateway Layer

- Kerlink iStation or Milesight UG67 gateways

---

## Tech Hub Platform

### ChirpStack

Functions:

- LoRaWAN Network Server
- Device provisioning
- Message deduplication
- Device management

### Mosquitto MQTT Broker

Functions:

- Message transport
- Reliable integration between components

### PostgreSQL

Functions:

- Long-term data retention
- Reporting
- Analytics support

### Grafana

Functions:

- Dashboards
- Operational visibility
- Historical trend analysis

### Prometheus + Alertmanager

Functions:

- Infrastructure monitoring
- Device monitoring
- Automated notifications

---

## NS Analytics Integration

Processed data is forwarded from PostgreSQL and MQTT services to NS Analytics through secure APIs.

The platform should maintain at least one year of historical data within PostgreSQL before archival.

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

I would store this information in a PostgreSQL asset database or an asset management platform such as Snipe-IT.

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
- Gateway connectivity status (where applicable)

Routine inspections should occur monthly and after major storms or wildlife-related incidents.

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
| Gateway solar systems (50–100 W panels, batteries and controllers) | 2 | $1,500 |
| Mounting hardware and protective enclosures | - | $1,000 |
| Servers and networking | - | $4,000 |

**Estimated Total:** ~$21,500

*These figures are rough planning estimates intended for high-level budgeting and would be refined during procurement.*

---

## Key Risks and Mitigations

| Risk | Mitigation |
|--------|-----------|
| Wildlife damage | Steel mounts, protective enclosures and wildlife-resistant installations |
| Theft | Tamper-resistant installations, asset tagging and GPS-recorded asset inventory |
| Lightning | Surge protection, grounding and proper mast design |
| Power failure | Appropriately sized solar systems, minimum 5-day battery autonomy, battery health monitoring and preventative battery replacement |
| Cellular outages | Local buffering, automatic retransmission and gateway connectivity alerts |
| Hardware failure | Spare equipment stock, firmware management and automated monitoring alerts |

---

# 7. Field Team Organisation and Supplier Management

## Technician Responsibilities

Field technicians would be assigned defined inspection routes and maintenance schedules.

Responsibilities include:

- Routine inspections
- Device replacements
- Battery testing
- Firmware upgrades
- Fault verification
- Incident response

A ticketing system should be used to track faults and maintenance activities.

---

## Managing Repeated Supplier Failures

If a supplier's equipment repeatedly failed:

1. Record every fault.
2. Calculate failure rates by model and firmware version.
3. Escalate findings to the supplier.
4. Request root-cause analysis and corrective actions.
5. Compare performance against alternative vendors.
6. Pilot replacement devices in the field.
7. Update procurement standards if reliability remains unacceptable.

The objective would be to make procurement decisions based on measured field performance rather than vendor specifications alone.

---

## Conclusion

This design delivers reliable monitoring across the conservancy while meeting reporting and retention requirements. By combining LoRaWAN, solar-powered infrastructure, cellular backhaul, local buffering, proactive monitoring, and disciplined asset management, the network can operate sustainably in a remote conservation environment while providing timely, high-quality data to NS Analytics.
