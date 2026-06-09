# Participant Deployment Guide
## SONAIR Campus Sub-Portal — MMAI'26 Hackathon
### You have 6 hours. This guide tells you what to build — how you build it is up to you.

---

## What You Must Deliver

Three things, all required:

```
your-repo-root/
├── index.html          ← Your own Campus Sub-Portal (you design and build this)
├── federation.json     ← Fill in your institution details (template provided, 10 required fields)
└── theme.config.json   ← Fill in your theme (template provided, 5 fields)
```

**`index.html` is your own design.** A minimal integration template is included so you can copy the Requests Inbox and theme-loading code safely. You may redesign the page completely, but keep the `window.SONAIR_NODE_ID`, `window.SONAIR_API_BASE`, `sonair_request_system.js`, and `SONAIR_REQUESTS.renderInbox(...)` parts.

---

## Content Requirements (Section A)

Your portal must include:

- **Institution name and city**
- **Lab identity** — who you are and what you work on
- **At least one equipment item or dataset** with specific details
- **A collaboration contact** — how other labs or industry partners can reach you

---

## Technical Requirements (each item is mandatory)

### B. federation.json
At the repository root. Must contain all 10 required fields (see below).

### C. theme.config.json
At the repository root, with 5 fields.
**Critical requirement: your `index.html` must use `fetch('theme.config.json')` on page load to read this file and apply `primary_color` as a CSS variable. The colour must not be hardcoded.**

Example (must implement):
```javascript
fetch('theme.config.json')
  .then(r => r.json())
  .then(config => {
    document.documentElement.style.setProperty('--primary', config.primary_color);
    // Also apply institution_name etc...
  });
```

### D. iframe compatibility
Your portal must load cleanly inside an `<iframe>` without errors.
Check: open browser DevTools → Console and confirm no X-Frame-Options or CSP frame-ancestors errors.
**GitHub Pages and Netlify are already iframe-safe by default.**

### Other technical requirements
- Accessible at a **stable HTTPS URL**
- **No login required**
- **Must not set X-Frame-Options response headers**

---

## E. Robot Data Dashboard (required)

Build a data dashboard on your page containing:

### 1. RTT time-series plot
- Shows round-trip time over the entire session (437 seconds)
- Data: `sonair_ucl_rtt_dataset.csv`, columns `ts` and `rtt_ms`
- Use Chart.js, D3.js, Plotly, or any library you know

### 2. Command latency histogram
- Shows the distribution of command ACK latencies
- Data: `sonair_ucl_cmd_latency.csv`, column `relay_rtt_ms`

### 3. Written interpretation (must be on the page)
In plain language (non-technical audience):
- What do these two plots show?
- What does this mean for the reliability of remote teleoperation?

---

## federation.json — The 10 Required Fields

```json
{
  "node": {
    "id":            "your-institution-id",
    "name":          "Your University Name",
    "city":          "Your City",
    "lat":           51.5074,
    "lon":           -0.1278,
    "color":         "#FF6B35",
    "url":           "https://your-username.github.io/your-repo/",
    "contact_email": "yourname@your-institution.ac.uk",
    "role":          "Hackathon campus node",
    "summary":       "One sentence about your lab"
  },
  "co_creation_card": {
    "title":       "Your Lab SONAIR Sub-Portal",
    "description": "Describe your contribution to the SONAIR network",
    "tags":        ["Robotics", "Sim2Real", "SONAIR"],
    "visitor_url": "https://your-username.github.io/your-repo/"
  }
}
```

Fields 1–8 are in `node`; fields 9–10 are in `co_creation_card`.
**`lat` and `lon` must be real UK coordinates** (lat 49–61, lon −9 to +3).
Find your coordinates: right-click your university on Google Maps.

### theme.config.json — 5 Fields

```json
{
  "institution_name": "Your University Name",
  "node_name":        "Your Robotics Lab",
  "city":             "Your City",
  "primary_color":    "#FF6B35",
  "logo_url":         ""
}
```

---

## Setting up the Requests Inbox (required)

When visitors submit requests via the main portal Co-Creation Space, they are
routed to your inbox through the Python server. Add both variables to your
`index.html`:

```html
<!-- Place BEFORE the sonair_request_system.js script tag -->
<script>
  window.SONAIR_NODE_ID  = 'your-institution-id'; // must match node.id in your federation.json exactly
  window.SONAIR_API_BASE = 'http://ORGANISER_IP:5055'; // ask organiser on the day
</script>
<script src="sonair_request_system.js"></script>
```

Then add the inbox container anywhere in your page:

```html
<div id="requests-inbox"></div>
<script>SONAIR_REQUESTS.renderInbox('requests-inbox');</script>
```

> ⚠️ **`SONAIR_NODE_ID` is required — do not skip it**
> The system uses this value as `subportal_id` to query the server.
> Without it the script tries `fetch('federation.json')`, which fails
> silently under `file://` protocol, leaving `subportal_id=unknown`
> and an empty inbox even when the server has your records.

---

## Pick Your Challenge

After completing the above requirements, go deeper with one direction:

### Challenge 1 — Sim2Real Metric Design
Design a novel metric that quantifies the gap between simulation and real deployment.
Required: surface the metric in your dashboard + supporting visualisations + written proposal (what it measures, why it matters, how it could be adopted across the SONAIR network).

### Challenge 2 — Federated Evidence Governance
Treat your federation.json as a governance document. Design a structured evidence card format and include a written governance policy: what you chose to publish, what you chose to withhold, and why.

### Challenge 3 — Accessible Co-Creation Portal
Build the most usable portal for non-technical visitors (industry partner, funder, student).
Required: usability test with 2+ people outside your team → document what you changed → propose one concrete improvement.

### Define Your Own
Discuss with a Problem Holder before committing.

---

## Stretch Goals (if time permits)

- **Event annotation** — mark GRANT (~14s) and ESTOP (~427s) from `session_audit.csv` on the RTT time-series
- **Schema validator** — GitHub Action that validates `federation.json` on every commit
- **Accessibility audit** — semantic headings, alt text, colour contrast; document improvements

---

## Submission Checklist

- [ ] Portal accessible at a **stable HTTPS URL** (test in a private/incognito window)
- [ ] Portal loads **without login**
- [ ] Portal loads cleanly in an `<iframe>` — no console errors
- [ ] `federation.json` at repo root, all 10 fields filled
- [ ] `theme.config.json` at repo root, `primary_color` applied via `fetch()` as CSS variable
- [ ] RTT time-series plot visible
- [ ] Command latency histogram visible
- [ ] Written interpretation on the page
- [ ] Node visible on the main portal 3D UK map
- [ ] Card visible in main portal Co-Creation Space

---



## Troubleshooting request inbox

If your GitHub Pages sub-portal does not show requests:

1. Make sure `sonair_request_system.js` is uploaded to the same GitHub repository root as `index.html`.
2. Open browser DevTools Console and check for `404 sonair_request_system.js`.
3. Make sure `window.SONAIR_NODE_ID` exactly matches `federation.json -> node.id`.
4. If your sub-portal is served from `https://...github.io`, do not use a plain `http://192.168.x.x:5055` API URL for long-term testing. Browsers may block this as mixed content. Use an HTTPS tunnel such as ngrok or cloudflared, or test the sub-portal locally over HTTP.
5. If the main portal shows an application ID starting with `local-`, the request was not saved to the organiser server. Check that the main portal is using the same `SONAIR_API_BASE` as the participant sub-portal.
6. If `Open approved sub-portal` opens a 404 page, check that `federation.json` contains your real GitHub Pages URL, not an example URL.
