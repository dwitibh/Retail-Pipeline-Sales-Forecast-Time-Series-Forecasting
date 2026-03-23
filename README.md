# Pipeline-Forecast-Prophet-Time-Series-Forecasting
A Jupyter Notebook project demonstrating daily sales forecasting using Facebook Prophet, applied to a retail pipeline dataset.
Project Overview
This project builds a univariate time series forecast for a specific product-store combination using the Prophet forecasting library. It covers the full pipeline from raw data ingestion through to visualising the forecast and its components.

Dataset
File: dataset.csv
ColumnDescriptionTime DateDate in ddmmyyyy integer format (e.g. 1012018 = 01 Jan 2018)ProductProduct IDStoreStore codeValueSales value

Rows: 1,080
Date range: Jan 2018 onwards
Scope: Single product (2667437) at a single store (QLD_CW_ST0203)


Notebook Structure
File: Pipeline_forcast.ipynb
Step 1 — Install Dependencies
python!pip install pystan
!pip install prophet
Step 2 — Import Libraries
pythonimport pandas as pd
from prophet import Prophet
import matplotlib.pyplot as plt
Step 3 — Load Data
pythondf = pd.read_csv('dataset.csv')
Step 4 — Parse Dates
The Time Date column uses a compact ddmmyyyy integer format and must be parsed into a proper datetime:
pythondf['year']  = df['Time Date'].apply(lambda x: str(x)[-4:])
df['month'] = df['Time Date'].apply(lambda x: str(x)[-6:-4])
df['day']   = df['Time Date'].apply(lambda x: str(x)[:-6])
df['ds']    = pd.to_datetime(df['year'] + '-' + df['month'] + '-' + df['day'])
Step 5 — Filter to Target Product & Store
pythondf = df.loc[(df['Product'] == 2667437) & (df['Store'] == 'QLD_CW_ST0203')]
df.drop(['Time Date', 'Product', 'Store', 'year', 'month', 'day'], axis=1, inplace=True)
df.columns = ['y', 'ds']
Step 6 — Train Prophet Model
pythonm = Prophet(interval_width=0.95, daily_seasonality=True)
model = m.fit(df)
Model configuration:

interval_width=0.95 — 95% uncertainty intervals
daily_seasonality=True — Captures intra-day patterns
Weekly and yearly seasonality enabled by default

Step 7 — Generate Forecast
pythonprediction = m.make_future_dataframe(periods=100, freq='D')
forecast   = m.predict(prediction)
Forecasts 100 days beyond the last observed data point.
Step 8 — Visualise Results
pythonplot = m.plot(forecast)
plt2 = m.plot_components(forecast)
The component plot breaks down the forecast into:

Trend — Long-term direction of sales
Weekly seasonality — Day-of-week patterns
Yearly seasonality — Annual cycles
Daily seasonality — Sub-daily variation (enabled explicitly)


Requirements
pandas
prophet
pystan
matplotlib
Install with:
bashpip install prophet




Key Outputs
OutputDescriptionyhatPoint forecastyhat_lowerLower bound of 95% uncertainty intervalyhat_upperUpper bound of 95% uncertainty intervaltrendUnderlying trend componentweeklyWeekly seasonal componentyearlyYearly seasonal component

Notes

The dataset contains only one product and one store, so filtering is straightforward.
Prophet requires columns named exactly ds (datestamp) and y (target value) — lowercase.
Dates must be parsed into proper datetime64 format before fitting.
The forecast extends 100 days into the future (through to approximately late March 2021).


File Structure
├── Pipeline_forcast.ipynb   # Main Jupyter Notebook
├── dataset.csv              # Raw sales data
└── README.md                # This file
