# atharvabidwalkar.github.io

# nyc-flight-routes
Interactive 3D globe dashboard mapping every flight route out of NYC airports (JFK, EWR, LGA) — colored by airline market share competition. Built with real BTS T-100 data (568K+ segment records, 2025).

# ✈️ NYC Flight Routes Intelligence

**Interactive 3D globe dashboard mapping every flight route out of New York City's three major airports — JFK, EWR, and LGA — colored by airline market share competition.**

---

## What This Shows

I mapped **350 routes** out of NYC's three airports for 2025 and colored them by competition level based on airline market share:

- 🟢 **Contested** — No single airline holds more than 50% of flights. Travelers have real choices.
- 🟡 **Partial** — One airline controls 50–70%. Competition exists but is limited.
- 🔴 **Monopoly** — One airline controls 70%+ of flights. Expect higher fares and fewer schedule options.

**Key finding:** Routes with healthy competition see **6x more daily departures** on average than monopoly routes. The hub-and-spoke model means United dominates EWR (67% share), Delta dominates JFK (38%), and LGA is split between Delta (44%) and American (31%). Many routes that appear as monopolies from one airport are actually competitive when you combine all three NYC airports — a pattern I call the **"hub split effect."**

---

## Data Source

Built with **real government data** — not estimates or samples:

- **BTS T-100 Segment Data** — 568,925 segment records from the Bureau of Transportation Statistics (Form 41), covering every flight operated by every carrier at JFK, EWR, and LGA for all 12 months of 2025
- **BTS Average Fare Data** — Average domestic itinerary fares by origin airport (2024)
- Cross-validated against OAG schedule data

Every number in this dashboard — departures, arrivals, airline flight counts, passenger volumes, seasonal patterns — comes directly from mandatory government filings by airlines.

---

## Features

### 🌍 Interactive Globe
- **Mapbox GL JS** with globe projection — zoom from space down to airport terminal level
- Great circle route arcs colored by competition level
- Click any destination to focus, hover for quick stats

### 📊 Dynamic Filtering
- **Airport selector** — ALL / JFK / EWR / LGA with instant re-filtering
- **Monthly data** — Real per-month BTS data for all 12 months. Toggle months to see seasonal variation
- **Airline filter** — Toggle Delta, United, JetBlue, American individually
- **Route type** — All / Domestic / International
- **Competition filter** — Click the legend to show/hide contested, partial, or monopoly routes
- **Route search** — Search by city name or airport code with dropdown autocomplete

### 📋 Rich Tooltips
Click any airport dot to see:
- Daily arrivals and departures (filtered by selected months)
- Full airline breakdown with flight counts and branded colors
- Domestic/International classification

### 💡 Smart Insights Engine
The right panel generates contextual analysis based on your current filters:

**When you search a destination (e.g., Nice):**
```
Routes to Nice (NCE):
JFK→NCE: Delta 99% — monopoly (JFK is Delta hub)
EWR→NCE: United 85% — monopoly (EWR is United hub)

Combined NYC view: Delta 49%, United 43% — overall contested
⚠ Individual routes show as monopoly but combined NYC service
  is competitive due to hub splits

📅 Peak: Jun (+22%) · Low: Jan (−35%)
👥 0.43M passengers/yr · avg 198 per flight
```

**Hub dominance analysis:**
```
EWR: United 67% (United hub)
JFK: Delta 38% (Delta hub)
LGA: Delta 44% / American 31% (shared hub)

⚠ 47 routes appear as monopoly from one airport but are actually
  competitive when combining all NYC airports (hub split effect)
```

### ✏️ Editable Data Table
Click **TABLE** to open a full data editor. Modify any departure or arrival number and click Save — the globe, insights, and all analytics update instantly.

---

## Competition Classification

Unlike the typical approach of counting airlines, this dashboard defines competition by **market share** — a more meaningful measure of actual competitive pressure:

| Level | Definition | NYC Routes | What It Means |
|-------|-----------|-----------|---------------|
| 🟢 Contested | Top carrier < 50% share | 49 | Real competition — multiple strong carriers |
| 🟡 Partial | Top carrier 50–70% share | 117 | One dominant carrier but alternatives exist |
| 🔴 Monopoly | Top carrier > 70% share | 184 | Single carrier controls the route |

---

## Hub Split Effect

A unique insight from this dashboard: **many NYC routes that appear as monopolies from a single airport are actually competitive when viewed across all three airports.**

Example — NYC to Boston:
- **LGA→BOS:** Republic 83% → Monopoly
- **JFK→BOS:** JetBlue 44% → Contested  
- **EWR→BOS:** United 50% → Partial

Each airport tells a different story, but NYC travelers choosing between all three have genuine competition.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Globe & Map | Mapbox GL JS v3.9 (globe projection) |
| Data | BTS T-100 Segment (568K records, 2025) |
| Processing | Python (openpyxl, csv, json) |
| Frontend | Vanilla HTML/CSS/JS — single file, no build step |
| Deployment | GitHub Pages |

The entire dashboard is a **single HTML file** (~225KB) with the processed route data embedded as JSON. No backend, no API calls (other than Mapbox tiles), no framework dependencies.

---

## Data Processing Pipeline

```
BTS T-100 Segment CSV (568,925 rows)
        ↓
Airport Data XLSX (origin, dest, month, carrier)
        ↓
Join on row index → filter NYC origins → aggregate by route/month/carrier
        ↓
Calculate market share → classify competition → compute daily averages
        ↓
Generate coordinates → embed as JSON → single HTML file
```

---

## Methodology

### Competition Classification
For each route, I calculate the market share of the top carrier (by departure count). Routes are classified as:
- **Contested** if no single carrier exceeds 50% of departures
- **Partial** if the top carrier holds 50–70%
- **Monopoly** if the top carrier holds 70%+

### Monthly Data
BTS T-100 provides monthly segment-level data. Daily averages are computed per-month (dividing by days in that month), allowing accurate seasonal filtering.

### Regional Carriers
Airlines like Republic, Endeavor, SkyWest, and GoJet operate flights branded as American Eagle, Delta Connection, or United Express. BTS T-100 data only reports the operating carrier, not the marketing carrier. These regionals are shown as-is because the data doesn't allow reliable attribution to mainline brands.
---

## License

MIT

---

*Built with BTS open data. Airline competition matters — it directly impacts fares, schedule frequency, and service quality for 140M+ NYC-area travelers annually.*
