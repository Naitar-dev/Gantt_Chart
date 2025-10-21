🗂️ Gantt_Chart

📘 ETL from ArcGIS Feature Layer → generate docs/data.json → visualize with dhtmlxGantt on GitHub Pages → automate via GitHub Actions.

📖 Introduction

Gantt_Chart provides a lightweight pipeline to keep a Gantt view of your ArcGIS tasks always up to date:

Pull data from ArcGIS Feature Layer

Transform with Python (pandas + arcgis)

Serve an interactive Gantt chart using dhtmlxGantt on GitHub Pages

Keep data fresh with scheduled & manual CI/CD via GitHub Actions

🧱 Repository Structure
Gantt_Chart/
│
├── Project_Management_ETL.py   # ETL: ArcGIS → cleaned JSON for frontend
├── docs/
│   ├── index.html              # dhtmlxGantt entry page
│   ├── data.json               # generated output (do not edit manually)
│   ├── dhtmlxgantt.css         # dhtmlxGantt styles (or use CDN)
│   └── dhtmlxgantt.js          # dhtmlxGantt script (or use CDN)
└── .github/
    └── workflows/
        └── main.yml            # CI/CD workflow (schedule + dispatch)

⚡ Quick Start
pip install pandas arcgis
export ARCGIS_USERNAME="your_username"
export ARCGIS_PASSWORD="your_password"
python Project_Management_ETL.py

# Preview (avoid file:// CORS)
python -m http.server 8000  # open http://localhost:8000/docs/


Data format: docs/data.json must follow dhtmlxGantt schema (e.g., text, start_date, duration, progress(0–1), parent, optional links).
Dates: keep consistent with gantt.config.date_format (ISO YYYY-MM-DD recommended).

🌐 GitHub Pages

Settings → Pages → Deploy from a branch

Branch: main · Folder: /docs

⚙️ Workflow (CI/CD)

.github/workflows/main.yml — key points (no full YAML):

🗓️ Triggers:

Schedule: 0 2 * * 1 (every Monday 02:00 UTC)

Manual: workflow_dispatch

🔐 Permissions: contents: write, pages: write, id-token: write, actions: write

🧱 Runner: ubuntu-latest

📥 Steps (high level):

Checkout repository (no persisted credentials)

Setup Python 3.10

Install pandas, arcgis

Run Project_Management_ETL.py with ArcGIS creds from Secrets

Commit docs/data.json & touch docs/index.html, then push to main

Call Pages Builds API to force a rebuild

🔑 Secrets:

ARCGIS_USERNAME, ARCGIS_PASSWORD (Repo → Settings → Secrets and variables → Actions)

GITHUB_TOKEN (auto-provided at runtime)

📝 Tips

🕒 Cron uses UTC—convert from your local time if needed.

🔄 If Pages doesn’t refresh, double-check Pages configuration and workflow permissions.

✅ Validate data.json (dates/parent/progress) to prevent frontend errors.
