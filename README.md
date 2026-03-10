# Natural Gas Futures Pricing with Seasonal Convenience Yield Models

## Project Overview

This project studies the **pricing dynamics of European natural gas futures** traded on the **TTF (Title Transfer Facility) market**, focusing on the role of **convenience yield and seasonality** in commodity pricing.

The research compares two stochastic models used in energy markets:

* **Gibson–Schwartz (1990) model**
* **Seasonal Convenience Yield (sCY) model**

The goal is to evaluate which model better captures the **structure of forward curves and seasonal fluctuations** in natural gas prices.

The analysis is conducted using **spot and futures prices observed in 2024**, and models are calibrated through **RMSE minimization** to match market forward curves. 

---

# Motivation

Between **2021 and 2022**, natural gas prices in the European TTF market exceeded **€300/MWh**, highlighting the need for models capable of capturing the unique dynamics of gas markets.

Unlike oil or metals, natural gas exhibits strong **seasonal demand patterns** driven by:

* winter heating demand
* gas-to-power electricity production
* storage constraints
* geopolitical supply shocks

These features make standard commodity pricing models insufficient, requiring models that incorporate **seasonality in the convenience yield**. 

---

# Dataset

The empirical analysis uses **European natural gas prices from the TTF market**.

### Data used

* **Monthly spot prices** ( S_t )
* **Futures contracts with maturities up to 24 months**

All prices are expressed in:

```
€/MWh
```

For each observation date ( t ):

* futures prices define a **forward curve**
* the set of all curves forms the **forward surface**

The data also include **risk-free interest rates estimated using the Svensson yield curve model** based on AAA-rated European government bonds. 

---

# Convenience Yield

The **convenience yield** represents the economic benefit of physically holding the commodity rather than holding a futures contract.

It captures advantages such as:

* supply security
* inventory flexibility
* ability to meet sudden demand

The convenience yield is extracted from futures prices using:

[
F(t,T) = S_t \mathbb{E}^Q \left[\exp\left(\int_t^T (r(s) - \delta(s)) ds\right)\right]
]

where:

* (F(t,T)) = futures price
* (S_t) = spot price
* (r(t)) = risk-free rate
* ( \delta(t) ) = convenience yield. 

---

# Models Implemented

## 1. Gibson–Schwartz Model

The **Gibson–Schwartz (1990)** model jointly models:

* the **spot price**
* the **convenience yield**

The dynamics are defined by two stochastic processes:

### Spot price

Geometric Brownian Motion:

[
\frac{dS_t}{S_t} = \mu dt + \sigma_s dW_s
]

### Convenience yield

Mean-reverting Ornstein–Uhlenbeck process:

[
d\delta_t = \kappa (\theta - \delta_t) dt + \sigma_x dW_x
]

This model captures:

* stochastic convenience yield
* correlation between spot prices and yield. 

---

## 2. Seasonal Convenience Yield Model (sCY)

To account for **seasonal patterns**, the model extends Gibson–Schwartz by decomposing the convenience yield into:

[
\delta_t = g(t) + x_t
]

where:

* ( g(t) = a \cos(bt + c) ) captures deterministic **seasonality**
* ( x_t ) is a stochastic mean-reverting process.

This structure allows the model to capture **annual demand cycles in energy markets**. 

---

# Model Calibration

Both models are calibrated using **futures prices observed in the market**.

Parameters are estimated by minimizing the **Root Mean Squared Error (RMSE)**:

[
\hat{\Theta} = \arg\min_{\Theta}
\frac{1}{24} \sum_{i=1}^{24}
\left(F^{market} - F^{model}\right)^2
]

Calibration parameters include:

### Gibson–Schwartz

* mean reversion ( \kappa )
* long-term mean ( \theta )
* spot volatility ( \sigma_s )
* convenience yield volatility ( \sigma_x )
* correlation ( \rho )

### Seasonal model

* all GS parameters
* seasonal amplitude ( a )
* frequency ( b )
* phase shift ( c ). 

---

# Results

The **Seasonal Convenience Yield model outperforms the Gibson–Schwartz model**.

### Average RMSE

| Model           | Average RMSE |
| --------------- | ------------ |
| Gibson–Schwartz | ~1.63        |
| Seasonal CY     | ~0.84        |

The seasonal model significantly improves the fit because it captures **periodic fluctuations in natural gas demand**. 

---

# Key Findings

Main empirical conclusions:

* Natural gas futures exhibit **strong seasonal dynamics**.
* The **convenience yield is highly volatile** and influenced by geopolitical factors.
* Seasonal components improve the ability to reproduce **forward curve shapes**.
* The **Seasonal Convenience Yield model better matches observed market prices**.

---

# Technologies Used

* Python
* NumPy
* SciPy
* Pandas
* Matplotlib
* Optimization algorithms for model calibration

---

# Applications

These models are relevant for:

* **energy trading**
* **commodity derivatives pricing**
* **risk management**
* **energy market forecasting**
* **quantitative energy finance**

---

# References

Key academic references include:

* Gibson & Schwartz (1990) – Stochastic convenience yield
* Schwartz (1997) – Commodity price dynamics
* Schwartz & Smith (2000) – Short-term and long-term commodity factors
* Rotondi (2024) – Seasonal convenience yield model for natural gas futures. 
