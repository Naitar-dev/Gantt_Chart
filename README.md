🗂️ Gantt_Chart

📘 ETL from ArcGIS Feature Layer → generate docs/data.json → visualize with dhtmlxGantt on GitHub Pages → automate via GitHub Actions.

📖 Introduction

Gantt_Chart keeps a Gantt view of ArcGIS tasks continuously up to date:

Pull data from ArcGIS Feature Layer

Transform with Python (pandas, arcgis)

Serve an interactive chart via dhtmlxGantt in docs/index.html

Update on a schedule (UTC) or manual trigger using GitHub Actions

🧱 Repository Structure
Gantt_Chart/
│
├── Project_Management_ETL.py        # ETL: ArcGIS → cleaned JSON for frontend
├── docs/
│   ├── index.html                   # dhtmlxGantt entry page
│   ├── data.json                    # generated output (do not edit manually)
│   ├── dhtmlxgantt.css              # dhtmlxGantt styles (or use CDN)
│   └── dhtmlxgantt.js               # dhtmlxGantt script (or use CDN)
└── .github/
    └── workflows/
        └── main.yml                 # CI/CD workflow (schedule + dispatch)

🗺️ Architecture / Data Flow
ArcGIS Feature Layer
   (ARCGIS_USERNAME / ARCGIS_PASSWORD in Secrets)
            │
            ▼
Project_Management_ETL.py  ───►  Clean / Map / Validate  ───►  docs/data.json
                                                                  │
                                                                  ▼
                                                     docs/index.html + dhtmlxGantt
                                                                  │
                                                                  ▼
                                                            GitHub Pages site

⚡ Quick Start (Local)
# 1) Install
pip install pandas arcgis

# 2) Set credentials (local)
export ARCGIS_USERNAME="your_username"
export ARCGIS_PASSWORD="your_password"

# 3) Run ETL
python Project_Management_ETL.py

# 4) Preview (avoid file:// CORS)
python -m http.server 8000
# open http://localhost:8000/docs/


✅ On success, docs/data.json is (re)generated and the Gantt renders in your browser.

🌐 GitHub Pages (Hosting)

Settings → Pages → Deploy from a branch

Branch: main · Folder: /docs

After pushes to main, Pages deploys the content under docs/.

⚙️ Workflow (CI/CD)

File: .github/workflows/main.yml
Goal: Keep docs/data.json fresh and force a Pages rebuild.
Key points (no full YAML):

🗓️ Triggers

Schedule: 0 2 * * 1 (every Monday 02:00 UTC)

Manual: workflow_dispatch

🔐 Permissions

contents: write, pages: write, id-token: write, actions: write

🧱 Runner

ubuntu-latest

📥 High-Level Steps

Checkout repository (no persisted credentials)

Setup Python 3.10

Install dependencies: pandas, arcgis

Run Project_Management_ETL.py (uses ARCGIS_USERNAME/ARCGIS_PASSWORD from Secrets)

Commit docs/data.json and touch docs/index.html (bump timestamp), push to main

Call Pages Builds API to force a rebuild

🔑 Required Secrets

ARCGIS_USERNAME, ARCGIS_PASSWORD (Repo → Settings → Secrets and variables → Actions)

GITHUB_TOKEN (auto-provided during Actions runs)

🕒 Cron is UTC. Convert from your local timezone if you need a different schedule.

🗃️ Data Schema (docs/data.json)

dhtmlxGantt expects a root object with data (tasks) and optional links (dependencies):

Field	Type	Required	Description
data	array	✅	List of task objects
links	array	❌	List of dependency objects (optional)

Task object (typical):

Field	Type	Required	Notes
id	number/string	✅	Unique task id
text	string	✅	Task title/name
start_date	string	✅	Start date; match gantt.config.date_format (ISO YYYY-MM-DD works)
duration	number	✅	Planned duration in days
progress	number	❌	0–1 (e.g., 0.4 = 40% complete)
parent	id	❌	Parent task id (or 0 / null for root)

Links object (optional):

Field	Type	Required	Notes
id	id	✅	Unique link id
source	id	✅	Source task id
target	id	✅	Target task id
type	string	✅	"0" FS, "1" SS, "2" FF, "3" SF (dhtmlxGantt)

Minimal example:

{
  "data": [
    { "id": 1, "text": "Task", "start_date": "2025-10-01", "duration": 5, "progress": 0.4, "parent": 0 }
  ],
  "links": []
}

🔁 Field Mapping (ArcGIS → dhtmlxGantt)

Map ArcGIS attributes to the expected Gantt fields inside Project_Management_ETL.py:

dhtmlxGantt	Example ArcGIS Field	Transform / Notes
id	OBJECTID / custom	Ensure uniqueness
text	Title / TaskName	String
start_date	StartDate	Format to ISO YYYY-MM-DD (or match config)
duration	PlannedDays	Integer days
progress	PercentComplete	Convert 0–100 → 0–1
parent	ParentId	Use 0 / null for root

🧪 Validate types and handle missing values to avoid frontend errors.

🖥️ Frontend Notes (dhtmlxGantt)

Include dhtmlxgantt.css and dhtmlxgantt.js in docs/index.html (or switch to a CDN).

Ensure date parsing matches your data: set gantt.config.date_format if not ISO.

Load docs/data.json via fetch/XHR and call gantt.parse(json).

🔒 Security

Store ArcGIS credentials in GitHub Actions Secrets (ARCGIS_USERNAME, ARCGIS_PASSWORD).

Never commit credentials or tokens to the repository.

Limit workflow permissions to only what’s required (as outlined above).

🧪 Validation Checklist

 docs/index.html renders a chart locally

 docs/data.json has valid data (and optional links)

 Date formats align with gantt.config.date_format

 ArcGIS credentials are set in Secrets

 Pages is configured (main → /docs)

 Workflow runs manually and on schedule without errors

🛠️ Troubleshooting

Blank page locally: Use a local server (python -m http.server) instead of file:// to avoid CORS issues.

Pages didn’t refresh: The workflow both touches docs/index.html and calls the Pages Builds API. Re-check Pages settings and workflow permissions (pages: write, id-token: write).

No commit: If data.json has no changes the commit may be skipped. Optionally add a generated_at timestamp in ETL output to force diffs.

Time mismatch: Cron is UTC. Convert your local time to UTC before editing the schedule.

🧭 Maintenance Tips

Pin dependency versions in requirements.txt for reproducibility.

Log ETL steps (counts, dropped rows) to simplify debugging in Actions.

Keep a small sample dataset for local UI testing.

🗺️ Roadmap (Optional)

Dependencies (links) and critical path highlighting

Custom themes / resource workload views

Filters, grouping, and export (PNG/PDF)

🤝 Contributing

Fork & branch: feat/<name>

Ensure the chart renders locally

Open a PR describing changes and data schema impacts

📄 License

MIT (update if needed).
