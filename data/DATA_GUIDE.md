# Data Guide — UCL–Nottingham UR5e Teleoperation Dataset
## MMAI'26 Hackathon · SONAIR Robotics Strand

---

## The Experiment

**Date:** 15 May 2026 · **Duration:** ~7.5 minutes (12:02–12:10 UTC)
UCL (London) remotely operated a UR5e robot arm at the University of Nottingham over a public WAN link.

| Side | Role | Mean RTT | Meaning |
|------|------|---------|---------|
| UoN local | Host (robot) | ~2 ms | Simulation baseline |
| UCL remote | Guest (operator) | 55.5 ms | Real WAN deployment |

The gap between these two values **is the sim2real gap** — directly measured.

---

## File 1: `sonair_ucl_rtt_dataset.csv`
**1,750 rows** · UCL-side ping/pong round-trip measurements

| Column | Type | Description |
|--------|------|-------------|
| `ts` | ISO8601 timestamp | Absolute time — convert to elapsed seconds for plotting |
| `rtt_ms` | float (ms) | Round-trip time — **core column for the RTT time-series plot** |
| `jitter_ms` | float (ms) | RTT jitter vs previous sample |
| `ack_latency_ms` | float (ms) | Recorded for some samples, may be empty |
| `kind` | string | Always `pong` |
| `authority` | string | `observer` / `requesting` / `operator` — **three phases, worth analysing separately** |
| `vcap` | float 0–1 | Velocity cap factor: 1.0 = full speed, <1.0 = safety-limited, 0 = forced stop |
| `lost` | int | Cumulative packet loss count |

**Usage notes:**
- `ts` is ISO8601 (e.g. `2026-05-15T12:02:33.786Z`). Subtract the first timestamp to get elapsed seconds before plotting.
- Three `authority` phases: observer (n=4) → requesting (n=51) → operator (n=1,695)
- `vcap < 1.0` means the robot was forced to slow down due to high latency — a behavioural-level sim2real gap measurement

**Key statistics:**
```
Mean RTT:    55.5 ms      P95:  112 ms
Median:      41.0 ms      P99:  299 ms
Std dev:     47.3 ms      Max:  401 ms
RTT > 80ms:  13.6%        RTT > 200ms:  2.7%
vcap < 1.0:  36.7%  (robot safety-limited for over a third of the session)
```

---

## File 2: `sonair_ucl_cmd_latency.csv`
**4,220 rows** · Paired command send + ACK records

| Column | Type | Description |
|--------|------|-------------|
| `ts` | float (seconds) | Elapsed seconds since first command |
| `seq` | int | Command sequence number (sequential — check for gaps) |
| `cmd_type` | string | Command type (see table below) |
| `relay_rtt_ms` | float (ms) | Command ACK round-trip — **core column for the latency histogram** |
| `authority` | string | Always `operator` |

**Command breakdown:**
| Command | Count | Meaning |
|---------|-------|---------|
| `speedj` | 2,103 | Joint-space velocity control |
| `speedl` | 1,571 | Cartesian-space velocity control |
| `speedl/j_stop` | 543 | Stop commands |
| `movel` | 4 | Linear move to pose |
| `freedrive_start/stop` | 8 | Manual guidance mode |

**Key statistics:**
```
Mean:    42.9 ms    P95:  84 ms
Median:  37.0 ms    Max:  243 ms
Std dev: 22.2 ms    Skewness: 3.19 (right-skewed — typical for WAN teleoperation)
```

---

## File 3: `UCL_session_audit.csv`
**25 rows** · Control authority transitions and anomaly events

| Column | Description |
|--------|-------------|
| `timestamp` | Event time (`YYYY-MM-DD HH:MM:SS`) |
| `direction` | `OUT` (sent) / `IN` (received) / `REJ` (rejected) |
| `kind` | Event type |
| `detail` | Detail string |

**Key events for stretch goal (event annotation):**

| Time | Event | Position in RTT data | Meaning |
|------|-------|---------------------|---------|
| 12:02:33 | auth_ok | ~0s | Session established |
| 12:02:47 | **auth_grant** | **~14s** | UCL gets control (GRANT) |
| 12:09:34 | **link_degraded ×9** | **~427s** | 9 commands rejected (ESTOP-equivalent) |

---

## Analysis Suggestions

### Minimum viable (to satisfy required deliverables)
1. Load `rtt_dataset.csv`, convert `ts` to elapsed seconds, plot `rtt_ms` as a line chart
2. Load `cmd_latency.csv`, plot `relay_rtt_ms` as a histogram
3. Write a plain-language interpretation on your page

### Deeper analysis (Challenge 1 material)
- Group by `authority` phase and compare RTT statistics separately
- The `requesting` phase has std dev of 81ms vs 45.8ms in `operator` — what does this tell you?
- Correlate `vcap` with `rtt_ms` — at what latency threshold does the safety cap engage?
- Design your own sim2real gap metric (simulation baseline = 2ms, real deployment = 55.5ms)

### Governance analysis (Challenge 2 material)
The raw data contained personal information that was scrubbed before distribution:
UCL operator's public IP address, GPS location (Camden Town, London), and browser User-Agent fingerprint.
Your governance policy should address: what should be published, what withheld, and why.

### Plain-language descriptions (Challenge 3 material)
- 55ms average delay ≈ the time it takes to blink
- 401ms worst case ≈ nearly half a second
- The robot was forced to slow down for over a third of the session
- Simulation: 2ms latency. Real WAN: 55.5ms. That's a 27× gap.

---

## Code to Load the Data

**Python:**
```python
import pandas as pd

df = pd.read_csv('sonair_ucl_rtt_dataset.csv')
df['ts'] = pd.to_datetime(df['ts'])
df['elapsed'] = (df['ts'] - df['ts'].iloc[0]).dt.total_seconds()

cmd = pd.read_csv('sonair_ucl_cmd_latency.csv')
audit = pd.read_csv('UCL_session_audit.csv')
```

**JavaScript (browser):**
```javascript
// Using Papa Parse (CDN: https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js)
Papa.parse('sonair_ucl_rtt_dataset.csv', {
  download: true,
  header: true,
  dynamicTyping: true,
  complete: function(results) {
    const rows = results.data.filter(r => r.rtt_ms);
    const t0 = new Date(rows[0].ts).getTime();
    rows.forEach(r => r.elapsed = (new Date(r.ts).getTime() - t0) / 1000);
    // rows[i].elapsed = seconds, rows[i].rtt_ms = latency in ms
  }
});
```

**Note:** `fetch()` on local CSV files fails due to CORS in most browsers.
Solutions: deploy to GitHub Pages (recommended), run `python3 -m http.server 8080` locally,
or use a file input (`<input type="file" accept=".csv">`) for manual upload.
