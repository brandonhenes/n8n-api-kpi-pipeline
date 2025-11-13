# n8n API → KPI Pipeline (error-safe, charted)

A production-style n8n workflow that:
1) calls an external API,  
2) branches on success/failure (no hard crashes),  
3) logs every run to a sheet/DB, and  
4) publishes a KPI chart image via QuickChart.

<img src="assets/flow.png" width="800"/>

## What this does
- **API:** Open-Meteo (no key) for simple demo.
- **Resilience:** “Include Response Headers/Status” + “Never Error” → IF node checks 2xx.
- **Logging:** Appends `timestamp, run_id, source, kpi_name, kpi_value, sample_size, status, error`.
- **KPI:** Average of last 24h values → rendered as PNG with QuickChart.
- **Swap-in ready:** Replace the API + KPI function to track ticket SLA, backlog, agent success, etc.

## Files
- `workflow_n8n_api-kpi.json` — import into n8n (top-right **… → Import**).
- `api_kpi_workflow_overview.png` — screenshots of the flow.
- `api_kpi_workflow_overview_IF.png` — screenshot of the IF output.
- `api_kpi_workflow_overview_chart.png` — screenshot of the chart output.
- `kpi_log.csv` — example of the log row format.

## Setup (5 minutes)
1. **Import:** In n8n, **… → Import** → choose `workflow_n8n_api-kpi.json`.
2. **HTTP node:** (already configured) uses Open-Meteo; set `&temperature_unit=fahrenheit` if you want °F.
3. **Sheets (optional):** Point the Google Sheets node at your `kpi_log` sheet (or swap to Notion/Postgres).
4. **QuickChart:** No key needed; response set to **File** (PNG).
5. **Run:** Execute once; see a row in your sheet and a chart image output.

## How it works
Schedule → HTTP Request (API) → IF (2xx?) → Edit Fields (compute KPI) → Append to Sheet → HTTP (QuickChart)
└─ false → Edit Fields (normalize error) → Append to Sheet

