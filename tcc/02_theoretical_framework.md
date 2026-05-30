# 2 THEORETICAL FRAMEWORK

        The theoretical basis for this study spans how exchange rate volatility impacts international trade flows and how predictive models assess these dynamics, drawing on economics, international relations, and the technological field.

## 2.1 Economic key concepts

        Nominal exchange rate: The nominal exchange rate is the relative price of two currencies and plays a major role in international competitiveness through its direct impact on import and export prices (KIM; LE, 2024). As stated by Kallianiotis (2022, p. 46), "Devaluation increases the price of imports and reduces the price of exports; and due to the law of demand, imports are falling and exports are increasing and the trade account is improved".

        REER: The Real Effective Exchange Rate (REER) is a broader measure representing the relative price of goods and services of a country versus its trading partners, adjusted for price differences and inflation (WAHAB, 2024). It thus reflects an economy's competitiveness, considering inflation differentials that would otherwise erode or magnify the impact of nominal exchange rate changes (MWANGE et al., 2022).

        HS: The Harmonized Commodity Description and Coding System (HS) is an international nomenclature for trade sector classification, indispensable for recording detailed trade flows (SCHMIDT; STEINGRESS, 2019). Its granularity allows analyzing how product categories react differently to exchange rate changes, revealing sector-specific heterogeneities (SELLAMI et al., 2024).

## 2.2 Models for determining the exchange rate and choosing exchange rate variables: USD/XXX rate and REER

        Various theories have been developed to explain the factors that determine exchange rates, and relying on a single model can be inappropriate (MWANGE et al., 2022). The analysis of the nominal exchange rate (USD/XXX) builds on traditional approaches: the International Fisher Effect (IFE), linking interest rates, inflation and devaluation expectations; Interest Rate Parity (IRP), connecting domestic and foreign rates; and the balance of payments theory, which considers the exchange rate as determined by foreign transactions and financial exchanges among countries (MWANGE et al., 2022). The U.S. dollar's superiority as an anchor currency further justifies its use, since it shapes prices internationally even for trades not involving the U.S. (GOPINATH, G. et al., 2020).

        Thus, the choice of USD/XXX enables a straightforward analysis of the U.S. currency's effect on the trade balance.

        The REER is justified since it adjusts for inflation differentials and trade structures with various partners, reflecting the logic of Purchasing Power Parity (PPP) but going beyond it by accounting for shifts in trade flows and bilateral relationships (MWANGE et al., 2022). It is a key variable for macroeconomic analysis in open economies, providing crucial information for policymaking, particularly during crises (BAYOUMI et al., 2005).

        Therefore, the combined use of REER and USD/XXX unites both perspectives, considering the direct impact of the U.S. dollar and global competitiveness adjusted for inflation, improving the predictive power of the analyses.

## 2.3 Trade flows and sectoral sensitivity

        The structure of bilateral trade flows is a prerequisite for examining sectoral sensitivity to exchange rate changes. Detailed HS-level data allows determining trade destinations and values, and modeling specific sectoral relations. This granularity reveals how exchange rate changes, particularly of the dollar, affect trade volumes (GOPINATH, G. et al., 2020), identifying areas vulnerable or stable to exchange rate shocks.

### 2.3.1 Sectoral impacts of exchange rate fluctuations

        Considering sectoral specificity is fundamental for accurately estimating export and import demand elasticity. The literature shows that exchange rates have differentiated impacts across various sectors (GODA et al., 2024).

### 2.3.2 Commodities vs. manufactured goods vs high technology

        Commodities, for example, are sensitive to exchange rate fluctuations, as they reflect international prices that respond directly to dollar variations. Production costs and loss of export competitiveness in a strong currency environment immediately affect export incentives and domestic supply (OLIVEIRA et al., 2023). Product complexity and technological intensity determine sectoral sensitivity to exchange rate fluctuations, with low-complexity sectors in developing countries showing the greatest elasticities. Less sophisticated sectors have more flexible production costs and depend more on relative prices to maintain international competitiveness (GODA et al., 2024).

        Conversely, Bergin and Corsetti (2020) claim that industries with differentiated goods, with nominal rigidity and costly entry, are less reactive to the exchange rate than industries producing substitutable goods, due to imperfect competition and dependence on stable macroeconomic preconditions.

## 2.4 Macroeconomic control variables

        Gross Domestic Product (GDP) and GDP per capita are among the most common macroeconomic variables for modeling trade flows, tracking economic development and supply-demand capacity. As noted by Wahab (2024), these variables help explain how similarity or dissimilarity in factor endowments affects bilateral trade patterns.

        Inflation is another fundamental variable linked to the exchange rate, significantly influencing the international competitiveness of products and the trade balance (NASIR; VO, 2020).

        Nominal exchange rate fluctuations also disrupt international negotiations and directly influence production costs and the competitiveness of tradable goods. While depreciation can increase imports of inputs while making exporters more competitive, appreciation reduces import prices of inputs (SANTORO; ROLIM, 2023).

        Financial and economic crises, accompanied by sharp exchange rate movements and market turbulence, also affect trade flows through changes in supply and demand for goods and services (ARÉVALO et al., 2022).

## 2.5 Time series forecasting models

        Time series analysis aims to model and predict the behavior of sequential data and discover temporal dependencies. The conventional approach, represented by econometric models such as ARIMA, assumes linearity and stationarity, so that statistical patterns remain stable and structural relationships can be modeled. Preprocessing techniques such as differencing make the data stationary (ABIR et al., 2024).

### 2.5.1 ARIMA model

        The AutoRegressive Integrated Moving Average (ARIMA), proposed by Box and Jenkins in the 1970s, is a classic statistical tool for time series forecasting, especially in finance (LIN et al., 2020). It suits series with linear trend and stationarity, or that can be transformed to satisfy these requirements (HE et al., 2020).

        The model has three main parameters: "p" (order of autoregression), "d" (degree of differencing for stationarity), and "q" (moving average order, capturing internal dependencies and removing trends or seasonality) (CHATTERJEE et al., 2024).

### 2.5.2 Stationarity tests

        Stationarity is fundamental for time-series models like ARIMA. Differencing (parameter "d") is used when data are non-stationary, to make them conducive for ARIMA fitting (CHATTERJEE et al., 2024). Formal tests such as the Dickey-Fuller test check the unit root and order of integration, serving as basis for further analysis such as co-integration (ARÉVALO et al., 2022).

## 2.6 Machine learning in forecasting

        Recent advances in machine learning have produced more flexible models for forecasting time series, capable of dealing with the non-linearity and complexity of economic phenomena (CHINN et al., 2023). Random Forest and LightGBM were chosen for this study because they capture non-linear relationships without imposing prior assumptions on distribution or stationarity (CHATTERJEE et al., 2024).

        Although not specifically developed for temporal dependency, Random Forest and LightGBM are extensively used in time series prediction due to their ability to model complex relationships and handle high-dimensional data. In these applications, time is incorporated through feature engineering using calendar information and series transformations (ZHANG; JIANG, 2025).

### 2.6.1 Random Forest

        Random Forest is a versatile and widely used algorithm for complex data. It is non-parametric, handling different responses and predictors across scales and distributions, suiting high-dimensional settings. Its prediction error remains stable as the number of trees grows (HU; SZYMCZAK, 2023). It mitigates the overfitting of isolated decision trees by creating multiple trees and outputting the mode (classification) or mean (regression) of individual trees (CHATTERJEE et al., 2024). This ensemble approach reduces variance and bias, maximizing predictive accuracy (LING; WANG, 2024).

        Random Forest also has practical advantages in economics and business: moderate resistance to outliers, tolerance to multicollinearity, and variable importance measures that improve interpretation. These traits make it a good choice for balancing accuracy and interpretability in empirical scenarios (SALMAN et al., 2024).

        Nevertheless, methodological weaknesses must be recognized, as raised by Sellami et al. (2024, p. 4):

> However, while Random Forest models are well suited for analyzing trade data, they have limitations in extrapolation. This inability to extrapolate effectively can hinder their accuracy in predicting future trade flows over time. Furthermore, in general, deep learning regression models tend to outperform Random Forest models.

### 2.6.2 LightGBM

        LightGBM is a gradient boosting framework with efficiency and accuracy on large data sets, optimized for training time and memory usage (CHATTERJEE et al., 2024). Its training acceleration techniques and reduced memory usage suit high-dimensional sparse scenarios, contributing significantly to model efficiency (GUO et al., 2024).

        Despite its advantages, interpreting LightGBM is difficult due to the high complexity of tree boosting models. Although the model produces accurate predictions, it does so less transparently than simple models, which matters when explainability is vital (GOPINATH et al., 2021).

### 2.6.3 Comparison between traditional approaches and ML

        The main difference between ML models like Random Forest and LightGBM and traditional econometric approaches is their intrinsic ability to model non-linear relationships and complex interactions, without imposing strong assumptions on data distribution (SELLAMI et al., 2024). This flexibility suits the prediction of complex phenomena such as exchange rates and trade flows, overcoming constraints of traditional methods (GUO et al., 2024). However, machine learning models are consistently described as "black boxes", making it difficult to grasp prediction logic compared with traditional econometric models, which are more transparent and built on explicit economic theory (ABOUZAID; BOUSSEDRA, 2025).

### 2.6.4 Hyperparameters and optimization

        Although machine learning models present "black box" challenges, interpretability techniques such as SHAP are starting to close this gap. Inspired by cooperative game theory, SHAP decomposes predictions into feature contributions, exposing non-linear structures and interactions otherwise hidden (ZHANG; JIANG, 2025). It assesses each feature's relative importance for individual predictions, improving interpretability and trust in complex models (GUO et al., 2024).

        This study therefore applied Random Forest and LightGBM to test their predictive ability on trade flows under fluctuating exchange rates, as an alternative to traditional methods. The implementation used Python and its statistical libraries, SQL for data integration, and GitHub for versioning and reproducibility.
