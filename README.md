# Bitcoin Trading Strategy Optimisation with SFLA

Optimising moving-average trading strategies using the **Shuffled Frog-Leaping Algorithm (SFLA)**, with comparisons against other nature-inspired optimisation algorithms.

This university group project explores whether optimised trading rules generalise to a later market period. It combines Python implementation, historical backtesting, algorithm benchmarking and repeated-seed experiments.

## My contribution

I worked on **SFLA** within a team of four. The implementation searches for moving-average parameters using local search within groups of candidate solutions, followed by shuffling to share information across the population.

The notebook also contains the group's implementations and comparisons of Particle Swarm Optimisation (PSO), Cuckoo Search (CS) and Slime Mould Optimisation (SMO). The full comparison is a shared team output.

## Project question

Can nature-inspired optimisation improve a Bitcoin moving-average strategy, and do strong training results translate into performance on unseen dates?

Two strategy configurations are compared:

- **SMA-only:** optimise the fast and slow simple moving-average windows.
- **Combined moving averages:** optimise weights and parameters for simple, linearly weighted and exponential moving averages.

Strategies buy when the fast indicator crosses above the slow indicator and sell when it crosses below. The backtest is long-only and moves between cash and Bitcoin.

## Data and experiment setup

Source: [Bitcoin historical data on Kaggle](https://www.kaggle.com/datasets/prasoonkottarathil/btcinusd), downloaded through `kagglehub`.

| Setting | Value |
| --- | --- |
| Dataset used | `BTC-Daily.csv` |
| Saved data snapshot | 2,651 daily observations, 28 November 2014–1 March 2022 |
| Training period | Through 31 December 2019; 1,860 observations |
| Test period | From 1 January 2020; 791 observations |
| Starting cash | USD 1,000 |
| Transaction fee | 3% per buy or sell, as configured for the assignment |
| Population size | 30 |
| Optimisation iterations | 100 |
| Main experiment seed | 42 |
| Repeated experiment seeds | 1, 7, 21, 42 and 99 |
| Baselines | Manual SMA 10/20 and buy-and-hold |

The optimiser maximises final cash on the training period. Parameters are then evaluated on the later test period. Reported metrics include final cash, return, maximum drawdown, market exposure, trade counts and fitness evaluation counts.

## How SFLA works

1. Generate a population of candidate parameter sets, called frogs.
2. Evaluate and rank them by training fitness.
3. Distribute them across five groups, called memeplexes.
4. Improve the weakest candidate in each group by moving it towards the local best.
5. If that fails, try the global best, then a random replacement.
6. Shuffle the groups and repeat.

The implementation performs five local search updates per memeplex per iteration and clips candidates to the permitted parameter bounds.

## Results

The table below reproduces **saved notebook outputs for the main seed-42 test experiment**, starting from USD 1,000. These are historical simulations, not live trading results.

| Strategy | Final cash (USD) | Return |
| --- | ---: | ---: |
| Manual SMA 10/20 | 1,311.27 | 31.1% |
| SFLA — SMA-only | 2,199.16 | 119.9% |
| SFLA — combined moving averages | 1,659.49 | 65.9% |
| CS — combined moving averages | 3,278.99 | 227.9% |
| Buy-and-hold | 5,660.26 | 466.0% |

**Main findings:**

- SFLA's SMA-only configuration outperformed the manual strategy and the other SMA-only optimisers in the main experiment.
- CS with combined moving averages achieved the highest test final cash among the eight optimised strategies in that experiment.
- **None of those eight strategies beat buy-and-hold.** Strong training fitness did not guarantee the strongest test performance.
- Across five seeds, SFLA SMA-only and PSO SMA-only had the same mean test final cash of **USD 2,176.60**, with a standard deviation of **USD 50.46**.
- SFLA required more fitness evaluations: approximately **6,947 on average** for SMA-only, compared with **3,000 for PSO**. The equal population and iteration settings did not create equal computational budgets.

## Tools and skills

Python, NumPy, pandas, Matplotlib, Jupyter, KaggleHub, time-series preparation, optimisation, backtesting, model evaluation and reproducible experiments.

## Run the notebook

Upload `CITS4404_Part_2_2.ipynb` alongside this README in the repository.

Install the dependencies:

```bash
python -m pip install numpy pandas matplotlib kagglehub jupyter
```

Start Jupyter:

```bash
jupyter notebook CITS4404_Part_2_2.ipynb
```

Run the cells in order. The data-loading cells require internet access to download the Kaggle dataset. The main comparison runs eight algorithm/configuration combinations; the repeated-seed experiment adds 40 optimisation runs and takes longer.

The notebook includes data preparation, moving-average functions, the backtesting engine, optimisation algorithms, convergence plots and results tables. The figures above come from its existing saved outputs; they were not regenerated for this README. Dependency versions are not pinned, and a changed dataset or environment may affect reproduction.

## Limitations and next steps

- The 3% transaction fee is an assignment assumption and should not be interpreted as a current exchange fee.
- Signals execute at the same price used to calculate them. A more realistic backtest would execute on a subsequent bar and model slippage.
- Reflective padding of the moving-average input can introduce look-ahead effects near the start of each sequence. A causal warm-up approach would strengthen the evaluation.
- Comparing and selecting configurations using the test results makes that comparison exploratory. A separate final holdout or walk-forward evaluation would provide stronger evidence of generalisation.
- Five seeds provide a limited view of variability. Future comparisons should use more seeds and matched fitness evaluation budgets.
- The study uses one asset and one historical train/test split. Its results do not establish future profitability.

## Context

Completed as a four-person group project for **CITS4404 at the University of Western Australia**. This portfolio repository highlights **Rohan Varghese Jacob's SFLA contribution** while retaining the wider experiment for context.

Research and educational use only; not a production trading system or investment recommendation.
