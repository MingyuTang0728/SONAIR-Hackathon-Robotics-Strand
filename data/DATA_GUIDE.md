# Dataset Guide

This folder contains a non-sensitive UR5e teleoperation dataset for the SONAIR robotics strand.

The dataset represents a UCL–Nottingham remote teleoperation session where commands were sent across a network link to a physical UR5e robot.

## Files

```text
sonair_ucl_rtt_dataset.csv
sonair_ucl_cmd_latency.csv
UCL_session_audit.csv
```

## Suggested dashboard outputs

Your dashboard should include at least:

1. **RTT time-series plot**
   - Show round-trip time over the session.
   - Use this to discuss network stability and delay variation.

2. **Command latency histogram**
   - Show the distribution of command delays.
   - Use this to discuss typical latency and outliers.

3. **Written interpretation**
   - Explain what the plots reveal about remote teleoperation reliability.
   - Explain how timing or latency could contribute to the simulation-to-real gap.

## Optional stretch goal

Use `UCL_session_audit.csv` to annotate important events, such as:

```text
GRANT
RELEASE
ESTOP
```

Then discuss whether latency changes around these control or safety events.

## Example questions to explore

- What is the typical RTT during the session?
- Are there latency spikes?
- Do command delays cluster around a stable value or vary widely?
- What metric could describe the sim-to-real timing gap?
- How would network delay affect safe remote teleoperation?

## Responsible use

Use this dataset for hackathon analysis, visualisation, and evidence-dashboard design. Do not treat it as a complete industrial safety validation dataset.
