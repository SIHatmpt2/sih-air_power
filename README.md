# AERO-OPS: Dynamic Air Operations & Resource Optimisation
**SIH26250 | Implementation Master Specification**  
*A human-in-the-loop, simulation-first decision-support platform*

---

## 1. Overview & Core Value Proposition

AERO-OPS is a role-based decision-support platform designed for dynamic air operations, resource readiness, and multi-source operational awareness. It provides:
- **Common Operational Picture (COP):** Aggregates airbase resources, operational zones, sensor health, and equipment status.
- **Real Aircraft & Defence Asset Modeling:** Incorporates real aircraft and defense systems (e.g., Dassault Rafale, Su-30MKI, LCA Tejas, S-400 Triumf, MR-SAM Barak-8, Akash-NG, QRSAM, and hybrid gun/missile systems) into a structured readiness and deployment model.
- **Human-in-the-Loop Re-planning:** Preserves human review, acknowledgement, and complete audit history for recommendations; no autonomous or unmonitored execution of operational actions.
- **Explainable AI (XGBoost):** Provides readiness forecasting and generic option scoring with transparent reason codes and fallback to deterministic rule baselines.
- **Real-Time Delivery:** Powered by Django Channels and WebSockets backed by Redis for multi-client dashboard updates.

> **Safety & Scope Note:** This system operates as a decision-support and readiness optimization demonstration. It intentionally does not execute automated weapon engagement procedures, real-world weapon selection, or classified sensor/tactical targeting logic.

---

## 2. Technology Stack

* **Backend / Web Layer:** Django (Modular Monolith) with Django REST Framework
* **Real-Time Communication:** Django Channels + ASGI (Daphne) backed by Redis
* **Database & Geospatial:** PostgreSQL + PostGIS (GeoDjango)
* **Machine Learning / Analytics:** XGBoost, Scikit-Learn, Pandas, NumPy
* **Frontend:** Django Templates, HTML5/CSS3/JavaScript, Leaflet.js
* **Containerization:** Docker & Docker Compose

---

## 3. Repository & Application Structure

```text
├── manage.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── README.md
├── config/                  # Django project configuration (settings, ASGI/WSGI, routing)
├── apps/
│   ├── core/                # RBAC, airbases, squadrons, equipment, maintenance, orders, audit
│   ├── radar_simulation/    # Synthetic sensor nodes, scenario playback, track processing, WebSockets
│   └── suggestion/          # Feature extraction, XGBoost readiness models, explainable scoring
├── templates/               # Role-scoped UI (auth, commander, squadron, shared)
├── static/                  # Stylesheets, JavaScript, images, Leaflet assets
├── fixtures/                # Seed data (demo_seed.json)
├── ml_artifacts/            # Versioned XGBoost model weights, schemas, and metrics
└── data/
    └── raw/                 # Baseline defense systems dataset (INDIA_Air_Defence_Systems.csv)
```

---

## 4. User Roles & Access Control

1. **Airbase Commander:** Oversees assigned airbase equipment availability, crew readiness, maintenance boards, event acknowledgements, and advisory suggestions.
2. **Squadron Leader:** Scoped strictly to their assigned squadron; manages orders inbox, equipment readiness submissions, and duty/rest logging.
3. **Zonal Commander:** Cross-airbase aggregated visibility across assigned zones, sensor health monitoring, scenario overview, and zone-level coordination.

---

## 5. Local Setup & Quickstart

### 1) Virtual Environment Setup
```bash
python -m venv .venv

# Windows (PowerShell):
.venv\Scripts\activate

# macOS / Linux:
source .venv/bin/activate
```

### 2) Install Dependencies
```bash
pip install -r requirements.txt
```

### 3) Configure Environment Variables
Copy `.env.example` to `.env` and verify your settings:
```bash
cp .env.example .env
```

### 4) Database & Migrations
Ensure PostgreSQL with PostGIS is running:
```bash
python manage.py migrate
python manage.py loaddata fixtures/demo_seed.json
```

### 5) Run the ASGI Web Server
```bash
daphne -b 0.0.0.0 -p 8000 config.asgi:application
```

### 6) Run the Radar Simulation Engine (Separate Terminal)
```bash
python manage.py run_simulation --scenario baseline --seed 26250
```

---

## 6. Verification & Acceptance Targets
- Scope-isolated access enforcement on all endpoints (Squadron leaders cannot read out-of-scope airbase records).
- Real-time updates delivered to connected dashboards within 2 seconds of event publication.
- Advisory suggestions provide plain-language reason codes and fall back cleanly to rule-based baselines if ML artifacts are missing.
