<p align="center">
  Browser-based dashboard that fetches, analyzes and visualizes Coronal Mass Ejection (CME) events from NASA's DONKI API, with real-time velocity profiling and interactive event tracking.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-completed-brightgreen" alt="Status">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5">
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3">
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
  <img src="https://img.shields.io/badge/Chart.js-FF6384?style=for-the-badge&logo=chartdotjs&logoColor=white" alt="Chart.js">
  <img src="https://img.shields.io/badge/NASA%20DONKI-0B3D91?style=for-the-badge&logo=nasa&logoColor=white" alt="NASA DONKI API">
  <img src="https://img.shields.io/badge/GitHub%20Pages-121013?style=for-the-badge&logo=github&logoColor=white" alt="GitHub Pages">
</p>

<p align="center">
  <b>Click the buttons below to open the project:</b>
</p>

<p align="center">
  <!-- Substitua pela URL do GitHub Pages após publicar -->
  <a href="https://nasa-cme.github.io/Nasa-Cme-Dashboard/">
    <img src="https://img.shields.io/badge/GitHub%20Pages-Live-blue?style=for-the-badge" alt="Live Demo">
  </a>
  <a href="https://github.com/nasa-cme/Nasa-Cme-Dashboard">
    <img src="https://img.shields.io/badge/GitHub-Repository-black?style=for-the-badge" alt="GitHub Repository">
  </a>
</p>

---

# Table of Contents

- [About](#about)
- [Screenshots](#screenshots)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Deployment](#deployment)
- [Documentation](#documentation)
- [Contributors](#contributors)
- [License](#license)

---

# About

CME Watch is a browser-based dashboard for exploring space weather data from NASA's DONKI (Database Of Notifications, Knowledge, Information) API. It fetches Coronal Mass Ejection events for any user-defined date range, calculates statistics such as average velocity and Earth-directed count, and renders an interactive velocity profile chart alongside a sortable event log.

The application uses a data pipeline architecture: NASA DONKI API → GitHub Actions → Supabase → GitHub Pages.

---

# Screenshots

## Dashboard

<p align="center">
  <!-- Add screenshot here -->
</p>

## Velocity Profile & Event Log

<p align="center">
  <!-- Add screenshot here -->
</p>

---

# Features

- NASA DONKI API integration with configurable date range
- Quick range presets (7D / 30D / 90D / 1A)
- Stats cards: events detected, average velocity, fastest event, Earth-directed count
- Velocity profile chart with threshold line (Chart.js)
- Event log sortable by date or velocity
- Event detail modal with full CME metadata
- Status LED and live clock
- Error/alert bar with HTTP status feedback
- CRT visual effects (scanlines + noise overlay)
- Data persistence via Supabase
- Automated data updates via GitHub Actions

---

# Technology Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | CSS3 |
| Logic | JavaScript (Vanilla) |
| Charts | Chart.js |
| Fonts | Google Fonts (Michroma + JetBrains Mono) |
| Data Source | NASA DONKI API |
| Database | Supabase (PostgreSQL) |
| Backend | GitHub Actions (Python) |
| Deployment | GitHub Pages |
| Version Control | Git & GitHub |

---

# Architecture

```text
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   NASA DONKI    │────▶│  GitHub Actions  │────▶│    Supabase     │────▶│  GitHub Pages   │
│      API        │     │  (Python Script) │     │   (PostgreSQL)  │     │  (Static Site)  │
└─────────────────┘     └──────────────────┘     └─────────────────┘     └─────────────────┘
        │                        │                        │                        │
        │                        │                        │                        │
   CME Events              Batch Upsert            cme_events              Browser
   (JSON)                  (50 records)            execucoes               Dashboard
```

---

# Project Structure

```text
DONKI-CME/
│
├── index.html              # Application shell, component markup and modal
├── style.css               # Global styles, CRT effects, layout
├── script.js               # Supabase integration, chart rendering, event log logic
├── scripts/
│   └── fetch_nasa.py       # Pipeline de coleta de dados (GitHub Actions)
├── sql/
│   └── setup.sql           # Setup inicial do banco de dados Supabase
├── supabase/
│   └── migrations/
│       └── 001_create_tables.sql  # Migração inicial das tabelas
├── docs/
│   ├── ai-interaction.md   # Registro de interações com IA
│   └── reflexao.md         # Reflexões do grupo
├── .github/
│   └── workflows/
│       └── update-data.yml # Workflow de atualização automatizada
└── README.md
```

---

# Getting Started

## Prerequisites

- A modern browser (Chrome, Firefox, Edge, Safari)
- Node.js (optional, for local server)
- Python 3.11+ (for running the pipeline locally)

## Installation

```bash
git clone https://github.com/nasa-cme/Nasa-Cme-Dashboard.git
cd DONKI-CME
```

Open `index.html` in a browser, or use a local server:

```bash
# Node.js
npx serve .

# Python
python -m http.server 8000
```

---

# Configuration

## 1. Supabase Setup

1. Create a new project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** and execute the contents of `sql/setup.sql`
3. Note your project URL and keys from **Settings → API**

### Migração para bancos existentes

Se o banco já foi criado com a coluna `record_hash`, execute esta migração para removê-la:

```sql
-- Remover constraint e coluna record_hash (se existirem)
ALTER TABLE cme_events DROP CONSTRAINT IF EXISTS cme_events_record_hash_key;
ALTER TABLE cme_events DROP COLUMN IF EXISTS record_hash;
```

## 2. GitHub Secrets

Add the following secrets to your repository (Settings → Secrets and variables → Actions):

| Name | Description |
|------|-------------|
| `SUPABASE_URL` | Your Supabase project URL |
| `SUPABASE_SERVICE_KEY` | Your Supabase service_role key |
| `NASA_API_KEY` | Não necessária para a API DONKI do CCMC |

O pipeline usa a API DONKI oficial do CCMC, que aceita consultas CME sem
`api_key`. O secret `NASA_API_KEY` pode permanecer configurado no repositório
para outros usos, mas não é enviado nem necessário para este workflow.

## 3. Frontend Configuration

A configuração do Supabase já está feita em `script.js`. Os valores públicos (URL e chave `anon`) estão definidos no início do arquivo:

```javascript
const SUPABASE_URL = 'https://SEU_PROJETO.supabase.co';
const SUPABASE_ANON_KEY = 'SUA_CHAVE_ANON_PUBLICA';
```

**Importante:** Nunca inclua a chave `service_role` ou qualquer chave privada no frontend.

## 4. GitHub Pages

1. Go to **Settings → Pages**
2. Select **Source: Deploy from a branch**
3. Choose **main** branch and **/ (root)** folder
4. Click **Save**

---

# Deployment

Deployed on **GitHub Pages**. Any push to `main` is reflected immediately.

```
<!-- Substitua pela URL do GitHub Pages após publicar -->
https://nasa-cme.github.io/Nasa-Cme-Dashboard/
```

---

# Documentation

## Commit conventions

Commit messages must be written exclusively in English and follow the
[Conventional Commits](https://www.conventionalcommits.org/) format:

```text
<type>(<optional scope>): <imperative description in English>
```

Use a recognized type such as `feat`, `fix`, `docs`, `refactor`, `test`, `build`,
`ci`, `chore`, or `perf`. Keep the subject concise, use the imperative mood, and
do not end it with a period.

Examples:

```text
fix: validate NASA API key in workflow
docs: document GitHub Actions configuration
```

- [AI Interaction Log](docs/ai-interaction.md) - Registro de interações com IA
- [NASA DONKI/CME Diagnosis](docs/diagnostico_nasa_donki_cme_copilot.md) - Investigação da instabilidade da API
- [Reflexão do Grupo](docs/reflexao.md) - Reflexões sobre o desenvolvimento

## Pendentes

- `docs/tutorial.pdf` - Tutorial em PDF (versão inicial gerada)
- `docs/apresentacao.pdf` - Apresentação em PDF (versão inicial gerada)
- `docs/ai-interaction.md` - Histórico de prompts (a preencher com interações reais)
- `docs/reflexao.md` - Reflexão do grupo (a preencher)

---

# Known Limitations

- The DONKI/CCMC endpoint does not require a NASA API key for the current CME
  queries.
- DONKI events are typically published hours after detection, not in real time.
- Data is updated daily via GitHub Actions cron schedule (06:00 UTC).

## NASA DONKI/CME incident

The update workflow experienced a persistent failure in the NASA DONKI/CME
endpoint beginning on **September 30, 2026**:

- The last successful execution was on September 29, 2026, and returned 119 CME
  records.
- The first failure on September 30 returned HTTP `503 Service Unavailable`.
- Subsequent executions continued to fail at the NASA request, including manual
  runs and runs using the updated 80-second request timeout.
- A direct request using `DEMO_KEY` also returned HTTP `503`, confirming that the
  failure was not specific to the repository secret.
- The NASA APOD endpoint returned HTTP `200` during the same investigation, so
  the evidence points specifically to the DONKI/CME service rather than a
  complete NASA API outage.
- Supabase remained available and continued to record each execution with the
  `erro_critico` status.

The `API_KEY_INVALID` response observed during troubleshooting is documented as
a separate credential/configuration issue. The workflow reads `NASA_API_KEY`
from GitHub Secrets and does not embed the key in the source code. The timeout
increase does not resolve an HTTP `503`, because that status indicates a service
availability failure rather than a client-side timeout.

The pipeline now uses the official CCMC endpoint
`https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME`, which returned HTTP `200` and
90 records for the validation window used during the migration. When the CCMC
service is unavailable, retry the workflow later and check the execution logs.

The migration was confirmed in GitHub Actions run `41649309627` on November 3,
2026. The workflow fetched and normalized 90 records, wrote them to Supabase,
registered the execution with status `concluido`, and finished successfully.

---

# Contributors

| Name | GitHub |
|---|---|
| Murilo de Souza Cândido | [@murilotecoteco](https://github.com/murilotecoteco) |
| Guilherme | — |
| Yasmin C. | [@Yasminc21](https://github.com/Yasminc21) |

---

# License

This project is licensed under the MIT License.
