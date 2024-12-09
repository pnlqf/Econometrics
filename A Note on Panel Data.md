# A Note on Panel Data  

## Panels  

We say that a dataset has a panel-data structure when we are following a specific cross-sectional unit, \(N\), over a specific time period, \(T\). This naturally results in our covariate matrix having dimensions \(N \times T\) and a model of the following format:  

---

## Pooled OLS (POLS)  

The simplest form of an estimator in a case like this is the pooled OLS estimator, which has the well-known form:  

$$
\hat{\beta} = (X'X)^{-1}X'y
$$  

For this estimator to be consistent, we need it to satisfy two distinct properties:  

1. **Contemporaneous Exogeneity:**  

$$
E(u_{it} | X_{it}) = 0
$$  

2. **Regular Rank Condition:**  

The matrix \(X'X\) must be invertible.  

Note that the contemporaneous exogeneity condition does not restrict the relationship between the error term and the regressors for other time periods than the contemporary one.  

For our estimates to be valid, we need two additional conditions:  

- Homoscedastic errors:  

$$
E(u_{it}^2 | X_{it}) = \sigma^2
$$  

- No serial correlation:  

$$
E(u_{it}u_{is} | X) = 0 \quad \text{for} \quad t \neq s
$$  

While one can argue for the validity of the consistency assumptions, it is difficult to justify that the error term should be homoskedastic and even harder to assume zero serial correlation when observing the same individuals through time.  

Thus, it is best practice to use standard errors that are robust to arbitrary serial correlation and heteroskedasticity:  

$$
\hat{V}(\hat{\beta}) = (X'X)^{-1} X' \Omega X (X'X)^{-1}
$$  

This is the correction that Stata applies with the option `cluster(id)`.  

---

## An Unobserved Effect Model  

The key assumption for POLS to produce consistent estimates is contemporaneous exogeneity. However, this assumption may not hold in a panel data setting, particularly when the true model has an unobserved effect structure:  

$$
y_{it} = X_{it}\beta + c_i + u_{it}
$$  

Where:  

- \(c_i\): Unobserved effect  
- \(u_{it}\): Idiosyncratic error term  

The unobserved effect is a time-invariant effect specific to the cross-sectional unit \(i\). In this setting, contemporaneous exogeneity is no longer sufficient. We need the stronger assumption of **Strict Exogeneity**:  

$$
E(u_{it} | X_{i1}, \ldots, X_{iT}) = 0
$$  

This implies:  

$$
E(u_{it} | X_{is}) = 0 \quad \text{for all } s \leq T
$$  

If this condition is not satisfied, we say that the pooled OLS estimator suffers from heterogeneity bias.  

Even if strict exogeneity holds, the presence of an unobserved effect induces serial correlation, necessitating robust standard errors. Additionally, the pooled OLS estimator is no longer efficient.  

---

## Random Effects (RE)  

To address the issue of serial correlation, we can use a GLS transformation, analogous to the Prais-Winston estimator for time-series data. This transformation requires the assumption:  

$$
E(c_i | X_{it}) = 0
$$  

The variance-covariance matrix for unit \(i\) is:  

$$
\Sigma_i = \sigma^2_u I_T + \sigma^2_c J_T
$$  

The Random Effects estimator is:  

$$
\hat{\beta}_{RE} = (X' \Sigma^{-1} X)^{-1} X' \Sigma^{-1} y
$$  

It is efficient among estimators assuming:  

$$
E(c_i | X_{it}) = 0
$$  

---

## Fixed Effects (FE)  

In many cases, the main benefit of panel data is that it allows for arbitrary correlation between regressors and the unobserved effect. The Fixed Effects model achieves this by transforming the data through de-meaning:  

$$
\tilde{y}_{it} = y_{it} - \bar{y}_i, \quad \tilde{X}_{it} = X_{it} - \bar{X}_i
$$  

This yields the Fixed Effects estimator:  

$$
\hat{\beta}_{FE} = (\tilde{X}' \tilde{X})^{-1} \tilde{X}' \tilde{y}
$$  

The transformation removes both observed and unobserved time-invariant regressors. This can be problematic if estimating the effect of time-invariant variables such as gender is important.  

---

## Dummy Variable (DV)  

Instead of removing the unobserved effect, we can include dummy variables for each cross-sectional unit:  

$$
y_{it} = X_{it}\beta + \sum \delta_i D_i + u_{it}
$$  

This approach is equivalent to the Fixed Effects model but allows time-invariant regressors. However, it lacks nice asymptotic properties and becomes computationally impractical with large datasets.  

---

## First Difference (FD)  

If the idiosyncratic error exhibits serial correlation, the First Difference estimator is a better choice. It transforms the data as follows:  

$$
\Delta y_{it} = y_{it} - y_{it-1}, \quad \Delta X_{it} = X_{it} - X_{it-1}
$$  

The resulting estimator is:  

$$
\hat{\beta}_{FD} = (\Delta X' \Delta X)^{-1} \Delta X' \Delta y
$$  

The transformation removes time-invariant variables and variables growing at a constant rate (e.g., calendar year).  

The estimator is efficient when the idiosyncratic error follows a random walk:  

$$
u_{it} = u_{it-1} + \epsilon_{it}
$$  

---

## Summary  

- **Pooled OLS (POLS):** Requires contemporaneous exogeneity, homoskedasticity, and no serial correlation. Use clustered standard errors when these assumptions fail.  
- **Random Effects (RE):** Efficient under strict exogeneity between regressors and the unobserved effect.  
- **Fixed Effects (FE):** Allows arbitrary correlation between regressors and the unobserved effect but removes time-invariant variables.  
- **Dummy Variable (DV):** Equivalent to Fixed Effects but allows time-invariant regressors.  
- **First Difference (FD):** Efficient if the idiosyncratic error follows a random walk.  

---
