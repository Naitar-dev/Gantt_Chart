Gantt_Chart

ArcGIS → Python ETL → docs/data.json → dhtmlxGantt → GitHub Pages → GitHub Actions

Pull tasks from ArcGIS Feature Layer, transform with Python, output docs/data.json, and visualize an interactive Gantt chart using dhtmlxGantt on GitHub Pages. A scheduled workflow keeps data fresh.

✨ Features

🧰 ETL with pandas + arcgis → docs/data.json

📊 Frontend: dhtmlxGantt in docs/index.html

🚀 Hosting: GitHub Pages from /docs

🔁 Automation: GitHub Actions (weekly + manual)

🗂️ Structure
Gantt_Chart/
├─ Project_Management_ETL.py
├─ docs/
│  ├─ index.html
│  ├─ data.json
│  ├─ dhtmlxgantt.css
│  └─ dhtmlxgantt.js
└─ .github/workflows/main.yml

⚡ Quick Start
pip install pandas arcgis
export ARCGIS_USERNAME="your_username"
export ARCGIS_PASSWORD="your_password"
python Project_Management_ETL.py
# Preview (avoid file:// CORS)
python -m http.server 8000  # open http://localhost:8000/docs/


docs/data.json must match dhtmlxGantt format (dates consistent with your gantt.config.date_format).

🌐 GitHub Pages

Settings → Pages → Deploy from a branch

Branch: main · Folder: /docs

⚙️ Workflow (CI/CD)

.github/workflows/main.yml — key points (no full YAML):

🗓️ Triggers:

Schedule: 0 2 * * 1 (Mon 02:00 UTC)

Manual: workflow_dispatch

🔐 Permissions: contents: write, pages: write, id-token: write, actions: write

🧱 Runner: ubuntu-latest

📥 Steps (high level):

Checkout repo (no persisted credentials)

Setup Python 3.10

Install deps: pandas, arcgis

Run Project_Management_ETL.py with ARCGIS_USERNAME/ARCGIS_PASSWORD from Secrets

Commit docs/data.json and touch docs/index.html to bump timestamp; push to main

Call Pages Builds API to force rebuild

Finish with a success message

🔑 Secrets:

ARCGIS_USERNAME, ARCGIS_PASSWORD (Repo → Settings → Secrets and variables → Actions)

GITHUB_TOKEN (auto-provided at runtime)

📝 Tips

🕒 Cron is UTC. Convert from local time if needed.

🔄 If Pages doesn’t refresh, confirm Pages config and workflow permissions.

🗃️ Keep data.json strictly aligned with dhtmlxGantt schema (date format, progress 0–1, parent IDs).
