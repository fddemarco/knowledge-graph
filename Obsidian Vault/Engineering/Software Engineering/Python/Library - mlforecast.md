---
base: "[[Reading List.base]]"
Category:
  - ML
Author: Nixtla
Status: Not started
---
[Repo](https://github.com/Nixtla/mlforecast)

**mlforecast** is a framework to perform time series forecasting using machine learning models, with the option to scale to massive amounts of data using remote clusters.

## Why?

Current Python alternatives for machine learning models are slow, inaccurate and don’t scale well. So we created a library that can be used to forecast in production environments. [`MLForecast`](https://nixtlaverse.nixtla.io/mlforecast/forecast.html#mlforecast) includes efficient feature engineering to train any machine learning model (with `fit` and `predict` methods such as [`sklearn`](https://scikit-learn.org/stable/)) to fit millions of time series.

## Features

- Fastest implementations of feature engineering for time series forecasting in Python.
- Out-of-the-box compatibility with pandas, polars, spark, dask, and ray.
- Probabilistic Forecasting with Conformal Prediction.
- Support for exogenous variables and static covariates.
- Familiar `sklearn` syntax: `.fit` and `.predict`.
## Examples and Guides

📚 [End to End Walkthrough](https://nixtlaverse.nixtla.io/mlforecast/docs/getting-started/end_to_end_walkthrough.html): model training, evaluation and selection for multiple time series.

🔎 [Probabilistic Forecasting](https://nixtlaverse.nixtla.io/mlforecast/docs/tutorials/prediction_intervals_in_forecasting_models.html): use Conformal Prediction to produce prediction intervals.

👩‍🔬 [Cross Validation](https://nixtlaverse.nixtla.io/mlforecast/docs/how-to-guides/cross_validation.html): robust model’s performance evaluation.

🔁 [M5: Reuse CV Splits + Global/Grouped Rolling Means](https://nixtlaverse.nixtla.io/mlforecast/docs/how-to-guides/hyperparameter_optimization.html): optimize with cached CV windows while tuning global and grouped rolling features in one workflow.

🔌 [Predict Demand Peaks](https://nixtlaverse.nixtla.io/mlforecast/docs/tutorials/electricity_peak_forecasting.html): electricity load forecasting for detecting daily peaks and reducing electric bills.

📈 [Transfer Learning](https://nixtlaverse.nixtla.io/mlforecast/docs/how-to-guides/transfer_learning.html): pretrain a model using a set of time series and then predict another one using that pretrained model.

🌡️ [Distributed Training](https://nixtlaverse.nixtla.io/mlforecast/docs/getting-started/quick_start_distributed.html): use a Dask, Ray or Spark cluster to train models at scale.
