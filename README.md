# Fuel Economy Prediction | Machine Learning in R

Predicting highway fuel economy from engine displacement, cylinder count, vehicle class and drivetrain using `ggplot2::mpg`. This reproducible workshop project compares two regression approaches on 234 historical vehicle configurations from 1999 and 2008. The analysis report is in Spanish.

## Key results

Both models use the same five cross-validation folds from the training set:

| Model | CV RMSE (mpg) | CV MAE (mpg) | CV squared correlation |
|---|---:|---:|---:|
| Linear regression (`lm`) | 2.341 | 1.707 | 0.848 |
| Decision tree (`rpart`) | 2.932 | 2.161 | 0.778 |

Linear regression was selected using cross-validation, before evaluating the reserved test set. On its **48 held-out rows**, the final model achieved **RMSE 2.995 mpg**, **MAE 1.985 mpg** and **squared correlation 0.786**. These describe this particular row-level split, rather than performance on new vehicle generations or markets.

Here, `yardstick::rsq()` is the squared correlation between observed and predicted values, not a percentage of correct predictions or the traditional residual-based R². [Metric definition](https://yardstick.tidymodels.org/reference/rsq.html).

## Methodology

- Split the data before EDA: approximately 80/20, stratified by `hwy`, with seed 123; 186 training rows and 48 test rows.
- Explore training data only. Use `recipe(hwy ~ displ + cyl + class + drv)` with dummy encoding and zero-variance removal; retain numeric `cyl`.
- Fit the recipe inside each `workflow()` and each resample to keep preprocessing within training folds.
- Compare `lm` and the default `rpart` regression tree on common five-fold CV splits, using RMSE, MAE and squared correlation. No hyperparameter tuning or additional models.
- Fix the model choice, then use `last_fit()` once for the final held-out evaluation.

## Tech stack

R, tidymodels, ggplot2, rpart and R Markdown. The report includes the original tables, three plots and `sessionInfo()`.

## View report

[Download the self-contained HTML report](docs/index.html) · [Read the R Markdown source](Proyecto_Final_ML.Rmd)

Download the HTML and open it in a browser to explore the report without R. Its figures, styles and scripts are embedded.

## How to reproduce

Verified with **R 4.6.1 on Windows 11**, tidymodels 1.5.0, ggplot2 4.0.3, rpart 4.1.27, rmarkdown 2.32 and knitr 1.52. Pandoc 3.10 was available through RStudio. Use RStudio's bundled Pandoc or make Pandoc available to R Markdown.

From the repository root, in a fresh R session:

```r
install.packages(c("tidymodels", "ggplot2", "rpart", "rmarkdown", "knitr"))
rmarkdown::render("Proyecto_Final_ML.Rmd", output_file = "docs/index.html", encoding = "UTF-8")
```

No external data files are needed. Seeds and session versions are recorded in the report. Package versions are documented rather than locked; changes in R or dependencies may affect future numerical reproduction. The published metrics were checked against a fresh render and the supplied original HTML.

## Limitations

The dataset is small and historical: 234 configurations covering 38 car models in 1999 and 2008. Random row splitting can place similar configurations of the same model in both training and test sets. This is not grouped or temporal validation, and it does not establish performance on unseen car models, current vehicles or driving conditions in Costa Rica. Errors may differ across vehicle categories. Predictions are educational estimates, not official consumption ratings or emissions estimates; no deployed model or commercial impact is claimed.

## Data source

[`ggplot2::mpg` documentation](https://ggplot2.tidyverse.org/reference/mpg.html), a subset of U.S. Environmental Protection Agency fuel-economy data available through [FuelEconomy.gov](https://fueleconomy.gov/). The response `hwy` is highway mileage in miles per U.S. gallon; `displ` is measured in litres.
