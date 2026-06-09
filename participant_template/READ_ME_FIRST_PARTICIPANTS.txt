READ ME FIRST — SONAIR PARTICIPANT SUB-PORTAL

You are building a Campus Sub-Portal for your university or lab.

Required files in your GitHub repository root:

1. index.html
2. federation.json
3. theme.config.json
4. sonair_request_system.js

Important rule:

- federation.json -> node.id
- index.html -> window.SONAIR_NODE_ID

These two values must be exactly the same.

Example:

federation.json:
  "id": "ucl"

index.html:
  window.SONAIR_NODE_ID = 'ucl';

The file sonair_request_system.js must stay next to index.html. If it is missing, the request inbox will not load.

Your final submission should include the live federation manifest URL:

https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
