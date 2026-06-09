# Participant Deployment Guide

This guide helps you deploy your SONAIR Campus Sub-Portal using GitHub Pages.

## 1. Repository setup

Create a new public GitHub repository and copy these files into its root:

```text
index.html
federation.json
theme.config.json
sonair_request_system.js
```

## 2. Configure `federation.json`

Update every placeholder value:

```json
{
  "node": {
    "id": "your-node-id",
    "name": "Your University Name",
    "city": "Your City",
    "lat": 52.000,
    "lon": -1.000,
    "color": "#19E6C2",
    "url": "https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/",
    "contact_email": "yourname@your-institution.ac.uk",
    "role": "Hackathon campus node",
    "summary": "Brief description of your lab."
  },
  "co_creation_card": {
    "title": "Your Lab SONAIR Sub-Portal",
    "description": "Describe your robotics evidence and collaboration interests.",
    "tags": ["Robotics", "Sim2Real", "SONAIR"],
    "visitor_url": "https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/"
  }
}
```

## 3. Configure `theme.config.json`

Example:

```json
{
  "institution_name": "Your University Name",
  "node_name": "Your Robotics Evidence Node",
  "city": "Your City",
  "primary_color": "#19E6C2",
  "logo_url": ""
}
```

Your page should load this file using `fetch()` and apply `primary_color` as a CSS variable.

The template already does this.

## 4. Configure request routing

In `index.html`, find:

```js
window.SONAIR_NODE_ID  = 'your-node-id';
window.SONAIR_API_BASE = 'http://EVENT_REQUEST_API:5055';
```

Replace `your-node-id` with the same value as `federation.json -> node.id`.

Replace `http://EVENT_REQUEST_API:5055` with the event request API endpoint provided during the session.

## 5. Enable GitHub Pages

In GitHub:

```text
Settings → Pages → Deploy from a branch → main → root → Save
```

Wait for GitHub Pages to publish your site.

Your site should become available at:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/
```

Your manifest should become available at:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
```

## 6. Test your pages

Open these URLs in a browser:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/theme.config.json
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/sonair_request_system.js
```

All four must load successfully.

## 7. Dataset dashboard requirements

Use files from the `data/` folder to create:

1. an RTT time-series plot;
2. a command latency histogram;
3. a short written interpretation of what the plots show about remote teleoperation reliability.

Optional stretch goal: annotate the RTT time-series with GRANT, RELEASE, and ESTOP events from `UCL_session_audit.csv`.

## 8. Final submission

Submit your federation manifest URL:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
```
