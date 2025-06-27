# Hourly Load Forecasting for New Hampshire Electricity Consumption

## Project Overview

This repository contains a data analysis and machine learning project to forecast the hourly electricity load (Real-Time Demand) in New Hampshire. The project utilizes historical hourly System-Wide Market Data (SMD) from 2020 to 2022, including load demand and weather information. The core of the project is a Jupyter Notebook (`Code.ipynb`) that walks through data loading, exploratory data analysis, feature engineering, and the development of predictive models.

The analysis demonstrates the process of building increasingly complex regression models and evaluates their performance, culminating in a comparison between a final Linear Regression model and a Random Forest Regressor.

## Data

The dataset consists of hourly electricity and weather data for New Hampshire spanning three years (2020-2022). The data was originally provided in three separate Excel files (`2020_smd_hourly.xlsx`, `2021_smd_hourly.xlsx`, `2022_smd_hourly.xlsx`), which were then combined for the analysis.

The key variables used in the models are:
*   **`RT_Demand`**: Real-Time Load Demand in Megawatts (MW) - The target variable.
*   **`Hr_End`**: The hour of the day (1-24).
*   **`Dry_Bulb`**: The dry-bulb temperature.
*   **`Dew_Point`**: The dew point temperature.
*   **`Date`**: The date of the recording.

## Methodology

The analysis follows a structured approach to build and evaluate forecasting models.

### 1. Data Loading and Preprocessing

*   Loaded the hourly data for 2020, 2021, and 2022 from their respective Excel files.
*   Filtered the data to include only the necessary columns: `Date`, `Hr_End`, `Dry_Bulb`, `Dew_Point`, and `RT_Demand`.
*   Concatenated the three yearly DataFrames into a single comprehensive dataset.

### 2. Exploratory Data Analysis (EDA)

*   **Correlation Analysis**: A correlation matrix revealed notable relationships, especially `RT_Demand` with `Hr_End` (0.43) and `Dry_Bulb` (0.25).
*   **Visualizations**:
    *   Scatter plots showed clear daily patterns in load demand and non-linear relationships between temperature (`Dry_Bulb`, `Dew_Point`) and demand.
    *   Histograms revealed the distribution of the key variables. `RT_Demand` was observed to be right-skewed.
    *   A time-series plot of `RT_Demand` over the three years highlighted seasonal patterns.

### 3. Feature Engineering

To capture the complex relationships in the data, several new features were created:
*   **Day of the Week**: A `DAYOFWEEK` categorical feature was extracted from the `Date`.
*   **Polynomial Terms**: Quadratic terms for temperature (`I(Dry_Bulb**2)`, `I(Dew_Point**2)`) were added to model the non-linear "U-shaped" effect of temperature on load.
*   **Interaction Terms**: An interaction term between `Dry_Bulb` and `Dew_Point` (`Dry_Bulb:Dew_Point`) and interactions with `DAYOFWEEK` were included to capture combined effects.
*   **One-Hot Encoding**: The categorical features `Hr_End` and `DAYOFWEEK` were converted into numerical format using one-hot encoding.

### 4. Modeling and Evaluation

A series of models were developed and tested:

*   **Ordinary Least Squares (OLS) Regression**:
    *   Ten successive linear models (`mod1` through `mod10`) were built, progressively adding features like temperature, polynomial terms, interaction terms, and day of week effects.
    *   The model performance was tracked using R-squared, which improved from **0.446** (with only `Hr_End`) to **0.841** in the most complex model (`mod10`).
    *   ANOVA tables were used to confirm the statistical significance of the added features.

*   **Forward Stepwise Selection**:
    *   This method was used to systematically identify the most predictive subset of all 34 engineered features.
    *   Criteria like Adjusted R-squared, AIC, BIC, and Mallows's C_p were used for evaluation, all indicating that the full model with all 34 features was optimal.

*   **Final Model Training and Comparison**:
    *   The data was split into a training set (data before December 1, 2022) and a testing set (data from December 1, 2022, onwards).
    *   Two models were trained on the selected features:
        1.  **Linear Regression**: The final linear model was trained and evaluated on the test set.
        2.  **Random Forest Regressor**: A `RandomForestRegressor` with 100 estimators was trained for comparison.

## Results

The models were evaluated on the test set (December 2022 data) using Root Mean Squared Error (RMSE) and Mean Squared Error (MSE).

| Model | MSE | RMSE | R² Score |

| :--- | :--- | :--- | :--- |

| Linear Regression | 11663.61 | 107.99 | 0.655 |

| Random Forest Regressor | 6426.51 | 80.17 | - |

The **Random Forest Regressor** significantly outperformed the Linear Regression model, achieving a much lower MSE and RMSE. This indicates that the Random Forest's ability to capture complex, non-linear interactions in the data led to more accurate forecasts.

## How to Run

### Prerequisites

The project was developed in a Google Colab environment. The following Python libraries are required:
*   `numpy`
*   `pandas`
*   `matplotlib`
*   `seaborn`
*   `statsmodels`
*   `scikit-learn`
*   `google-colab` (for mounting Google Drive)

### Setup

1.  Clone the repository.
2.  Upload the `Code.ipynb` notebook to your Google Colab environment.
3.  Place the data files (`2020_smd_hourly.xlsx`, `2021_smd_hourly.xlsx`, `2022_smd_hourly.xlsx`) into the root of your Google Drive (`My Drive`).
4.  Run the cells in the `Code.ipynb` notebook. The code will automatically mount your Google Drive to access the data files.
