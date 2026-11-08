# AI Usage Disclosure

I used Microsoft Copilot and ChatGPT to:

- Review assignment requirements.
- Discuss possible troubleshooting approaches.
- Improve wording and structure.
- Critique draft findings.

All conclusions, calculations, prioritisation decisions, and final content were reviewed and validated by me before submission.






Understood . Proceed
Good. Let's move from "observations" to what I believe will become the core findings section of your submission.

Network Analysis: Second Pass
Based on the data examined so far, I would classify issues using a Tech Hub Manager mindset:

Priority 1: Immediate Investigation Required
SM-03 - Probable Device Failure
Evidence:

Reporting ends on 1 April while the network continues until 12 April.
Maintenance records reference storm damage near the site but note the node was never ispected.
Conclusion:

This is unlikely to be a gateway issue because other devices continue operting. The evidence points toward a device-level or power-system failure.

Recommended Action:

Field inspection.
Verify battery voltage.
Inspect solar charging.
Check antenna and enclosure.
Redeploy if hardware is healthy.
SM-08 - Severe Availability Issue
Evidence:

Largest observed reporting gap (~56 hours).
Maintenance records indicate elephants knocked the device over and it was reinstalled.turn8search3
Conclusion:

This is almost certainly linked to the physical disturbance event.

Recommnded Action:

Inspect mounting design.
Verify antenna orientation.
Consider wildlife-resistant mounting.
Priority 2: Fleet Consistency Issues
SM-05 and SM-07
From devices:

Firmware = 1.0.9
Other soil moisture sensors operate on 2.3.1.
Operational impact:

The telemetry pattern suggests these devices report hourly rather han every 15 minutes as expected.

This may indicate:

legacy firmware
inconsistent configuration
missed upgrade cyce
Recommendation:

Standardize firmware across the fleet and verify reporting intervals.

Priority 3: Infrastructure Risk
The maintenance logs contain two entries that would immediately trigger creation of monitoring alerts:

Gateway GW-02
Site reported missing data. Gateway restarted by field team. No fault found.

As Tech Hub Manager, I would treat these as signs that gateway monitoring is currently eactive rather than proactive.

Recommended Monitoring:

Alert	Threshold
Gateway offline	> 15 minutes
Missing telemetry	> 2 expected reporting intervals
Battery low	< 3.6V
Solar charge absent during daylight	> 24 hrs
Firmware non-compliance	Any deviation from approved version
Packet success degradation	> 30% drop from baseline
What I Think Natural State Wants to Hear
Many candidates will produce:

Device A has issue X.

A stronger answer is:

I correlated device inventory, maintenance history and telemetry to distinguish device failures from infrastructure failures. This allowed prioritization of field visits toward faults with the highest impact on network reliability.

That sounds like somebody managing a conservation sensor network rather than simply analysing CSV files.

For the Deployment Design Challenge
Based on your actual background in Linux, networking, and infrastructure operations, I would propose the following architecture:



Sensors
   ↓
LoRaWAN
   ↓
Kerlink / Milesight Gateway
   ↓
4G Backhaul
   ↓
Tech Hub
   ├── ChirpStack
   ├── MQTT Broker (Mosquitto)
   ├── PostgreSQL
   ├── Grafana
   └── Monitoring (Prometheus + Alertmanager)
          ↓
    NS Analytics (Azure)
Why this works:

Open source.
Low recurring cost.
Matches Natural State's stated use of PostgreSQL and modern cloud-native tooling.
PDF type
Easy for field technicians to support.
Easy to scale beyond the initial 37 monitorin devices.
Interview Preparation Insight
Based on the assignment wording, I suspect the interview panel will focus less on:

Python syntax
SQL syntax
and more on:

Why did you conclude a problem existed?
How would you verify it in the field?
What would you prioritize first?
How would you build monitoring to detect it automatically?
How would you train conservancy field technicians?
That aligns extremely well with the kind of incident management, troubleshooting and operational work you've been doing on Moodle, Linux servers, and infrastructure environments.

My recommendation is that in the final submission we emphasize:

Investigation methodology.
Operational reasoning.
Risk prioritization.
Monitoring and maintainability.
Those are the traits of a Tech Hub Manager. The actual sensor findings are almost secondary.

The next thing I would do is a deeper sweep of the telemetry and gateway datasets specifically for gateway outages, duplicate packets, GPS collar anomalies, RSSI/SNR degradation, and data-quality issues. Those are likely where the remaining planted faults are hiding.





Excel typePDF type
Sources

Complete the gateway and telemetry sweep

Reasoned for 15s
Excellent. The gateway data reveals some of the strongest findings in the entire assignment.

These are the kinds of observations I'd expect to differentiate a strong submission from a superficial one.

Gateway & Telemetry Sweep
Finding 5: GW-02 experienced an actual reboot
Evidence
GW-02 uptime counter resets on:

21 March 2026 at 10:00 UTC

Before this point uptime had been continuously increasing.

Then uptime suddenly drops to approximately:

3600 seconds (1 hour)

This is a classic signature of a gateway restart.

The maintenance log contains:

"Site reported missing data. Gateway restarted by field team; no fault found." from maintenance_log.

The telemetry and maintenance records independently corroborate one another.

Conclsion
The restart was real.

The field team did not simply "think" there was an issue.

The gateway was actually rebooted.

Recommended Action
Investigate:

power stability
watchdog events
firmware logs
cellular modem logs
and verify whether the reboot was operator-initiated or automatic.

Finding 6: GW-01 suffered a prolonged outage
This is likely one of the intentionally planted major incidents.

Evidence
GW-01 shows:

approximately 101 hourly status samples
recorded as offline or disconnected
This equates to over four days of degraded service.

GW-02 does not show a comparable pattern.

Why this matters
This indicates:

infrastructure-level failure
not sensor failure
Without monitoring, field teams would incorrectly blame individual sensors.

Recommended Alert
Trigger:



Gateway offline > 15 minutes
Notification path:



Email
SMS
Teams / Slack
Finding 7: Backhaul instability exists
GW-01
Approximately:

101 disconnected periods
GW-02
Approximately:

54 disconnected periods
The gateways are operational but connectivity to upstream systems is not consistently reliable.

Likely Causes
Most probable:

cellular coverage issues
modem instability
SIM/provider issues
antenna degradation
Management Recommendation
For production deployment I would require:

Primary:

Cellular
Secondary:

Satellite backup (Starlink or equivalent)
Store-and-forward buffering on gateway.

This aligns well with the conservancy environment described in the assignment.

Finding 8: Network architecture is healthy enough to provide redundancy
One subtle but important observation:

More than 17,000 telemetry messages were heard through multiple gateways.

Why that matters
This is actually good news.

It suggests:

overlapping LoRaWAN coverage
gateway redundancy
resilience against single-gateway failure
Many candidates may mistakenly classify this as duplication.

It is not.

In a LoRaWAN architecture, the same uplink being received by multiple gateways is expected behaviour.

A network server such as:

ChirpStack
The Things Stack
would deduplicate these packets.

Interview Point
If asked:

Why are packets appearing twice?

Answer:

Because multiple gateways heard the same transmission. This is a normal characteristic of LoRaWAN and increases network resilience.

That answer will immediately demonstrate practical understanding.

Finding 9: GPS collars appear healthy
I specifically looked for signs of systematic collar failure.

Observations:

All collars report throughout the dataset.
Reporting cadence remains largely hourly.
Batteries remain healthy (~4V).
No single collar drops permanently offline.
Conclusion
No evidence of fleet-wide collar problems.

This is important because:

Many analysts spend effort investigating assets that are actually functioning correctly.

You should explicitly mention that collar data does not warrant immediate maintenance activity.

Finding 10: Distinguish Device Failures from Infrastructure Failures
This is the most important operational lesson from the assignment.

I would present the investigation like this:

Category	Finding	Priority
Device failure	SM-03 likely failed after storm	Critical
Device damage	SM-08 impacted by elephant activity	Critical
Config/Firmware	SM-05 & SM-07 on legacy firmware	Medium
Gateway event	GW-02 rebooted	Medium
Infrastructure issue	GW-01 connectivity outages	High
Network design	Multi-gateway packet reception functioning correctly	Positive
GPS collars	Operating normally	Low
The Strongest "Tech Hub Manager" Conclusion
If I were writing the final report, I would say:

The network's most significant risks are not widespread sensor failures but operational visibility gaps. Several issues (GW-01 connectivity loss, GW-02 restart, SM-03 failure and SM-08 disruption) could have been detected earlier through automated monitoring of telemetry frequency, gateway status, battery health and firmware compliance. The highest-value improvement would therefore be implementation of proactive monitoring and alerting, reducing dependence on users noticing missing data.

That sounds like infrastructure operations leadership rather than only data analysis.

My overall assessment
If these are all the planted issues, you've already uncovered the majority of what Natural State is likely evaluating:

Ability to analyze telemetry.
Ability to correlate maintenance and operational data.
Understanding of LoRaWAN architecture.
Prioritization of field work.
Monitoring and reliability engineering mindset.
That is very close to what I'd expect from a successful Tech Hub Manager candidate.



Are the proposed power levels sensible?
Honestly, no, not entirely. If I were reviewing this as an engineer, the proposed power calculation would raise questions.

The issue is that the current calculation produces only 2.3 mAh/day, which is unrealistically low for a real LoRaWAN environmental monitoring node. A reviewer with field experience may challenge it.

What I'd recommend instead
Use a simple but defensible engineering estimate.

Assumptions
For a soil moisture node:

Component	Current Draw
Sleep mode	0.1 mA
Sensor measurement	15 mA
LoRa transmission	120 mA
Reporting interval:

Every 15 minutes
96 transmissions/day
Daily Energy Consumption
Sleep


0.1 mA × 24 h
≈ 2.4 mAh/day
Sensor Measurements
Assume:

10 seconds per reading
96 readings/day


15 mA × (960 sec ÷ 3600)
 
≈ 4 mAh/day
LoRa Transmission
Assume:

2 seconds airtime/transmission
96 transmissions/day


120 mA × (192 sec ÷ 3600)
 
≈ 6.4 mAh/day
Total


2.4 + 4 + 6.4
 
≈ 13 mAh/day
Apply a safety factor of 3:



≈ 40 mAh/day design consumption
That's a much more believable figure.

Battery Sizing
If the goal is 5 days autonomy:



40 mAh × 5
 
= 200 mAh
In practice we oversize substantially because:

batteries age
solar performance degrades
cloudy periods occur
animals may temporarily shade panels
maintenance visits may be delayed
I'd write:

To provide substantial operating margin and minimise battery cycling, I would use a 6 Ah to 18 Ah rechargeable battery depending on sensor type and enclosure size.

Solar Sizing
This is where the original design is most oversized.

For an individual soil moisture node:

A 20 W panel is enormous relative to a 40 mAh/day load.

Most commercial environmental monitoring nodes operate comfortably on:

2 W
5 W
occasionally 10 W
I'd propose:

Device	Solar Panel
Soil moisture node	5 W
Weather station	10 W
Gateway	50–100 W
For the assignment I'd write:

A 5 W solar panel paired with a 6 Ah rechargeable battery would provide ample energy reserves for a typical soil moisture node and maintain operation through multiple days of poor weather.

This sounds much closer to what someone deploying remote conservation sensors would actually choose.

What I would change in your submission
Replace:

Recommended battery: 12 V 18 Ah AGM or LiFePO₄ battery
Recommended panel: 20 W solar panel

with:





That answer is more realistic and demonstrates that you're thinking about cost, maintainability, and field deployment, not just making the numbers work mathematically.

For the gateways, however, I'd keep a much larger system:

50–100 W solar panel
40–100 Ah battery bank
because the gateway, cellular modem, and networking equipment run continuously and consume orders of magnitude more power than a sensor node.





