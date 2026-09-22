# Stock Market Analytics — Module 1

## Overview

This project contains the code and analysis for **Module 1 of the DataTalksClub Stock Markets Analytics Zoomcamp**.

The main purpose of this module is to understand how financial and economic data can be collected, processed, visualized, and used to support data-driven decisions in financial markets.

The analysis combines **stock-market data with macroeconomic indicators** such as Gross Domestic Product (GDP), inflation, interest rates, and other economic measures.

---

## Objectives

The main objectives of this module are to:

* Understand financial and economic data sources.
* Retrieve historical economic data using Python.
* Work with time-series data using Pandas.
* Calculate economic growth rates.
* Visualize economic indicators.
* Understand the relationship between economic conditions and financial markets.
* Prepare data for further stock-market analysis and modeling.

---

## Technologies and Libraries

The project is written in Python and uses the following libraries:

```python
import numpy as np
import pandas as pd

import yfinance as yf
import pandas_datareader as pdr

import plotly.graph_objs as go
import plotly.express as px

import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
```

### NumPy

`numpy` is used for numerical calculations and working with numerical data.

### Pandas

`pandas` is used to store, manipulate, clean, and analyze time-series data using DataFrames.

### yfinance

`yfinance` is used to obtain financial and stock-market data from Yahoo Finance.

### pandas-datareader

`pandas_datareader` is used to retrieve economic and financial data from sources such as the Federal Reserve Economic Data (FRED) database.

### Matplotlib

`matplotlib` is used to create statistical and economic visualizations.

### Plotly

`plotly` is used to create interactive financial and economic charts.

---

# Project Structure

The project is organized around the different economic and financial indicators analyzed in Module 1.

```text
stock-market-analytics/
│
├── Module01_Colab_Introduction_and_Data_Sources.ipynb
│
├── README.md
│
└── data/
    └── (optional local datasets)
```

The main notebook contains the Python code used to retrieve, process, analyze, and visualize the data.

---

# 1. Date Range

The analysis begins by determining the current date:

```python
end = date.today()

print(
    f'Year = {end.year}; month={end.month}; day={end.day}'
)
```

A historical start date is then created by going back 70 years:

```python
start = date(
    year=end.year - 70,
    month=end.month,
    day=end.day
)
```

This creates a long historical period for economic and financial analysis.

For example:

```text
Period for indexes: 1956-08-23 to 2026-08-23
```

This allows the analysis to examine long-term economic trends.

---

# 2. Gross Domestic Product (GDP)

The project retrieves **Real Potential Gross Domestic Product (GDPPOT)** from FRED.

```python
gdppot = pdr.DataReader(
    "GDPPOT",
    "fred",
    start=start
)
```

`GDPPOT` is the FRED identifier for Real Potential Gross Domestic Product.

The data is **quarterly**, meaning there are four observations per year.

---

## GDP Year-over-Year Growth

The project calculates year-over-year GDP growth using:

```python
gdppot['gdppot_us_yoy'] = (
    gdppot.GDPPOT /
    gdppot.GDPPOT.shift(4)
    - 1
)
```

Because GDP is quarterly:

```text
4 quarters = 1 year
```

Therefore:

```python
shift(4)
```

allows the current quarter to be compared with the same quarter one year earlier.

---

## GDP Quarter-over-Quarter Growth

Quarter-over-quarter growth is calculated using:

```python
gdppot['gdppot_us_qoq'] = (
    gdppot.GDPPOT /
    gdppot.GDPPOT.shift(1)
    - 1
)
```

Here:

```python
shift(1)
```

refers to the previous quarter.

Therefore:

* `shift(1)` → previous quarter
* `shift(4)` → same quarter one year earlier

---

## GDP Visualization

The project visualizes potential GDP and GDP growth using two y-axes.

```python
fig, ax = plt.subplots(figsize=(20, 6))
```

The potential GDP is displayed as an area:

```python
ax.fill_between(
    gdppot.index,
    gdppot.GDPPOT,
    color="red",
    alpha=0.3,
    label="US Potential GDP"
)
```

A secondary y-axis is created for GDP growth:

```python
ax2 = ax.twinx()
```

The GDP year-over-year growth is then plotted:

```python
ax2.plot(
    gdppot.gdppot_us_yoy,
    color="blue",
    marker="o",
    label="US Potential GDP Growth, % Y/Y"
)
```

The two axes are useful because GDP level and GDP growth have very different numerical scales.

---

# 3. Inflation — Core CPI

The project also analyzes **Core Consumer Price Index (Core CPI)**.

The FRED series used is:

```text
CPILFESL
```

It represents:

> Consumer Price Index for All Urban Consumers: All Items Less Food & Energy.

The data is retrieved using:

```python
cpilfesl = pdr.DataReader(
    "CPILFESL",
    "fred",
    start=start
)
```

Unlike GDP, Core CPI is **monthly**.

Therefore, there are approximately:

```text
12 observations per year
```

---

## Why Core CPI?

Core CPI excludes food and energy prices because these categories can be relatively volatile.

This allows analysts to examine an underlying trend in consumer prices without the short-term volatility associated with food and energy.

---

## CPI Time-Series Calculations

Because CPI is monthly, comparing the current value with the same month one year earlier requires:

```python
shift(12)
```

For example:

```python
cpilfesl["cpi_yoy"] = (
    cpilfesl.CPILFESL /
    cpilfesl.CPILFESL.shift(12)
    - 1
)
```

The important relationship is:

```text
GDP:
shift(4)  → one year ago

CPI:
shift(12) → one year ago
```

The number used with `shift()` depends on the frequency of the dataset.

---

# 4. Time-Series Analysis

A major concept in this project is **time-series data**.

Economic and financial observations are indexed by dates.

For example:

```text
2025-10-01
2026-01-01
2026-04-01
2026-07-01
```

The order of observations matters because the data represents changes over time.

The project therefore uses Pandas operations such as:

```python
.head()
.tail()
.shift()
```

### `.head()`

Displays the first rows of a DataFrame.

```python
cpilfesl.head()
```

### `.tail()`

Displays the last rows.

```python
gdppot.tail(15)
```

### `.shift()`

Moves observations so that current values can be compared with previous observations.

---

# 5. Financial Data and Economic Indicators

The project uses both financial-market data and macroeconomic indicators.

The general workflow is:

```text
Data Sources
     ↓
Data Collection
     ↓
Pandas DataFrames
     ↓
Data Processing
     ↓
Growth Calculations
     ↓
Visualization
     ↓
Economic Interpretation
```

The economic indicators provide context for understanding financial-market movements.

For example:

```text
GDP
 ↓
Economic growth

Core CPI
 ↓
Inflation

Interest Rates
 ↓
Cost of borrowing

Stock Prices
 ↓
Financial-market performance
```

These indicators can later be combined with financial-market data for more advanced analysis and modeling.

---

# 6. Key Concepts Learned

This module introduces several important concepts:

### Financial Data Sources

Understanding where reliable financial and economic data comes from.

### Time-Series Data

Understanding data where observations are ordered according to time.

### Data Frequency

Different datasets can have different frequencies:

```text
GDP       → Quarterly
CPI       → Monthly
Stocks    → Daily
```

### Lagged Values

Using previous observations with:

```python
shift()
```

### Growth Rates

Calculating changes using:

```python
current / previous - 1
```

### Data Visualization

Using Matplotlib and Plotly to identify trends and patterns.

### Economic Context

Understanding that stock-market analysis can benefit from considering the wider economic environment.

---

# Data Sources

The main data sources used in this module include:

* **FRED — Federal Reserve Economic Data**
* **Yahoo Finance**

The FRED datasets used in the current analysis include:

```text
GDPPOT   → Real Potential Gross Domestic Product
CPILFESL  → Core Consumer Price Index
```

---

# How to Run the Project

## 1. Clone the repository

```bash
git clone <repository-url>
```

## 2. Navigate into the project

```bash
cd stock-market-analytics
```

## 3. Install the required libraries

```bash
pip install numpy pandas yfinance pandas-datareader matplotlib plotly
```

## 4. Start Jupyter Notebook

```bash
jupyter notebook
```

Open the Module 1 notebook:

```text
Module01_Colab_Introduction_and_Data_Sources.ipynb
```

Run the cells sequentially.

---

# Conclusion

Module 1 establishes the foundation for stock-market analytics by introducing financial data sources, economic indicators, time-series data, data processing, and visualization.

The module demonstrates how Python can be used to retrieve historical economic information and transform raw data into useful indicators such as GDP growth and inflation.

These concepts provide the foundation for the later modules, where the project moves toward deeper dataframe analysis, financial modeling, trading strategies, simulation, deployment, and automation.
