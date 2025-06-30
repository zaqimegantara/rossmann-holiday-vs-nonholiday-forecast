# Rossmann Holiday vs Non-Holiday Forecasting

This project investigates how holidays impact daily sales forecasts at Rossmann stores by comparing model performance with and without holiday features. The first step is to determine whether holidays significantly affect sales using Ordinary Least Squares (OLS) regression. If a significant impact is found, the next step is to evaluate whether incorporating holiday information improves forecasting accuracy. To do this, forecasting results from models that include holiday variables are compared against those that do not, using two popular time series models: Prophet and SARIMA.

---

## Problem Statement

Retail sales often fluctuate around holidays—some holidays lead to increased demand, while others may cause temporary store closures. These effects can disrupt typical sales patterns and, if not accounted for, reduce the accuracy of sales forecasts.
This project aims to address **two key questions:**

> - Does holidays have an effect on sales?
> - Does including holiday information lead to better daily sales predictions?

By first testing the significance of holidays using Ordinary Least Squares (OLS) regression and then evaluating forecasting performance with and without holiday features using Prophet and SARIMA, this project seeks to understand the real impact of holidays on sales and predictive accuracy.

---

## How It Was Done

### 1. Dataset
- **Source**: Rossmann Store Sales dataset from Kaggle (https://www.kaggle.com/competitions/rossmann-store-sales)  
- **File used**: `train.csv` — contains daily sales data for multiple Rossmann stores.
- **Timeframe**: January 2013 – July 2015  
- **Focus Store**: Store 262 (the store with the highest sales volume)
- **Key Features Used**:
  - `Sales`, `Date`, `Promo`, `SchoolHoliday`, `StateHoliday`

### 2. Preprocessing
- Converted holiday types (`StateHoliday = a, b, c`) into dummy variables.
- Removed non-operating days (`Sales = 0`)
- Extracted day-of-week and converted date columns
- Created two sets of training data:
  - One **without** holiday info
  - One **with** holiday dummies as features

### 3. OLS Regression (Holiday Effect Analysis)
- Used **Ordinary Least Squares (OLS)** regression to evaluate whether holidays have a statistically significant effect on daily sales.
- Included features like `Promo`, `SchoolHoliday`, `DayOfWeek`, and dummy variables for holiday types (`StateHoliday_a`, `b`, `c`).
- A `p-value` less than 0.05 indicates that holidays have a statistically significant impact on sales.

### 4. Modeling
Two time series forecasting models were applied:

- **Prophet**:
  - Additive model with components for trend, seasonality, and optional holidays
- **SARIMA (and SARIMAX)**:
  - Statistical model with seasonal components
  - SARIMAX used for incorporating external holiday features

Each model was trained and tested in **two versions**:
- Without holidays
- With holidays

### 5. Evaluation
Models were compared using:
- **MAE** (Mean Absolute Error)
- **RMSE** (Root Mean Squared Error)
- **MAPE** (Mean Absolute Percentage Error)


---

## Results

**OLS Regression Analysis**

![alt text](ols_regression_result.png)

**Key Insights:**

Holiday variables `StateHoliday_a`, `StateHoliday_b`, and `StateHoliday_c` all had `p-values` < 0.05, indicating a statistically significant positive effect on sales.

Specifically:

  -  **StateHoliday_a**: On average, sales increase by **12,570 units**
  - **StateHoliday_b**: On average, sales increase by **15,170 units**
  - **StateHoliday_c**: On average, sales increase by **8,707 units**

  Non-holiday variable `Promo` also had a strong positive effect (coefficient = 2342.69, `p` = 0.000).
    
  `SchoolHoliday` was not statistically significant (p = 0.422).

  These results support the decision to include holiday features as exogenous variables in the forecasting models.

## **SARIMA & Prophet Modeling**


| Model                     | MAE      | RMSE     | MAPE    |
|--------------------------|----------|----------|---------|
| Prophet (No Holidays)    | 2328.58  | 3529.33  | 10.69%  |
| Prophet (With Holidays)  | 2229.70  | 3582.18  | 9.99%   |
| SARIMA (No Holidays)     | 4045.21  | 5343.49  | 17.77%  |
| **SARIMA (With Holidays)** | **1886.15** | **2635.34** | **9.10%**  |

**Key Insights :**
- Including holiday data **improved forecast accuracy** across all models.
- The most significant improvement was seen in **SARIMA**, which performed best overall when holidays were included.
- Prophet also improved, but was generally smoother and less reactive to sharp peaks.

---

## Forecast Visualization: Actual vs Predicted Sales

To visualize the impact of including holiday features in forecasting, we compared actual sales against model predictions in two settings:

1. **Without Holidays**
   
![alt text](result_without_holidays.png)

- When holidays were excluded, both models failed to accurately predict sharp peaks and troughs.

- Prophet showed an overall increasing trend but missed high-variance holiday effects.

- SARIMA struggled more, underestimating sales during holiday periods.

2. **With Holidays**
   
![alt text](result_with_holidays.png)

- Both Prophet and SARIMA models that included holiday variables followed the spikes and dips in actual sales more closely.

- The models were better at capturing peak sales periods, especially around holiday-related surges.

- SARIMA responded more sharply to weekly fluctuations, while Prophet produced smoother trends.

## Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/zaqimegantara/rossmann-holiday-vs-nonholiday-forecast.git
cd rossmann-holiday-vs-nonholiday-forecast  # or the folder you cloned into
```

### 2. (Optional) Create a Virtual Environment
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
```

### 3. Install Dependencies
Install all required packages:

```bash
pip install -r requirements.txt
```
Or manually install core libraries:

```bash
pip install pandas==2.2.3 numpy==1.24.3 matplotlib==3.10.0 scikit-learn==1.6.1 statsmodels prophet
```
If Prophet fails to install, run:
```bash
pip install pystan==2.19.1.1
pip install prophet==1.1.5
```
### 4. Run the Notebooks
You can open the notebooks in VS Code or Jupyter Notebook:

```bash
jupyter notebook
```
Run these in order:

- `01_regression_holiday_impact.ipynb`

- `02_modeling_prophet_sarima.ipynb`

## Project Structure
```
├── data/
│ └── train.csv # Raw sales data
│
├── notebooks/
│ ├── 01_regression_holiday_impact.ipynb # OLS regression analysis
│ └── 02_modeling_prophet_sarima.ipynb # Forecasting with Prophet and SARIMA
│
├── ols_regression_result.png # Regression summary image
├── result_with_holidays.png # Forecast visualization (with holidays)
├── result_without_holidays.png # Forecast visualization (without holidays)
│
├── requirements.txt # Dependencies list
└── README.md # Project overview and instructions
```
## License

This project is licensed under the [MIT License](./LICENSE).



