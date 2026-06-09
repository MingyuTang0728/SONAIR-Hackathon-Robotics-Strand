SONAIR Participant Sub-portal Quick Start

1. Open PARTICIPANT_SUBPORTAL_INDEX_TEMPLATE.txt.
2. Copy all text into a new file named index.html in your GitHub repository.
3. Upload these four files to the repository root:
   - index.html
   - federation.json
   - theme.config.json
   - sonair_request_system.js
4. In index.html, edit:
   window.SONAIR_NODE_ID = your node id
   window.SONAIR_API_BASE = organiser API URL
5. In federation.json, make sure node.id exactly matches window.SONAIR_NODE_ID.
6. In federation.json, replace all placeholder URLs with your real GitHub Pages URL.
7. Enable GitHub Pages and share your federation.json URL with the organiser.

Important: If sonair_request_system.js is missing, your request inbox will not work.
