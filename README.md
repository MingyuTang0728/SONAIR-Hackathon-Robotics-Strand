# SONAIR Robotics Hackathon Participant Package

This repository contains the participant materials for building a **Campus Sub-Portal** for the SONAIR robotics strand.

Your goal is to create a public-facing campus or lab sub-portal that can be discovered through the SONAIR network, show your robotics capability, and present evidence from the provided UR5e teleoperation dataset.

## What is included

```text
SONAIR_Participant_Package_EN/
├── README.md
├── QUICK_START.md
├── SUBMISSION_CHECKLIST.md
├── TROUBLESHOOTING.md
├── data/
│   ├── DATA_GUIDE.md
│   ├── sonair_ucl_rtt_dataset.csv
│   ├── sonair_ucl_cmd_latency.csv
│   └── UCL_session_audit.csv
└── participant_template/
    ├── index.html
    ├── federation.json
    ├── theme.config.json
    ├── sonair_request_system.js
    ├── PARTICIPANT_SUBPORTAL_INDEX_TEMPLATE.txt
    ├── READ_ME_FIRST_PARTICIPANTS.txt
    ├── DEPLOY_PARTICIPANT.md
    └── REQUEST_SYSTEM_GUIDE.md
```

## What you need to build

Your team should produce:

1. A live Campus Sub-Portal website.
2. A valid `federation.json` file at the root of your website repository.
3. A valid `theme.config.json` file at the root of your website repository.
4. A dashboard using the provided dataset, including:
   - an RTT time-series plot;
   - a command latency histogram;
   - a short plain-language interpretation.
5. A request inbox on your sub-portal that can receive and approve/reject routed access or collaboration requests.

## Recommended workflow

1. Copy the files from `participant_template/` into your own GitHub repository.
2. Edit `federation.json` with your institution name, city, coordinates, GitHub Pages URL, and contact email.
3. Edit `theme.config.json` to control the visual identity of your sub-portal.
4. Edit `index.html` or use `PARTICIPANT_SUBPORTAL_INDEX_TEMPLATE.txt` as a copy-and-paste template.
5. Build your RTT and latency dashboard using the files in `data/`.
6. Enable GitHub Pages for your repository.
7. Submit your live `federation.json` URL.

## Important routing rule

The following two values must match exactly:

```text
federation.json -> node.id
index.html -> window.SONAIR_NODE_ID
```

For example, if your `federation.json` says:

```json
"id": "ucl"
```

then your `index.html` must say:

```js
window.SONAIR_NODE_ID = 'ucl';
```

If these values do not match, requests will not appear in your sub-portal inbox.
