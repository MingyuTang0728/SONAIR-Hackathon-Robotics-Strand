# SONAIR Hackathon — Complete Deployment Package
## MMAI'26 · "Prove It — Building Evidence for Trustworthy Multimodal AI Deployment"
### Teaching Space 4, Room 301 · One Pool Street · London · E20 2AF

---

## Package Contents

```
hackathon_package/
│
├── README.md                          ← YOU ARE HERE
│
├── organiser/
│   ├── SONAIR_main_portal_v17.html    ← Main portal (open in browser, no server needed)
│   ├── federation-registry.json       ← Add team URLs here as they register
│   └── DEPLOY_ORGANISER.md            ← Organiser deployment steps
│
├── participant_template/
│   ├── index.html                     ← Minimal sub-portal + Requests Inbox template
│   ├── federation.json                ← Teams fill in their own details (10 required fields)
│   ├── theme.config.json              ← Teams set their colour / institution name
│   ├── sonair_request_system.js       ← Shared application inbox code
│   └── DEPLOY_PARTICIPANT.md          ← Technical spec + content requirements
│
└── data/
    ├── sonair_ucl_rtt_dataset.csv     ← RTT data (1,750 samples, 437s session)
    ├── sonair_ucl_cmd_latency.csv     ← Command ACK latency (4,220 paired records)
    ├── UCL_session_audit.csv          ← GRANT / ESTOP event log
    └── DATA_GUIDE.md                  ← Field dictionary, statistics, analysis suggestions, code examples
```

**Updated note:** a minimal `index.html` is now included only as a safe integration template for `theme.config.json` and the Requests Inbox. Teams can replace the design completely, but should keep the request-routing block and the `SONAIR_REQUESTS.renderInbox(...)` call.

---

## Organiser On-The-Day Checklist

### Before participants arrive
- [ ] Open `organiser/SONAIR_main_portal_v17.html` in Chrome or Firefox (no server needed)
- [ ] Confirm the 3D UK map loads and the Nottingham node is visible
- [ ] Open `organiser/federation-registry.json` in a text editor — ready to add team URLs
- [ ] Distribute `data/` and `participant_template/` to all teams (USB or shared drive)

### During the hackathon
- [ ] As each team deploys, add their `federation.json` URL to `federation-registry.json`
- [ ] Refresh the main portal — their node appears on the map automatically
- [ ] Or: tell teams to use **SONAIR Nodes tab → Connect Node** to self-register

### End of day
- [ ] Each team's node visible on the 3D UK map
- [ ] Each team's card in Co-Creation Space
- [ ] Each team's Evidence Dashboard has RTT plot + histogram + written interpretation

---

## federation.json — The 10 Required Fields

| # | Field | JSON path |
|---|-------|-----------|
| 1 | Node ID | `node.id` |
| 2 | Institution name | `node.name` |
| 3 | City | `node.city` |
| 4 | Latitude | `node.lat` |
| 5 | Longitude | `node.lon` |
| 6 | Colour | `node.color` |
| 7 | Portal URL | `node.url` |
| 8 | Contact email | `node.contact_email` |
| 9 | Card title | `co_creation_card.title` |
| 10 | Card description | `co_creation_card.description` |

---

## Challenge × Data mapping

| Challenge | Core deliverable | Key data |
|-----------|----------------|---------|
| **Challenge 1** — Sim2Real Metric | Novel metric + visualisation + proposal | `rtt_dataset.csv` (`authority`, `vcap`), `cmd_latency.csv` |
| **Challenge 2** — Evidence Governance | Evidence card format + governance policy | All files (decide what to publish/withhold) |
| **Challenge 3** — Accessible Portal | Usability-tested portal + documented changes | Plain-language data descriptions |
| **Stretch: Event annotation** | RTT plot with GRANT/ESTOP markers | `rtt_dataset.csv` + `UCL_session_audit.csv` |
| **Stretch: Schema validator** | GitHub Action | `federation.json` structure |
