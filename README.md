# Airfare Pricing and Purchase-Timing Analysis

An end-to-end analytics project examining how booking timing, airline, cabin class, route, duration, and number of stops relate to domestic airfare prices in India.

[View the Interactive Tableau Dashboard](https://public.tableau.com/app/profile/.78083245/viz/AirfarePricingandPurchase-TimingAnalysis/AirfarePricingDashboard?publish=yes)

## Business Problem

Travelers often lack clear information about how purchasing decisions and itinerary characteristics affect ticket prices. This project analyzes 300,153 airfare observations to:

* Identify major airfare drivers
* Compare pricing across airlines, routes, and cabin classes
* Measure the relationship between advance booking and ticket prices
* Build and validate a machine-learning model for airfare estimation
* Translate the findings into practical purchase-timing insights

## Dataset

The dataset contains 300,153 Indian domestic flight observations across:

* 6 airlines
* 30 directional routes
* 1,561 flight numbers
* Economy and business cabins
* Booking windows from 1 to 49 days before departure

The data passed quality checks with no missing values, duplicate rows, nonpositive prices, or invalid same-city routes.

[Dataset on Kaggle](https://www.kaggle.com/datasets/shubhambathwal/flight-price-prediction)

## Key Findings

### Booking Timing

Advance booking is particularly important for economy travelers.

* Economy average fare: ₹11,634 at 1–7 days versus ₹4,789 at 41–49 days, representing a 142.9% last-minute premium.
* Business average fare: ₹56,745 at 1–7 days versus ₹51,559 at 41–49 days, representing a 10.1% premium.

### Airline Pricing

Airline comparisons were performed separately by cabin class.

* Business: Vistara averaged ₹55,477, compared with ₹47,131 for Air India.
* Economy: Vistara averaged ₹7,807, while AirAsia averaged ₹4,091.
* Only Air India and Vistara offered business-class observations in the dataset.

### Routes

Route-level prices differed substantially within each cabin class.

* Business: Bangalore → Kolkata was highest at ₹58,855; Mumbai → Delhi was lowest at ₹43,846.
* Economy: Kolkata → Chennai was highest at ₹8,012; Mumbai → Hyderabad was lowest at ₹5,775.

### Number of Stops

Higher-stop itineraries were associated with higher average fares. However, this pattern should not be interpreted as a causal stop premium because stops are also associated with route and flight duration.

## Predictive Modeling

A Random Forest regression pipeline was developed using:

* One-hot encoding for categorical variables
* Direct passthrough for numerical variables
* Airline, cities, departure and arrival periods, stops, cabin class, duration, and days before departure as predictors

Flight number was excluded from the predictors to reduce memorization risk.

### Random-Split Results

| Metric | Random Forest | Class-Median Baseline |
| ------ | ------------: | --------------------: |
| MAE    |        ₹1,297 |                ₹4,824 |
| RMSE   |        ₹2,792 |                ₹7,950 |
| R²     |        0.9849 |                0.8776 |

The Random Forest reduced MAE by 73.1% and RMSE by 64.9% relative to the baseline.

### Grouped-Holdout Validation

To test generalization more rigorously, observations were split by flight number:

* Training flight numbers: 1,248
* Testing flight numbers: 313
* Overlapping flight numbers: 0

| Metric | Grouped Holdout |
| ------ | --------------: |
| MAE    |          ₹2,898 |
| RMSE   |          ₹5,278 |
| R²     |          0.9449 |

The lower grouped-holdout performance indicates that the random split was optimistic, but the model retained strong predictive performance on unseen flight numbers.

## Feature Importance

Permutation importance identified the leading predictive features as:

1. Cabin class
2. Flight duration
3. Source city
4. Airline
5. Destination city
6. Days before departure

These values represent predictive importance rather than causal effects.

## Tools

* Python
* pandas
* NumPy
* scikit-learn
* Matplotlib
* seaborn
* Tableau

## Project Files

* [`Airfare_Pricing_Analysis.ipynb`](Airfare_Pricing_Analysis.ipynb) — data preparation, exploratory analysis, modeling, validation, and feature importance
* [Interactive Tableau Dashboard](https://public.tableau.com/app/profile/.78083245/viz/AirfarePricingandPurchase-TimingAnalysis/AirfarePricingDashboard?publish=yes)

## Limitations

The dataset is cross-sectional and does not contain actual travel dates or repeated historical price observations for the same itinerary. Therefore, the findings describe associations between booking windows and observed fares rather than real-time price movements or causal effects.

