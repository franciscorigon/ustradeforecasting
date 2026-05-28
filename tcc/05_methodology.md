# 5 METHODOLOGY

        In this research, CRISP-DM (Cross-Industry Standard Process for Data Mining) was used to predict the impact of exchange rate changes on trade flows between the United States and Canada, Mexico, and Brazil. Widely recognized in data mining projects, CRISP-DM organizes the work into well-defined phases, guaranteeing quality and transparency from business understanding to deployment (TRIPATHI et al., 2021).

        The process consists of six phases addressed in this chapter: Business Understanding, Data Understanding, Data Preparation, Modeling, Evaluation and Deployment (WIRTH; HIPP, 2000). Although presented sequentially, the actual research involved repeated cycles between Data Preparation and Modeling, and between Modeling and Evaluation, consistent with the iterative nature of the framework.

## 5.1 Business Understanding

        This is the first phase of CRISP-DM, where project goals are set. Requirements are transformed into a definition of the data mining problem and project plans (SHIMAOKA et al., 2024). It also identifies external forces such as exchange rate volatility and macroeconomic shocks, which affect both analytical models and the interpretation of results (NGALAWA; KUTU, 2017).

        In this study, the Business Understanding phase determined the most important macroeconomic variables, defined performance metrics, and stated the core problem. The global purpose was to assess Random Forest and LightGBM compared to ARIMA when predicting trade flows between the United States and Canada, Mexico and Brazil from 2000 to 2025 under exchange rate fluctuations. The choice of variables and metrics came from the theoretical review of ARIMA limitations on complex macroeconomic series and the potential of ML to model nonlinear relationships (LIN et al., 2020).

## 5.2 Data Understanding

        In this analysis, data included USD/CAD, USD/MXN and USD/BRL exchange rates from central banks and the Federal Reserve (FED), bilateral trade flows from UN Comtrade and the U.S. Census Bureau, macroeconomic indicators from the World Bank and OECD, and sectoral data on commodities, manufactured goods and high-tech to capture sectoral asymmetry.

        The data covered the period from 2000 to 2024 at multiple frequencies, daily for exchange rates and monthly for macroeconomic variables and trade flows. The modeling window was restricted to 2010–2024 by the availability of monthly Comtrade data, yielding 180 monthly observations. The 24 target series combined three countries (Canada, Mexico, Brazil), two directions (exports and imports) and four sectors (commodities, manufactured goods, high-technology and total), producing the 3 × 2 × 4 design used in subsequent phases.

## 5.3 Data Preparation

        The Data Preparation phase ensured the quality and consistency of the raw data through cleaning, transformation and feature engineering, producing the analytical dataset used in the modeling phase.

        Outlier detection and processing, which identifies atypical observations, is important because untreated outliers may mislead statistical results and prediction accuracy (TAJMOUATI et al., 2022). Outliers were identified using descriptive statistical methods, including the interquartile range and visual inspection of graphs (SUDJIANTO; ZHANG, 2024). Automatic exclusion is not generally recommended; including outliers can be reasonable for checking model robustness, rather than treating them as invalid data (DIAS et al., 2020).

        Feature engineering generated and transformed variables to capture temporal behavior for econometric and ML models (CHOWDHURY et al., 2024). Transformations such as lags, moving averages and percentage variations were central to representing the exchange rate signal across different time scales (LIN et al., 2020).

        In this study, the Data Preparation pipeline aggregated daily exchange rate series to monthly frequency by simple mean, and forward-filled quarterly GDP series to monthly. Two target representations were generated: a log-transformed variant for Random Forest and LightGBM, and a log-differenced variant for ARIMA. Stationarity of the differenced series was confirmed at the 5% level through the Augmented Dickey-Fuller test for most series. Outliers identified by the interquartile range were retained, complemented by binary indicators for the Global Financial Crisis (2008–2009) and the COVID-19 pandemic (2020–2021). Feature engineering generated lags (1, 3, 6 and 12 months), moving averages (3, 6 and 12 months), monthly percentage variations and calendar indicators. The final dataset contained 180 monthly observations across 97 columns, 73 features and 24 target representations. The chronological split used January 2010 to December 2021 for training and January 2022 to December 2024 for the out-of-sample test, preserving temporal order and avoiding look-ahead bias (VERSTRAETE et al., 2021).

## 5.4 Modeling

        The Modeling phase applied ARIMA, Random Forest and LightGBM to the prepared dataset, comparing their predictive performance on the 24 bilateral trade flow series.

        ARIMA was used as a univariate benchmark, while Random Forest and LightGBM accepted multivariate inputs without requiring stationarity, allowing the incorporation of lagged macroeconomic variables and feature-engineered transformations (PORTO, 2022; BOTZ et al., 2025; ZANOTTI, 2025).

        For each univariate time series, ARIMA modeling started with a stationarity test (Augmented Dickey-Fuller, ADF) to determine the differentiation order (d). Final model selection used information criteria such as AIC and BIC, identifying the most parsimonious and best-fitted models. Residual diagnostics confirmed the absence of heteroscedasticity and autocorrelation, validating model assumptions (REICHERT; SOUZA, 2020). The backtesting process used a sliding window approach, with models trained on historical data up to a point in time and predictions generated for the following period (BOTZ et al., 2025).

        Random Forest is implemented through an ensemble that aggregates multiple decision trees over the supervised matrix (ABIR et al., 2025). Unlike ARIMA, it handles non-linear relationships and complex interactions between predictors without assuming stationarity (CHATTERJEE et al., 2024). It tolerates various data types and is less sensitive to outliers and multicollinearity, an advantage for macroeconomic and financial data (LING; WANG, 2024; SALMAN et al., 2024).

        LightGBM is a gradient boosting algorithm known for speed and accuracy on large, high-dimensional datasets (ZANOTTI, 2025). It is also based on a supervised matrix, but requires caution in hyperparameter optimization and treatment of missing values (MORTEZANEJAD; WANG, 2025).

        Hyperparameter optimization used Optuna, which trains a probabilistic model relating hyperparameters to performance metrics (VRANJEŠ; NIGGEMANN, 2024; RASCHKA et al., 2020).

        The Modeling phase produced 72 forecasting models from the combination of 24 target series and 3 algorithmic families, plus a Naïve random-walk baseline. ARIMA models were fitted under a walk-forward protocol, with order selection by AIC grid search over p from 0 to 3, d from 0 to 1, and q from 0 to 3. Random Forest and LightGBM were configured as direct 36-step forecasters on the logarithmic target, with Optuna performing hyperparameter optimization (AKIBA et al., 2019) over 30 trials per series using a TimeSeriesSplit cross-validator with 5 folds. The Naïve baseline used the last observed log value of the series; its structural information advantage over the 36-step ML forecasters is discussed in Sections 4.2 and 5.3.

## 5.5 Evaluation

        The Evaluation phase assessed the predictive performance of the three models using error metrics, statistical significance tests and interpretability tools.

        Predictive accuracy was measured through the Root Mean Squared Error (RMSE), Mean Absolute Error (MAE) and Mean Absolute Percentage Error (MAPE), with MAPE adopted as the primary metric due to its scale invariance (AYITEY et al., 2023).

        Although ML-based models may provide superior performance, their interpretability is often difficult to achieve (SUDJIANTO; ZHANG, 2024). To address this opacity, SHAP-based explainers were applied, elucidating the contribution of each input variable to the model's predictions (GUO et al., 2024). Overcoming the opacity of "black boxes" is crucial for trust and adoption of these predictive tools (ABOUZAID; BOUSSEDRA, 2025).

        In this study, the evaluation combined four elements. Point-forecast metrics were RMSE, MAE and MAPE, with MAPE adopted as the primary metric due to its scale invariance. The Ljung-Box test at lag 10 verified the ARIMA in-sample residuals. The significance tests were the Friedman test for the omnibus comparison, the Wilcoxon signed-rank with Bonferroni correction for pairwise comparisons, and the Diebold-Mariano test with the Harvey et al. (1997) small-sample correction, used as the primary instrument for ranking the three algorithms. SHAP (SHapley Additive exPlanations) values were computed through the TreeExplainer algorithm of the shap library (LUNDBERG; LEE, 2017), generated independently for Random Forest and LightGBM. A composite exchange rate sensitivity metric, the mean absolute SHAP value of FX and REER features averaged between the two models, was visualized as a heatmap across the 24 country-sector combinations.

## 5.6 Deployment

        The Deployment phase operationalized reproducibility as the primary deliverable, organizing all pipeline artifacts in a versioned public repository.

        Documentation and versioning are essential for traceability and transparency throughout the model lifecycle. Code versioning uses systems such as Git and collaborative platforms like GitHub (SEMMELROCK et al., 2024). The ability to easily revert changes fosters experimentation and innovation by reducing the perceived risk of adopting new approaches (STROMER et al., 2024).

        Although a production-grade forecasting service was outside the scope of this academic study, the Deployment phase of CRISP-DM was operationalized through the publication of a reproducible artifact set. The complete pipeline (raw and processed datasets, feature engineering scripts, trained models, hyperparameter configurations, forecast outputs and evaluation notebooks) was organized in a public Git repository (XXXXXXXXXX, accessed May 2026) with versioned commits, treating reproducibility as the primary deployment deliverable.
