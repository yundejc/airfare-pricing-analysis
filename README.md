# Airfare Pricing and Purchase-Timing Analysis
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yundejc/airfare-pricing-analysis/blob/main/Airfare_Pricing_Analysis.ipynb)
An end-to-end analytics project examining how booking timing, cabin class, airline, route, and itinerary characteristics are associated with Indian domestic airfare. The project combines exploratory analysis, machine learning, grouped validation, historical policy simulation, and interactive Tableau dashboards.

## Interactive Dashboards

* [Airfare Pricing Dashboard](https://public.tableau.com/app/profile/.78083245/viz/AirfarePricingandPurchase-TimingAnalysis/AirfarePricingDashboard?publish=yes)
* [Purchase-Timing Decision Dashboard](https://public.tableau.com/app/profile/.78083245/viz/AirfarePricingandPurchase-TimingAnalysis/PurchaseTimingDecisionDashboard?publish=yes)

## Business Problem

Airfare varies substantially across booking windows, routes, airlines, cabin classes, and itinerary structures. This project addresses three questions:

1. Which factors are most strongly associated with airfare?
2. Can a machine-learning model generalize to flight numbers not observed during training?
3. Could a model-guided booking-window strategy outperform random booking timing in a historical simulation?

## Dataset

The analysis uses the [Flight Price Prediction dataset by Shubham Bathwal](https://www.kaggle.com/datasets/shubhambathwal/flight-price-prediction).

* 300,153 airfare observations
* 6 airlines
* 1,561 flight numbers
* 30 directional routes
* Economy and business-class fares
* Booking windows ranging from 1 to 49 days before departure

The dataset includes airline, flight number, origin, destination, departure and arrival periods, number of stops, cabin class, duration, days remaining before departure, and fare.

## Data Preparation

The workflow:

* Removed the exported index column
* Checked missing values and duplicate observations
* Validated positive fare and duration values
* Verified that origins and destinations differed
* Created directional route labels
* Grouped `days_left` into six booking windows
* Built preprocessing and modeling steps within a scikit-learn pipeline

## Exploratory Findings

### Booking Timing

Economy fares showed substantially greater timing sensitivity than business fares.

* Economy: INR 11,634 when booked 1–7 days before departure versus INR 4,789 at 41–49 days
* Business: INR 56,745 when booked 1–7 days before departure versus INR 51,559 at 41–49 days
* The observed last-minute premium was 142.9% for economy and 10.1% for business

### Airline Differences

* Economy average fares ranged from INR 4,091 for AirAsia to INR 7,807 for Vistara
* Business observations were available only for Air India and Vistara
* Vistara had the higher average business fare

### Route Differences

* Economy route averages differed by approximately 38.7%
* Business route averages differed by approximately 34.2%
* Route-level comparisons were conducted separately by cabin class to avoid mixing structurally different fare segments

### Stops and Duration

Higher-stop itineraries were associated with higher average fares, but they also had substantially longer durations and different route compositions. This relationship is therefore interpreted as an association rather than a causal stop premium.

## Predictive Model

A Random Forest regression pipeline was trained using:

* Airline
* Origin and destination
* Departure and arrival periods
* Number of stops
* Cabin class
* Flight duration
* Days remaining before departure

Flight number was excluded as a predictor to reduce high-cardinality memorization risk.

### Random-Split Performance

| Model                 |       MAE |      RMSE |     R² |
| --------------------- | --------: | --------: | -----: |
| Class-median baseline | INR 4,824 | INR 7,950 | 0.8776 |
| Random Forest         | INR 1,297 | INR 2,792 | 0.9849 |

The Random Forest reduced MAE by 73.1% and RMSE by 64.9% relative to the class-median baseline.

### Grouped Holdout Performance

To evaluate generalization more realistically, flight numbers were separated between training and testing.

* Training set: 245,189 observations and 1,248 flight numbers
* Test set: 54,964 observations and 313 unseen flight numbers
* Flight-number overlap: 0

|       MAE |      RMSE |     R² |
| --------: | --------: | -----: |
| INR 2,898 | INR 5,278 | 0.9449 |

The grouped result is used as the primary reported model performance because it measures prediction on flight numbers not observed during training.

## Historical Booking-Window Simulation

A historical policy simulation compared two strategies:

* **Model-guided policy:** Select the booking window with the lowest predicted average fare
* **Random-timing policy:** Select one of the six booking windows with equal probability

The strict evaluation retained 603 decision units with observations in all six booking windows. Random booking timing was simulated 5,000 times.

| Segment  | Random-Timing Fare | Model-Guided Fare | Average Savings | Savings Rate | 95% Simulation Interval |
| -------- | -----------------: | ----------------: | --------------: | -----------: | ----------------------: |
| Overall  |         INR 17,042 |        INR 15,301 |       INR 1,740 |       10.21% |            9.14%–11.30% |
| Business |         INR 46,571 |        INR 45,959 |         INR 612 |        1.31% |             0.67%–2.03% |
| Economy  |          INR 6,654 |         INR 4,510 |       INR 2,144 |       32.20% |           29.53%–34.77% |

**Business conclusion:** In a historical grouped-holdout simulation, the model-guided booking-window strategy reduced average airfare by 10.21% relative to random booking timing.

## Historical Buy Now / Hold Signal

The Purchase-Timing Decision Dashboard includes a historical signal:

* **Buy Now:** The current predicted fare is within 5% of the lowest predicted fare available in the remaining booking windows
* **Hold:** A remaining booking window has a predicted fare that is more than 5% lower than the current prediction

Users can explore the signal by cabin class, route, flight number, and current booking window.

## Feature Importance

Permutation importance identified the strongest predictive features as:

1. Cabin class
2. Flight duration
3. Origin city
4. Airline
5. Destination city
6. Days remaining before departure

Feature importance is predictive rather than causal.

## Tools

* Python
* pandas
* NumPy
* scikit-learn
* Matplotlib
* seaborn
* Tableau Public
* Google Colab

## Repository Contents

* [`Airfare_Pricing_Analysis.ipynb`](Airfare_Pricing_Analysis.ipynb): Data preparation, exploratory analysis, predictive modeling, grouped validation, policy simulation, and signal generation
* `README.md`: Project overview and results
* `LICENSE`: MIT License

## Limitations

* The dataset is cross-sectional rather than a longitudinal record of the same ticket’s price over time
* The Buy Now / Hold output is a historical model simulation, not a live purchasing recommendation
* The policy comparison does not establish a causal effect of following the signal
* Results may not generalize to other countries, time periods, or airline markets
* Unobserved factors such as travel date, holidays, demand shocks, and fare restrictions may affect prices

