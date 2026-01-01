# SALES PREDICTION BY SPARSE IDENTIFICATION OF AN ERP SYSTEM FROM POS MODULE DATA

**Author:** CESAR DANIEL RINCÓN BRITO

## OBJECTIVE

Adapt a sparse nonlinear system dynamics identification method to predict the sales behavior of the POS module of an ERP system.

## INTRODUCTION

The SINDy (Sparse Identification of Nonlinear Dynamics) methodology has become a powerful tool for discovering dynamic equations from data, even in contexts where systems exhibit complex and nonlinear behaviors. In the case of discrete stochastic models, SINDy allows identifying relationships between sales variables or other processes that evolve over time under uncertainty, selecting only the most relevant terms of the model. This complexity reduction capability is key to avoiding overfitting and capturing the essence of the underlying dynamics. Furthermore, its flexibility makes it applicable in scenarios where inputs are noisy or affected by random external factors. In summary, SINDy brings interpretability and efficiency to the modeling of discrete stochastic dynamic systems, facilitating the generation of predictions and the analysis of the real dynamics of the data.

In SINDy, the dynamic system is considered a map. Instead of predicting derivatives, the right-hand side functions advance the system by one time step.

![SINDy Prediction](Docs/Images/eq001.png)

The notation xk+1 indicates that the system evolves in discrete time steps. That is, states are observed at k=0,1,2,… instead of a continuous time. In the case of sales, this corresponds to daily, weekly, or monthly records, where each observation depends on the previous one.

In the context of discrete sales, it represents a nonlinear stochastic dynamic map, capable of capturing complex patterns and generating forecasts considering both past dependencies and random fluctuations. It is useful when the data are discrete observations (daily sales, time series, sensors with sampling, etc.) rather than continuous measurements.

[SINDy Introduction](readme.sindy.en.md)

## Project Description

This project uses the SINDy (Sparse Identification of Nonlinear Dynamics) method to model and predict sales. The process includes:

1.  **Data loading and pre-processing**: Sales data is read from a CSV file and relevant features are scaled.
2.  **Data splitting**: The data is divided into training, validation, and test sets.
3.  **SINDy Model**: A discrete-time SINDy model is defined and trained.
4.  **Simulation and Evaluation**: The trained model is used to simulate sales, and the results are compared with the actual data to evaluate the model's performance using metrics such as R².

## Control Variables

| Variable | Data set | Column | Records |
|---|---|---|---|
| Sales (Pred) | y_trainDatos | Total net sales | 1093 |
| Categorical Peak | r_trainDatos | Peak B_M_A | 1093 |
| Units | x_trainDatos | Kit Units | 1093 |
| CO Sales | n_trainDatos | Center-East Sales | 1093 |
| OC Sales | k_trainDatos | West Sales | 1093 |
| NT Sales | l_trainDatos | North Sales | 1093 |
| High Peak | u_trainDatos | Peak 1/0 | 1093 |

Table 1. Control variables.


## Control Variables

1. \(u_{1r,k}\): Categorical Peak (High - Medium - Low) Promotions, Special Dates.
2. \(u_{2x,k}\): Kit Units
3. \(u_{3n,k}\): Center-East Sales
4. \(u_{4k,k}\): West Sales
5. \(u_{5l,k}\): North Sales
6. \(u_{6u,k}\): Peak 1/0

![SINDy Prediction](Docs/Images/eq002.png)

The model indicates that future sales depend not only on the current level of sales but also on multiple external factors that change over time.


## SINDy

![SINDy Prediction](Docs/Images/SINDy_params.png)

*** STLSQ *** (Sequentially Thresholded Least Squares).

Calculates a linear regression to approximate the dynamics with a threshold (threshold=0.05), the smallest (less influential) coefficients are eliminated, leaving only the most important terms.

![SINDy Prediction](Docs/Images/eq003.png)

from eq. 3 SINDy looks for a sparsity model

*** feature library *** ps.PolynomialLibrary(degree=2)
Constructs the matrix Θ(X,U), which contains all candidate functions (polynomial terms up to degree 2).

![SINDy Prediction](Docs/Images/eq004.png)

where SINDy seeks the combination of these functions that best represents the dynamics

*** Discrete time *** (discrete_time=True)

Indicates that the system is not in continuous form (derivatives), but in discrete steps. Instead of approximating x˙(t) (derivatives), it directly adjusts the step-by-step evolution.

## Data

![Predicción de SINDy](Docs/Images/data.png)

The graph illustrates how the model will be trained on 90% of the data and then evaluated on the remaining 10%. The six trajectories represent the behavior of the key control variables, showing whether the model can track their trends and variations outside the training set.

## Model

![SINDy Prediction](Docs/Images/model.png)


## Coefficients

![SINDy Prediction](Docs/Images/FilterCoefSindy.png)

*** Most relevant linear terms ***

nVtaCO = +0.30 → Sales in the Center-West region have a direct positive effect on the dependent variable "Sales".

The other linear terms (rPicoBMA, xUnd, kVtaOCC, lVtaNT, uPicoBin) appear with very low or near-zero coefficients → little direct influence.

*** Strong interactions with Sales ***

Sales * xUnd = +0.72 → Past sales multiplied by "units sold" are a strong positive predictor: when both grow together, future sales grow.

Sales * nVtaCO = -0.82 → Strong but negative relationship: a simultaneous increase in sales and sales in the Center-West.

Sales * uPicoBin = -0.14 → Weakly negative relationship.

*** Interactions between control variables ***

rPicoBMA * xUnd = +0.23 → If the BMA peak increases and units sold also rise, it provides a positive boost.

rPicoBMA * nVtaCO = -0.52 → Strong negative interaction.

xUnd * nVtaCO = -0.53 → Also strong and negative.

nVtaCO * lVtaNT = +0.30 and kVtaOCC * ^2 = +0.27 → These appear as positive nonlinear effects.

*** Quadratic terms ***

nVtaNT^2 = -0.15 → Large increases in sales in the North have a decreasing effect.

kVtaOCC^2 = +0.27 → Accelerated growth: sales in the West behave in a non-linear expansive manner.

## Prediction Graph

![SINDy Prediction](Docs/Images/sindy_prediction.png)

*Note: To generate the graph, run the notebook `SINDY-discrete-stocastic-sales/Sindy-discrete-stocastic-sales-erp.ipynb`.*

## Libraries Used

-   pandas
-   numpy
-   scikit-learn
-   matplotlib
-   pysindy

## Repository Files

-   `SINDY-discrete-stocastic-sales/Sindy-discrete-stocastic-sales-erp.ipynb`: Jupyter notebook with the model implementation.
-   `ERP-POS-Data/Sales-CSV970-1093.csv`: Sales data used for training and validation.
-   `Matrix-Correlation/correlation_matrix.ipynb`: Notebook to analyze variable correlation.
-   `Test-Model/test_model.ipynb`: Notebook for additional model testing.

---

## Model v2: Training, Validation and Test

### Dataset: Sales-CSV-1201-Train-Value-Test.csv

Dataset split with **1201 records** for training, validation and independent testing:

| Set | Records | Indices | Period |
|-----|---------|---------|--------|
| **Train** | 976 | 0-975 | 2022-01-02 to 2024-09-06 |
| **Validation** | 109 | 976-1084 | 2024-09-07 to 2024-12-24 |
| **Test** | 109 | 1092-1200 | 2025-09-07 to 2025-12-24 |

Table 2. Dataset split for training, validation and testing.

### Control Variables v2

| Variable | Dataset | Column | Description |
|----------|---------|--------|-------------|
| y (Prediction) | TotalVentaNeta | Total net sales | Target variable to predict |
| r | Pico_B_M_A | Categorical peak | High - Medium - Low (0.33, 0.66, 1.0) |
| x | UnidadesKit | Units sold | Number of kits sold |
| n | VentasCENTROORIENTE | CO region sales | Center-East sales |
| k | VentasOCCIDENTE | OC region sales | West sales |
| l | VentasNORTE | NT region sales | North sales |
| u | EsFechaEspecial | Special date | Binary indicator (0/1) |

Table 3. Control variables for model v2.

### SINDy Model Equation v2

The SINDy model identified the following discrete dynamic equation with **34 active terms** (threshold = 0.01, alpha = 0.05):

```
y[k+1] = 0.006
       + 0.529·y[k]
       - 0.199·r[k] + 0.674·x[k] - 0.071·n[k] - 0.107·k[k] + 0.036·l[k] - 0.009·u[k]
       + 1.103·y[k]²
       - 0.482·y[k]·r[k] - 0.198·y[k]·x[k] - 2.742·y[k]·k[k] - 0.221·y[k]·l[k] - 0.028·y[k]·u[k]
       + 0.294·r[k]² - 0.259·r[k]·x[k] + 0.397·r[k]·n[k] + 0.992·r[k]·k[k] + 0.121·r[k]·l[k]
       - 1.728·x[k]² + 0.217·x[k]·n[k] + 0.815·x[k]·k[k] + 0.261·x[k]·l[k] - 0.117·x[k]·u[k]
       + 0.735·n[k]² - 1.260·n[k]·k[k] + 0.107·n[k]·u[k]
       + 0.664·k[k]² + 0.330·k[k]·l[k] + 0.086·k[k]·u[k]
       - 0.076·l[k]²
```

### Performance Metrics

| Set | Records | R² |
|-----|---------|------|
| **Validation** | 109 | 0.7006 |
| **Test (2025)** | 109 | 0.6691 |

Table 4. Performance metrics for model v2.

**Pearson Correlation:**
- Validation: r = 0.8370 (p-value < 0.001)
- Test: r = 0.8564 (p-value < 0.001)

### Graphical Results

#### Validation and Test Comparison

![Val-Test Results](Docs/Images/v2/resultados_train_test_val.png)

*Figure 1. Time series and scatter plots for validation (R²=0.7006) and test (R²=0.6691) sets.*

#### Prediction vs Real in Test (2025)

![Test Prediction vs Real](Docs/Images/v2/test_prediction_vs_real-0_6691.png)

*Figure 2. Detailed comparison between real values and SINDy model predictions for the test set (2025 period). Left: time series. Right: scatter plot with correlation r=0.8564.*

### Conclusions

1. **Generalization Capability**: The SINDy model demonstrates good generalization capability, maintaining an R² of 0.6691 on the test set (2025 data) that was not seen during training.

2. **Significant Correlation**: Pearson correlations above 0.81 in validation and test indicate a strong and statistically significant relationship between predictions and actual values.

3. **Interpretability**: Unlike black-box models, SINDy provides an explicit equation that allows understanding how control variables influence future sales.

4. **Nonlinear Interactions**: The model captures quadratic and cross-interactions between variables (such as y·r, y·x, r·n), revealing complex relationships in sales dynamics.

5. **Practical Applicability**: With a MAPE close to 48% on test, the model is useful for identifying trends and patterns, although point predictions have room for improvement due to the stochastic nature of sales.

*Note: To reproduce these results, run the notebook `SINDY-discrete-stocastic-sales/Sindy-discrete-stocastic-sales-erp(Train-Test-Val).ipynb` with the dataset `ERP-POS-Data/Sales-CSV-1201-Train-Value-Test.csv`.*

---

## Validation with Synthetic Data

To validate that the SINDy model truly captures the system dynamics and does not simply memorize patterns, tests were performed with synthetic datasets.

### Test 1: Random Synthetic Data (Without Temporal Correlations)

A synthetic dataset was generated with **similar statistical distributions** to the original test but **without preserving temporal correlations** (completely random data).

#### Results

| Metric | Original Test | Random Synthetic Test |
|--------|---------------|----------------------|
| **R²** | 0.6289 | **-1.1913** |
| **RMSE** | 0.0792 | 0.1784 |
| **Correlation** | 0.8011 | -0.0508 |

Table 5. Comparison between original and random synthetic test.

![Original Test vs Random Synthetic](Docs/Images/v2/test_original_vs_sintetico.png)

*Figure 3. Comparison between original test (top) and random synthetic test (bottom). The model fails completely with random data (negative R²).*

#### Interpretation

The **negative R² (-1.19)** in the random synthetic test is an **expected and positive** result for model validation:

1. **Synthetic data is random**: It has no temporal structure or correlations between variables that exist in real sales data.

2. **SINDy captures real dynamic patterns**: The model learned specific relationships such as:
   - Temporal dependency between consecutive sales (y[k] → y[k+1])
   - Correlations between units sold and total sales
   - Effects of regional sales

3. **No patterns = prediction worse than the mean**: A negative R² means that simply predicting the mean would be better than using the model. This **confirms that the model does not memorize** but learns the **real business dynamics**.

---

### Test 2: Synthetic Data with Preserved Temporal Correlations

A second synthetic dataset was generated by applying **controlled perturbations** to the original test:
- AR(1) correlated noise to continuous variables (preserves autocorrelation)
- 15% random changes to categorical variables
- Values maintained in range [0, 1]

#### Autocorrelation Comparison (lag-1)

| Variable | Original Test | Synthetic Test |
|----------|---------------|----------------|
| TotalVentaNeta | 0.7436 | 0.7547 |
| UnidadesKit | 0.6320 | 0.6208 |
| VentasCENTROORIENTE | 0.6922 | 0.6582 |
| VentasOCCIDENTE | 0.5133 | 0.5351 |
| VentasNORTE | 0.3278 | 0.3514 |

Table 6. Autocorrelations preserved in the synthetic dataset.

#### Results

| Metric | Original Test | Preserved Synthetic Test | Difference |
|--------|---------------|--------------------------|------------|
| **R²** | 0.6289 | 0.6211 | **0.0078** |
| **RMSE** | 0.0792 | 0.0834 | 0.0042 |
| **Correlation** | 0.8011 | 0.8022 | 0.0011 |

Table 7. Comparison between original and synthetic test with preserved correlations.

![Original Test vs Preserved Synthetic](Docs/Images/v2/test_original_vs_sintetico_final.png)

*Figure 4. Comparison between original test (top) and synthetic test with preserved temporal correlations (bottom). The model maintains similar performance (R² difference < 1%).*

#### Interpretation

1. **The model generalizes correctly**: With synthetic data that preserves temporal structure, the R² is practically the same (difference of only **0.78%**).

2. **Model validation**: This confirms that SINDy captures the **real dynamics** of sales and is **not overfitted** to the training data.

3. **Robustness**: The model is robust to small perturbations in the data, maintaining its predictive capability.

### Synthetic Validation Conclusions

| Scenario | R² | Conclusion |
|----------|-----|------------|
| Original Test | 0.6289 | Performance baseline |
| Random Synthetic | -1.1913 | Model **does not memorize**, captures real dynamics |
| Preserved Synthetic | 0.6211 | Model **generalizes well** to similar data |

Table 8. Summary of synthetic data validation.

This validation demonstrates that the SINDy model:
- ✅ **Works correctly** with data that follows real sales patterns
- ✅ **Does not work** with purely random data (expected behavior)
- ✅ **Is not overfitted** - generalizes to new data with similar structure

*Synthetic dataset available at: `ERP-POS-Data/Synthetic-Test-Perturbed.csv`*