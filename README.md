# BIT 2118: Scientific Computing — Task 20: Random Numbers and Monte Carlo Methods

## Overview
This repository contains the implementation, analysis, and real-world adaptation for **Task 20 (Random Numbers and Monte Carlo Methods)** of the **BIT 2118: Scientific Computing** course. 

Monte Carlo methods approximate probabilities and numerical outcomes by running a large number of repeated random simulations. In this project, we apply Monte Carlo sampling to two uncertainty modeling problems:
1. **Baseline Simulation (`Task_20_monte_carlo.py`):** Estimating the probability of network server overload under fluctuating traffic demand.
2. **Modified Real-World Case (`Task_20_project_budget_mc.py`):** Estimating the financial risk of a software/construction project exceeding its approved budget due to uncertain phase costs.

---

## Repository Structure
```text
├── code/
│   ├── Task_20_monte_carlo.py          # Original server-overload risk simulation
│   └── Task_20_project_budget_mc.py    # Modified real-world project budget overrun simulation
├── results/
│   ├── plot_server_overload.png        # Convergence plot for baseline simulation
│   └── plot_budget_overrun.png         # Convergence plot for modified real-world case
└── README.md                           # Project documentation and analysis
```

---

## Prerequisites & Setup

### Dependencies
Ensure you have Python 3.8+ installed along with `numpy` and `matplotlib`:
```bash
pip install numpy matplotlib
```

### Running the Scripts
Execute either script from the root directory:
```bash
python code/Task_20_monte_carlo.py
python code/Task_20_project_budget_mc.py
```

---

## Part 1: Baseline Simulation — Server Overload Risk (`Task_20_monte_carlo.py`)

### 1. Input Values & Mathematical Operations
* **Inputs:**
  * `seed = 42` (Ensures reproducible pseudo-random number generation via `np.random.default_rng`)
  * `capacity = 1000.0` (Maximum server capacity threshold)
  * `mean_demand` ($\mu$) = `850.0` (Expected average traffic demand)
  * `std_demand` ($\sigma$) = `120.0` (Standard deviation representing traffic variability)
  * `N = 100,000` (Total simulated scenarios)
* **Mathematical Operations:**
  1. **Normal Sampling:** Generates $N$ random demand realizations $X_i \sim \mathcal{N}(\mu, \sigma^2)$.
  2. **Threshold Evaluation:** Applies an indicator function $I_i = 1$ if $X_i > 1000.0$, else $0$.
  3. **Probability Estimation:** Calculates the sample mean $\hat{p} = \frac{1}{N}\sum_{i=1}^{N} I_i$.
  4. **Running Convergence:** Uses `np.cumsum()` to compute $\hat{p}_k = \frac{1}{k}\sum_{i=1}^{k} I_i$ across logarithmic intervals.

### 2. Parameter Sensitivity Analysis
* **Baseline Result ($\sigma = 120.0$):** Estimated overload probability is **~10.56%** (~10,565 overload events out of 100,000).
* **Modified Parameter ($\sigma = 200.0$):** Increasing traffic volatility while keeping $\mu = 850.0$ and $\text{capacity} = 1000.0$ causes the overload probability to jump to **~22.66%** (~22,660 events).
* **Explanation:** A higher standard deviation widens the normal distribution curve, pushing a significantly larger proportion of the right tail past the `1000.0` capacity limit even though average demand remains unchanged.

### 3. Output & Graph Interpretation
* **Output Meaning:** Under normal operating parameters, there is roughly a **10.6% probability** (about 1 in every 9.5 intervals) that incoming traffic will exceed server capacity.
* **Graph Interpretation:** The `semilogx` convergence plot shows noisy, erratic estimates when $N < 100$ due to small sample variance. Beyond $N = 10,000$, the curve stabilizes around `0.1056`, demonstrating the **Law of Large Numbers**.

---

## Part 2: Modified Real-World Case — Project Budget Overrun Risk (`Task_20_project_budget_mc.py`)

### 1. Problem Statement & New Assumptions
Project managers often sum expected phase costs to estimate budgets, overlooking the compounding effect of uncertainty across multiple deliverables. This modified program estimates the probability that a project exceeds its **KES 5,000,000** approved budget when three independent cost phases vary randomly:

| Cost Component | Distribution | Mean ($\mu$) | Std. Deviation ($\sigma$) |
| :--- | :--- | :--- | :--- |
| **Development & Labor** | Normal | KES 2,800,000 | KES 350,000 |
| **Infrastructure & Materials** | Normal | KES 1,300,000 | KES 200,000 |
| **Compliance & Contingency** | Normal | KES 500,000 | KES 100,000 |

* **Assumptions:**
  * Cost overruns/underruns in each phase follow independent normal distributions.
  * Total project cost per simulation is $C_{\text{total}} = C_{\text{labor}} + C_{\text{infra}} + C_{\text{compliance}}$.
  * $N = 100,000$ independent project executions are simulated with `seed = 101`.

### 2. Simulation Output
```text
Expected Total Cost: KES 4,600,142.50
Approved Budget:     KES 5,000,000.00
Probability of Budget Overrun: 16.65%
Overrun Scenarios:   16,650 out of 100,000
95th Percentile Cost (Safe Budget Target): KES 5,283,110.00
```

### 3. Interpretation & Decision Insight
* **Risk Assessment:** Although the sum of the average phase costs (**KES 4.60M**) sits **KES 400,000** below the approved **KES 5.00M** budget, combined variances create a **16.65% probability** of a budget overrun.
* **Actionable Recommendation:** To maintain at least 95% confidence of staying within budget (keeping overrun risk under 5%), leadership should approve a total budget reserve of **KES 5.28M** (the 95th percentile simulated cost).
