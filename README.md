# Weather Time Series Analytics

End-to-end time series analysis pipeline built in Python. I pull real historical and forecast weather data from the Open-Meteo API for any city in the world, then work through the full analytics workflow: exploratory analysis, time series decomposition, stationarity testing, anomaly detection, and forecasting. Everything runs in a single notebook with no manual data downloads required.

The example outputs shown here are for Seattle, Washington, covering five years of hourly observations (43,824 records).

---

## What the notebook does

**Part 1: Data ingestion**

I use the Open-Meteo geocoding API to convert any city name into coordinates and a timezone. The archive API then pulls five years of hourly historical data, which I cache to disk so repeat runs do not re-download it. A separate forecast API call retrieves the most recent week of observations plus a 16-day forward forecast. The two sources are joined to form a seamless, gap-free series.

The four variables collected at each hourly timestamp are: temperature (C), relative humidity (%), mean sea-level pressure (hPa), and wind speed at 10m (km/h).

**Part 2: Exploratory data analysis**

I examine the shape, completeness, and statistical properties of the dataset. This includes histograms and box plots for each variable to surface outliers, and a correlation heatmap to quantify the relationships between them. For Seattle, temperature and humidity show a moderate negative correlation, which is the expected inverse pattern.

**Part 3: Time series decomposition**

I separate the temperature signal into three components using a rolling window approach: a long-run trend, a seasonal cycle, and a residual. Plotting each component independently makes it possible to see whether warming or cooling patterns are present in the trend, how strong the annual seasonal swing is, and how much unexplained noise remains in the residual.

**Part 4: Stationarity check**

I apply the Augmented Dickey-Fuller test to confirm whether the series is stationary, and plot the autocorrelation function on both the original series and its first difference. This step is included because stationarity assumptions affect which forecasting and decomposition methods are valid. I subsample the series for the ACF plot to keep the computation fast on large datasets.

**Part 5: Anomaly detection**

I use Isolation Forest, an unsupervised algorithm, to flag unusual observations without needing labelled training data. Isolation Forest works by building many random trees and scoring each observation by how quickly it gets isolated: genuine anomalies get isolated in very few splits, giving them a short average path length and a high anomaly score. I apply this across all four weather variables simultaneously. For Seattle, 439 observations were flagged out of 43,824, a rate of approximately 1%.

**Part 6: Forecasting**

I build a linear regression model with sinusoidal features to capture the daily and annual temperature cycles. Specifically, I encode the hour of day and day of year as sine and cosine pairs, which allows the model to learn cyclical patterns directly rather than treating time as a monotonically increasing integer.

The model is evaluated on a held-out test set and then used to generate a 48-hour forward forecast. I compare this forecast head-to-head against the corresponding window from Open-Meteo's own professional forecast, which serves as an honest external benchmark.

**Part 7: Summary dashboard**

The final cell produces a multi-panel figure tying the key results together: the five-year temperature record with weekly trend overlay, a temperature versus humidity scatter plot, the average daily temperature cycle, a model performance bar chart, and a printed summary of the key statistics.

---

## Results (Seattle, Washington)

| Metric | Value |
|---|---|
| Observations | 43,824 hourly records |
| Span | 1,825 days |
| Mean temperature | 11.5 C (+/- 6.4 C) |
| Mean humidity | 77% |
| Mean pressure | 1,017 hPa |
| Anomalies flagged | 439 (approx. 1%) |
| Test MAE | 2.40 C |
| Test RMSE | 3.01 C |
| Forecast horizon | 384 hours |

---

## Dashboard

![Complete Analysis Dashboard](07_dashboard.png)

---

## How to run it

Clone the repository and install the dependencies listed below. Open `Weather_Analysis.ipynb` in Jupyter and run all cells. The notebook will prompt you for a city name at the data ingestion step. On the first run for a given city, the archive download takes roughly 30 to 60 seconds. All subsequent runs for the same city use the local cache and complete almost instantly.

To change the city, clear the cache file or set a different city name at the top of Part 1.

---

## Dependencies

```
requests
numpy
pandas
matplotlib
seaborn
scikit-learn
statsmodels
jupyter
```

Install with:

```bash
pip install requests numpy pandas matplotlib seaborn scikit-learn statsmodels notebook
```

---

## Project structure

```
Weather_Analysis.ipynb   Main notebook
07_dashboard.png         Summary dashboard output
README.md                This file
```

---

## Data source

All data is fetched live from [Open-Meteo](https://open-meteo.com), a free and open-source weather API that requires no API key for historical or forecast data. The archive data lags real-time by approximately one week, which is why the forecast API is used to fill the gap between the archive cutoff and today.

---

## Notes

The forecasting model is intentionally kept simple. A linear regression with sinusoidal features is a well-understood, interpretable baseline for cyclical time series. More complex models such as Prophet, SARIMA, or gradient-boosted trees could reduce the MAE further, but the goal here was to demonstrate the full analytics pipeline clearly, not to chase the lowest possible error.
