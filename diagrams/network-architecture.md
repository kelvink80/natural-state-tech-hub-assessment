# Network Architecture Diagram

## Proposed Field Monitoring Architecture

```text
┌──────────────────────────────────────────────────────────────┐
│                  LAIKIPIA CONSERVANCY SITE                  │
│                         (~30 km²)                           │
└──────────────────────────────────────────────────────────────┘

 Soil Moisture         Weather          Water Level
 Sensors (12)       Stations (3)       Sensors (2)
      │                  │                  │
      └──────────┬───────┴───────┬─────────┘
                 │               │

          GPS Collars (20)
                 │
                 ▼

        ┌─────────────────┐
        │     LoRaWAN     │
        │  Field Network  │
        └─────────────────┘
                 │
       ┌─────────┴─────────┐
       │                   │

       ▼                   ▼

┌────────────────┐   ┌────────────────┐
│ Gateway 1      │   │ Gateway 2      │
│ Western Ridge  │   │ Eastern Ridge  │
│ 4G Backhaul    │   │ 4G Backhaul    │
│ Solar Powered  │   │ Solar Powered  │
└───────┬────────┘   └───────┬────────┘
        │                    │
        └─────────┬──────────┘
                  │
                  ▼

        Cellular Network (4G)
                  │
                  ▼

┌──────────────────────────────────────────┐
│                 TECH HUB                 │
├──────────────────────────────────────────┤
│ ChirpStack                               │
│ LoRaWAN Network Server                   │
│                                          │
│ Mosquitto MQTT Broker                    │
│                                          │
│ PostgreSQL Database                      │
│                                          │
│ Grafana Dashboards                       │
│                                          │
│ Prometheus & Alertmanager                │
└──────────────────────────────────────────┘
                  │
                  ▼

┌──────────────────────────────────────────┐
│             NS ANALYTICS                 │
│ Reporting, Dashboards and Analytics      │
└──────────────────────────────────────────┘
```

## Design Notes

### Communications

- Environmental sensors and cattle collars communicate using LoRaWAN.
- Two ridge-mounted gateways provide overlapping coverage and redundancy.
- Gateways use 4G cellular connectivity for backhaul to the Tech Hub.

### Resilience

- Gateways continue collecting data during temporary cellular outages.
- Buffered data is forwarded automatically when connectivity is restored.
- Multiple gateways reduce the risk of coverage loss from a single gateway failure.

### Monitoring

The Tech Hub infrastructure provides:

- Device management through ChirpStack
- Reliable message transport through MQTT
- Long-term storage in PostgreSQL
- Operational dashboards through Grafana
- Automated alerting through Prometheus and Alertmanager

### Data Retention

Telemetry is stored within PostgreSQL for a minimum of one year before archival, meeting the conservancy's retention requirements while supporting reporting and analytics.
