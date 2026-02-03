# CompFin — Quantitative Finance Python Library 📈

**CompFin** is an open-source Python library for **quantitative finance** and **option pricing**, built with a focus on clean code, correctness, and educational clarity.

✅ The **v0.1** release focuses on a complete and production-quality implementation of the **Black–Scholes–Merton (BSM)** model for European options, including:

- European **Call/Put pricing**
- **Greeks** (1st, 2nd, and 3rd order)
- **Put–Call parity** validation
- **No-arbitrage bounds** checks
- **Arbitrage opportunity detection** with suggested trading strategies
- **Plotting tools** (price, greeks, bounds)
---
## Table of Contents
- [Installation](#installation)
- [Quickstart](#quickstart)
- [Black–Scholes–Merton Model](#blackscholesmerton-model)
- [Pricing](#pricing)
- [Greeks](#greeks)
- [Put–Call Parity](#putcall-parity)
- [No-Arbitrage Bounds & Arbitrage Detection](#noarbitrage-bounds--arbitrage-detection)
- [Plotting](#plotting)
- [Conventions](#conventions)
- [Roadmap](#roadmap)
- [License](#license)

---

# Installation

## Local development import
During development you can use a simple Python package layout:

```

project/
├── compfin/
│   ├── **init**.py
│   └── options/
│       ├── **init**.py
│       └── black_scholes.py
└── notebook.ipynb / main.py

````

Then import:

```python
from compfin.options.black_scholes import BlackScholesPricing, BlackScholesPlotter
````

## Editable install (recommended)

Once your repository contains a `pyproject.toml`, you can install it with:

```bash
pip install -e .
```

---

# Quickstart

```python
from compfin.options.black_scholes import BlackScholesPricing, BlackScholesPlotter

# Parameters
S, K, TTM = 100, 100, 0.5
rf, q, vol = 0.05, 0.03, 0.20

# Price
C, P = BlackScholesPricing.price(S=S, K=K, TTM=TTM, rf=rf, q=q, vol=vol)
print("Call:", C)
print("Put :", P)

# Greeks
greeks = BlackScholesPricing.greeks_first_order(S=S, K=K, TTM=TTM, rf=rf, q=q, vol=vol)
print("Delta(call):", greeks["delta_call"])
print("Gamma:", greeks["gamma"])
print("Vega :", greeks["vega"])

# Bounds & arbitrage report
report = BlackScholesPricing.Bounds(rf=rf, TTM=TTM, S=S, C=C, P=P, K=K, q=q)
print(report["call_status"], report["call_trade"])
print(report["put_status"], report["put_trade"])

# Plotting
plotter = BlackScholesPlotter(BlackScholesPricing)
plotter.plot_greeks_vs_spot(S_min=50, S_max=150, K=K, TTM=TTM, rf=rf, q=q, vol=vol, which="all")
plotter.plot_bounds(S_min=50, S_max=150, K=K, TTM=TTM, rf=rf, q=q, C_mkt=C, P_mkt=P, S_mkt=S)
```

---

# Black–Scholes–Merton Model

CompFin implements the **Black–Scholes–Merton (BSM)** model with continuous dividend yield:

[
dS_t = (r-q)S_t , dt + \sigma S_t, dW_t
]

Where:

* (S) is the spot price
* (r) is the continuously compounded risk-free rate
* (q) is the continuous dividend yield
* (\sigma) is volatility

---

# Pricing

### Call and Put pricing

BSM pricing formulas:

[
C = S e^{-qT}N(d_1) - K e^{-rT}N(d_2)
]
[
P = K e^{-rT}N(-d_2) - S e^{-qT}N(-d_1)
]

Python API:

```python
C, P = BlackScholesPricing.price(S, K, TTM, rf, q, vol)
```

---

# Greeks

CompFin provides Greeks up to **third order**, useful for risk management and volatility trading.

## 1st order Greeks

* **Delta**
* **Gamma**
* **Vega**
* **Theta**
* **Rho**

```python
greeks = BlackScholesPricing.greeks_first_order(S, K, TTM, rf, q, vol)
```

## 2nd order Greeks

* **Vanna**
* **Vomma**
* **Charm** (time decay of Delta)

```python
Vanna, Vomma, Charm = BlackScholesPricing.Greeks_second_order(S, vol, K, TTM, rf, q)
```

## 3rd order Greeks

* **Speed**
* **Zomma**
* **Ultima**

```python
Speed, Zomma, Ultima = BlackScholesPricing.Greeks_third_order(S, vol, K, TTM, rf, q)
```

---

# Put–Call Parity

Put–Call parity relation:

[
C - P = S e^{-qT} - K e^{-rT}
]

API:

```python
parity_msg = BlackScholesPricing.Put_Call_parity(C, P, S, q, TTM, K, rf)
```

---

# No-Arbitrage Bounds & Arbitrage Detection

European no-arbitrage bounds:

### Call bounds

[
\max(0, S e^{-qT} - K e^{-rT}) \le C \le S e^{-qT}
]

### Put bounds

[
\max(0, K e^{-rT} - S e^{-qT}) \le P \le K e^{-rT}
]

CompFin checks bounds and returns:

* bounds (LB/UB)
* arbitrage status
* suggested trade structure

```python
report = BlackScholesPricing.Bounds(rf, TTM, S, C, P, K, q)
print(report["call_status"], report["call_trade"])
print(report["put_status"], report["put_trade"])
```

---

# Plotting

The library includes plotting tools for:

* Option price vs spot
* Greeks vs spot
* No-arbitrage bounds regions

Initialize the plotter:

```python
plotter = BlackScholesPlotter(BlackScholesPricing)
```

## Price vs Spot

```python
plotter.plot_price_vs_spot(S_min=50, S_max=150, K=100, TTM=0.5, rf=0.05, q=0.03, vol=0.2)
```

## Greeks vs Spot

```python
plotter.plot_greeks_vs_spot(S_min=50, S_max=150, K=100, TTM=0.5, rf=0.05, q=0.03, vol=0.2, which="all")
```

## Bounds plot

```python
plotter.plot_bounds(S_min=50, S_max=150, K=100, TTM=0.5, rf=0.05, q=0.03)
```

---

# Conventions

* `TTM` is time to maturity in **years**
* `vol` is volatility in **decimal form** (`0.20 = 20%`)
* `rf` and `q` are continuously compounded rates
* Vega is returned per **1.0 volatility unit** (divide by 100 for 1%)

---

# Roadmap

✅ **v0.1**

* Full BSM pricing + Greeks up to 3rd order
* Bounds + arbitrage detection
* Plotting utilities

🔜 Future versions may include:

* Black-76 (forward/futures options)
* Implied volatility solver
* Binomial trees (American options)
* Monte Carlo pricing framework
* Exotic options (Asian, barrier)
* Stochastic volatility (Heston), SABR, SVI volatility surface tools

---
```
