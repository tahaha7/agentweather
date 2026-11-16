# AgentWeather

Agentic multi-metric weather & AQI forecasting system for Karachi, Pakistan.

Built as part of the 10Pearls SHINE Internship (Cohort 10, Data Science track).

## What it does

Forecasts six weather and air-quality metrics **24 hours ahead** for Karachi:

| Metric | Unit |
| --- | --- |
| Air Quality Index (AQI) | US AQI |
| Temperature | °C |
| Precipitation | mm + rain probability |
| Humidity | % |
| UV Index | index |
| Wind | speed (km/h) + direction (°) |

On top of the forecasts sits a team of four LangGraph agents (Forecast, Alert,
Weather Report, Drift) that raise alerts, write a news-style weather report and
watch for drift. Everything runs automatically — ingestion every hour, training
every day.

## How it works

```
Weather & AQI APIs  -->  Ingestion (hourly)  -->  Feature Store (Hopsworks)
                                                        |
                                              Training (daily + on drift)
                                                        |
                                              Model Registry (best model wins)
                                                        |
                              Forecast Agent --> Alert Agent --> Weather Report Agent
                                                        |
                                              Drift Agent --> retrain if needed
                                                        |
                                              Streamlit dashboard
```

## Data sources

- **Open-Meteo** (forecast + archive + air quality APIs) — no key needed.
  Covers all six metrics. Verified live for Karachi.
- **AQICN** — ground-station AQI for Karachi (US Consulate, Saddar Town,
  Karachi Zoo and others). Needs a free token. Fused with Open-Meteo AQI.

## Project structure

```
agentweather/
├── src/
│   ├── ingestion/     # hourly fetch, fuse, impute, feature building
│   ├── training/      # daily training, model registry
│   ├── agents/        # LangGraph agents: forecast, alert, report, drift
│   └── dashboard/     # Streamlit app
├── notebooks/         # EDA and experiments
├── data/
│   ├── raw/           # raw API responses (not committed)
│   └── processed/     # cleaned features + targets (not committed)
├── .github/workflows/ # GitHub Actions: hourly ingestion, daily training
├── requirements.txt
└── README.md
```

## Build order

The project is built and pushed module by module, a few days apart:

1. Repo setup (this commit)
2. Ingestion script
3. Backfill — historical dataset
4. EDA
5. Features + Hopsworks feature store
6. Training pipeline (3 model families + naive baseline)
7. Model registry and evaluation
8. LangGraph agents
9. GitHub Actions automation
10. Streamlit dashboard (deployed)
11. Final report

## Setup

```bash
pip install -r requirements.txt
```

Copy `.env.example` to `.env` and add your keys (AQICN token, Hopsworks key,
Groq key) when each module needs them. Never commit `.env`.
