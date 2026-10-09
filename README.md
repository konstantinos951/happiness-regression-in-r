# Happiness Score Analysis: Linear Regression and Model Selection in R

An undergraduate group project exploring country-level happiness data through multiple linear regression, exploratory analysis, residual diagnostics and model selection.

The project compares how different selection criteria balance model fit and complexity, and uses five-fold cross-validation to evaluate prediction error.

## Project Objectives

- Explore relationships between happiness scores and socioeconomic indicators.
- Fit and interpret a multiple linear regression model.
- Examine residuals to assess potential departures from model assumptions.
- Compare best subset selection and forward selection.
- Evaluate model size using RSS, adjusted R², Mallows’ Cp and BIC.
- Use cross-validation to compare predictive performance.
- Calculate confidence and prediction intervals for a new observation.

## Dataset

The analysis reads `happiness_score.csv` and describes 156 country or region observations.

The response variable is `Score`, representing the happiness score. The numeric predictors used in the original analysis are:

| Variable | Description |
|---|---|
| Overall rank | Country ranking in the dataset |
| GDP per capita | GDP-related indicator |
| Social support | Social support indicator |
| Healthy life expectancy | Health and life expectancy indicator |
| Freedom to make life choices | Freedom of choice indicator |
| Generosity | Generosity indicator |
| Perceptions of corruption | Corruption perception indicator |

The country or region column is excluded when the code selects numeric variables.

Variable names are converted to R-compatible names when the CSV is imported, such as `Social.support` and `Overall.rank`.

## Methodology

### 1. Exploratory Data Analysis

The project uses scatterplot matrices and correlation visualisations to examine relationships between numeric variables.

The written analysis reports positive correlations between happiness score and:

- GDP per capita: approximately 0.79.
- Social support: approximately 0.78.
- Healthy life expectancy: approximately 0.78.

These are pairwise associations. They do not establish causation or show the independent contribution of each predictor after accounting for the others.

### 2. Multiple Linear Regression

The initial model includes all seven numeric predictors:

```r
lm.full <- lm(Score ~ ., data = numeric_data)
summary(lm.full)
```

The model summary is used to examine coefficient estimates, individual significance tests, the overall F-test, R² and residual standard error.

The project also discusses why a variable can have a strong pairwise correlation with the response but a weaker conditional association in a multiple regression model. Correlation between predictors can affect coefficient estimates and their uncertainty.

### 3. Residual Diagnostics

Two diagnostic plots are examined:

- **Normal Q–Q plot:** assesses departures from normality in the residual distribution.
- **Residuals versus fitted values:** checks for visible patterns, nonlinearity and unequal residual spread.

These plots provide diagnostic evidence rather than proof that all model assumptions hold. In particular, a residuals-versus-fitted plot alone does not establish independence.

### 4. Best Subset and Forward Selection

Best subset selection is implemented using `leaps::regsubsets()` to identify the best-fitting predictor combination at each model size.

Forward selection is also applied. It builds a sequence of models by adding predictors incrementally.

The original report states that the two approaches selected the same predictor combinations at each size for this dataset. This agreement is specific to the analysis and is not guaranteed in other datasets.

Selection criteria include:

- Residual sum of squares (RSS).
- Adjusted R².
- Mallows’ Cp.
- Bayesian Information Criterion (BIC).

### 5. Five-Fold Cross-Validation

The analysis uses `set.seed(1)` and randomly assigns observations to five folds.

For each fold, subset selection is performed on the remaining observations. A custom prediction function generates predictions for the held-out observations, and mean squared error is calculated.

The implementation compares model sizes from **one to six predictors**. The seven-predictor full model is not included in this cross-validation comparison.

Fold sizes may differ because fold labels are sampled with replacement.

### 6. Confidence and Prediction Intervals

The selected three-predictor model is fitted to the complete dataset.

The project then illustrates:

- A **confidence interval** for the conditional mean happiness score.
- A **prediction interval** for an individual new observation.

The prediction interval is wider because it includes both uncertainty in the estimated mean and individual outcome variation.

## Reported Results

The following results are recorded in the original project write-up.

| Measure or criterion | Reported result |
|---|---|
| Full-model R² | 0.9817 |
| Full-model residual standard error | 0.1541 |
| Minimum RSS | Seven-predictor model |
| Maximum adjusted R² | Seven-predictor model |
| Mallows’ Cp selection | Seven-predictor model |
| BIC selection | Three-predictor model |
| Minimum cross-validation MSE among sizes 1–6 | Three-predictor model |

The three-predictor model selected on the full dataset contains:

```r
Score ~ Overall.rank + Social.support + Perceptions.of.corruption
```

### Cross-Validation Comparison

| Number of predictors | Reported mean CV MSE |
|---:|---:|
| 1 | 0.02764912 |
| 2 | 0.02840425 |
| 3 | 0.02531475 |
| 4 | 0.02657734 |
| 5 | 0.02687342 |
| 6 | 0.02723367 |

The three-predictor model has the lowest reported error in this comparison. The modest differences between model sizes should not be interpreted as statistically significant without further assessment.

## Important Interpretation: Target Leakage

The original analysis includes `Overall.rank` as a predictor of `Score`.

Because the rank is derived from the happiness score, it contains information about the response itself. This creates target leakage when the goal is to predict an unknown happiness score using independently available information.

Consequently:

- The high R² should not be interpreted as evidence that socioeconomic indicators alone explain 98.17% of score variation.
- Cross-validation does not remove leakage already present in a predictor.
- A rank-based model has limited value for predicting scores for countries whose rankings are not yet known.

The project remains useful as a demonstration of regression and model-selection techniques. For a more meaningful predictive analysis, the workflow should be repeated after excluding `Overall.rank`.

## Limitations and Possible Extensions

- The analysis is observational and does not establish causal relationships.
- The reported results depend on one random cross-validation split.
- Cross-validation compares six candidate sizes rather than all seven.
- Dataset provenance and variable definitions should be documented alongside the CSV.
- A useful extension would compare models without the ranking variable.
- Repeated cross-validation could assess the stability of model-size selection.
- Further diagnostics could examine influential observations and multicollinearity.

## Tools and Packages

- **R:** statistical analysis and modelling.
- **R Markdown:** reproducible reporting.
- **leaps:** best subset and forward selection.
- **corrplot:** correlation visualisation.
- **MASS:** loaded in the original analysis.
- **knitr / rmarkdown:** report rendering.

## How to Run

1. Download or clone the repository.
2. Place `happiness_score.csv` in the same folder as the R Markdown file.
3. Open the project in RStudio.
4. Install the required packages:

```r
install.packages(c("MASS", "leaps", "corrplot", "knitr", "rmarkdown"))
```

5. Set the working directory to the project folder.
6. Open the `.Rmd` file and select **Knit → Knit to HTML**.

HTML rendering avoids the additional LaTeX setup required for PDF output.

## Skills Demonstrated

- Exploratory data analysis.
- Multiple linear regression.
- Statistical interpretation.
- Residual diagnostics.
- Model selection and complexity assessment.
- Cross-validation and prediction error evaluation.
- Confidence and prediction intervals.
- Critical assessment of modelling limitations.

## Academic Context and Contributors

Developed as a group coursework project for MAS451 at the University of Cyprus.


This repository presents academic work and the methodological lessons learned from it.
