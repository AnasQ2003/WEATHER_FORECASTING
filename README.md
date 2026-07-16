# 🌦️ Weather Forecasting System

<div align="center">

![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-11557c?style=for-the-badge)
![SARIMA](https://img.shields.io/badge/SARIMA-Time%20Series-orange?style=for-the-badge)
![OpenWeatherMap](https://img.shields.io/badge/OpenWeatherMap-API-EB6E4B?style=for-the-badge&logo=openweathermap&logoColor=white)
![Tkinter](https://img.shields.io/badge/Tkinter-GUI-blue?style=for-the-badge)
![Statsmodels](https://img.shields.io/badge/Statsmodels-ARIMA-green?style=for-the-badge)

<br/>

</div>

---

## 📋 Project Overview

The **Weather Forecasting System** is a Data Science project developed as part of the **Introduction to Data Science Lab. It leverages **machine learning and statistical time-series modeling** to predict future weather conditions using live data fetched from the **OpenWeatherMap API**.

The application provides a clean **Tkinter GUI** where users can type any city name to:
- View real-time current weather conditions
- Run ARIMA-based **3-day temperature forecasts** with confidence intervals
- See interactive embedded **matplotlib** plots
- Log all forecasts and diagnostics to a local `weather_logg.txt` file

---

## 👨‍💻 Team Members

| Name | Enrollment No. |
|---|---|
| Abdul Samad | 02-134212-005 |
| Anas Ahmed Qureshi | 02-134212-042 |

**Course Instructor:** Ms. Saba Imtiaz
**Lab Instructor:** Rabia Amjad
**Class:** BS (CS) — 6(A)

---

## ✨ Features

| Feature | Description |
|---|---|
| 🌡️ **Live Current Weather** | Fetches real-time temperature, sky conditions via OpenWeatherMap REST API |
| 📈 **ARIMA Forecasting** | Fits `SARIMAX(1,1,1)` to 5-day/3-hourly OpenWeatherMap forecast data |
| 🔬 **ADF Stationarity Test** | Runs Augmented Dickey-Fuller test before and after differencing |
| 📊 **Interactive Forecast Plot** | Embedded Matplotlib chart: observed vs. predicted temps + confidence band |
| 📝 **Event Logging** | All weather lookups and ADF stats written to `weather_logg.txt` |
| 🖥️ **Tkinter GUI** | City input field, two action buttons, scrollable forecast text box |
| 🔄 **Scrollable Graph Canvas** | Vertically-scrollable graph frame handles varying forecast horizons |

---

## 🛠️ Technology Stack

| Library | Role |
|---|---|
| `requests` | HTTP client — calls OpenWeatherMap `/weather` and `/forecast` endpoints |
| `pandas` | Structures JSON forecast data into a time-indexed `DataFrame` |
| `matplotlib` | Renders observed vs. forecast temperature line chart with CI shading |
| `statsmodels` | Provides `SARIMAX` model and `adfuller` ADF test |
| `numpy` | Numerical support (differencing, array ops) |
| `tkinter` | Desktop GUI — labels, entry, buttons, `ScrolledText`, canvas |
| `FigureCanvasTkAgg` | Embeds matplotlib figure directly into Tkinter window |

---

## 🧠 How It Works

### 1. Data Collection
The app calls the **OpenWeatherMap API** (free tier):
- `/data/2.5/weather?q={city}` — current temperature + description  
- `/data/2.5/forecast?q={city}` — 5-day/3-hour forecast (40 readings)

### 2. Data Preprocessing
```python
def prepare_forecast_data(data):
    forecast_data = [{'ds': entry['dt_txt'], 'y': entry['main']['temp']}
                     for entry in data['list']]
    df = pd.DataFrame(forecast_data)
    df['ds'] = pd.to_datetime(df['ds'])
    df.set_index('ds', inplace=True)
    return df
```

### 3. Stationarity Check (ADF Test)
Before fitting ARIMA, an **Augmented Dickey-Fuller (ADF)** test checks whether the time series is stationary:
- If the p-value > 0.05 → non-stationary → apply **first differencing**
- ADF is run twice (pre- and post-differencing) and all statistics are logged to the GUI

```python
def adf_test(series):
    result = adfuller(series, autolag='AIC')
    # Outputs: ADF Statistic, p-value, Critical Values (1%, 5%, 10%)
```

### 4. ARIMA Model Fitting
```python
def fit_arima_model(series, order=(1,1,1)):
    model = SARIMAX(series, order=order)
    model_fit = model.fit(disp=False)
    return model_fit
```
The `SARIMAX(p=1, d=1, q=1)` model captures:
- **AR(1)** — autoregressive term
- **I(1)** — first-order differencing for stationarity
- **MA(1)** — moving average term

### 5. Forecasting
```python
def predict_future_weather(model, steps=3):
    forecast = model.get_forecast(steps=steps)
    return forecast.predicted_mean, forecast.conf_int()
```
Returns predicted mean temperatures + **95% confidence intervals** for the next `steps` periods.

### 6. Visualization
```python
def plot_forecast(df, forecast, conf_int, city):
    ax.plot(df.index, df, label='Observed Temperature')
    ax.plot(forecast.index, forecast, color='red', label='Forecasted Temperature')
    ax.fill_between(forecast.index, conf_int.iloc[:,0], conf_int.iloc[:,1],
                    color='pink', alpha=0.3)
```
The pink shaded region represents the **95% confidence band** around the forecast.

---

## 📂 Project Structure

```
WEATHER_FORECASTING/
│
├── weather_forecasting.py      # Main Python application (Tkinter GUI + ARIMA)
├── Project Proposal.docx       # Original project proposal submitted to Bahria University
├── Project Report.pdf          # Full project report with methodology and results
├── weather_logg.txt            # Auto-generated log file (created at runtime)
│
└── screenshots/
    └── bu_logo.jpg             # Bahria University official logo
```

---

## 🚀 Getting Started

### Prerequisites
- Python 3.8 or higher
- An **OpenWeatherMap** API key ([get free at openweathermap.org](https://openweathermap.org/api))

### 1. Clone the Repository
```bash
git clone https://github.com/AnasQ2003/WEATHER_FORECASTING.git
cd WEATHER_FORECASTING
```

### 2. Install Dependencies
```bash
pip install requests pandas matplotlib statsmodels numpy
```

### 3. Configure API Key
Open `weather_forecasting.py` and replace the API key on lines 25 and 39:
```python
api_key = 'YOUR_OPENWEATHERMAP_API_KEY'
```

### 4. Run the App
```bash
python weather_forecasting.py
```

---

## 🖥️ How to Use

1. **Launch** the app — a Tkinter window titled "Weather Forecasting App" opens
2. **Enter a city name** (e.g., `Karachi`, `London`, `New York`) in the input field
3. **Click "Get Current Weather"** to fetch live temperature and sky condition
4. **Click "Get Weather Forecast"** to:
   - Run ADF stationarity tests (output shown in the scrollable text box)
   - Fit ARIMA(1,1,1) to OpenWeatherMap 5-day forecast data
   - Display next 3-period temperature predictions with dates
   - Embed a matplotlib chart in the scrollable graph area below

---

## 📊 Data Pipeline Flow

```
OpenWeatherMap API
       │
       ▼
  JSON Response
       │
       ▼
  DataFrame (dt_txt → index, temp → y)
       │
       ▼
  ADF Test (stationarity check)
       │
       ├── Not stationary → First Differencing → ADF Test again
       │
       ▼
  SARIMAX(1,1,1) Fit
       │
       ▼
  Forecast (predicted_mean + conf_int)
       │
       ▼
  Matplotlib Plot + Tkinter Canvas embed
       │
       ▼
  weather_logg.txt (all output logged)
```

---

## 📚 Data Science Concepts Applied

| Concept | Application |
|---|---|
| **REST API Integration** | OpenWeatherMap `/weather` & `/forecast` endpoints using `requests` |
| **Time Series Analysis** | 5-day 3-hourly forecast as a time-indexed pandas Series |
| **Stationarity Testing** | Augmented Dickey-Fuller (ADF) test via `statsmodels.tsa.stattools` |
| **Differencing** | First-order differencing to make non-stationary series stationary |
| **ARIMA Modeling** | `SARIMAX(1,1,1)` via `statsmodels.tsa.statespace.sarimax` |
| **Confidence Intervals** | 95% CI on forecast using `get_forecast().conf_int()` |
| **Data Visualization** | Line charts with CI shading via `matplotlib` |
| **DataFrame Manipulation** | JSON-to-DataFrame conversion, datetime indexing with `pandas` |
| **GUI Development** | Tkinter widgets: Entry, Button, ScrolledText, Canvas, Frame |
| **Logging** | Append-mode file logging of all forecasts and test statistics |

---

## 📄 Project Documents

| Document | Description |
|---|---|
| [`Project Proposal.docx`](./Project%20Proposal.docx) | Initial project proposal submitted to Ms. Saba Imtiaz — scope, abstract, functionalities, module distribution |
| [`Project Report.pdf`](./Project%20Report.pdf) | Full academic report with literature review, implementation details, methodology, and results |

---

## 🏫 Academic Context

| Field | Details |
|---|---|
| **University** | Bahria University, Karachi Campus |
| **Department** | Computer Science |
| **Degree** | BS (Computer Science) |
| **Semester** | 6th Semester |
| **Course** | Introduction to Data Science |
| **Course Code** | CSL-487 |
| **Instructor** | Ms. Saba Imtiaz |
| **Lab Instructor** | Rabia Amjad |

---

## ⚠️ Notes

- The **API key** hardcoded in the source file (`13d6f372052b76fdc44bd6057ffb9dfc`) is a development key — **replace it with your own** from [openweathermap.org](https://openweathermap.org/api) for production use.
- ARIMA forecast steps are currently set to `steps=3` (next 3 time periods from the 5-day forecast dataset).
- The log file `weather_logg.txt` is appended on every run — delete or clear it periodically.
- The app requires an active internet connection to fetch live weather data.

---

## 📄 License

```
MIT License

Copyright (c) Weather Forecasting---2026 AnasQ2003

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

## 👨‍💻 Author

**Anas Ahmed Qureshi.** — [@AnasQ2003](https://github.com/AnasQ2003)

---

<div align="center">
  <p>Built with ❤️ by <strong>Anas</strong></p>
  
 <div align="center">

Made with 🔥 and a lot of ☕

**⭐ If you found this useful, please star the repository!**

</div>
