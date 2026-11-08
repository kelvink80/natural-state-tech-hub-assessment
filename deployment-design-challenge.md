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
- Reduced risk of coverage
