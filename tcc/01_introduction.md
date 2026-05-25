# 1 INTRODUCTION

        The purpose of this study was to investigate the impact of exchange rate volatility on bilateral trade flows between the U.S. and its main partners: Canada, Mexico and Brazil, which is the largest U.S. trading partner in Latin America (USITC, 2025).

        Canada and Mexico trade with the USA through USMCA, while Brazil trades without an equivalent free trade agreement (USTR, 2020). The comparison between an advanced and a developing economy enabled the detection of differences in the pattern of foreign trade sensitivity to exchange rate fluctuations, contributing to the existing debate (AUBOIN; RUTA, 2011).

        The analyzed period was originally framed from 2000 to 2025, with the actual modeling window restricted to 2010–2024 due to monthly data availability in the UN Comtrade database. The study used monthly data to capture short-term volatility of trade flows and exchange rate movements (SHEN et al., 2021).

        Beyond aggregate trade, the study also offered sectoral analysis to assess why different commodities respond asymmetrically to exchange rate fluctuations, providing a finer view of sectoral heterogeneity.

        The study used Random Forest and LightGBM to forecast bilateral trade flows and compared them to ARIMA in terms of predictive ability (SONG; CHEN, 2024). Exports and imports between the U.S. and the chosen countries were analyzed using macroeconomic indicators such as GDP, nominal and real exchange rates, industrial production, and inflation (KALLIANIOTIS, 2022). Trade flows and exchange rate dynamics are vulnerable to outlier events such as global financial crises and pandemics, and the influence of such events on predictive models was examined (BATARSEH et al., 2020). The research followed the CRISP-DM approach, from business understanding to deployment (MARTÍNEZ‐PLUMED et al., 2019).

        The primary research question was: "What is the impact of exchange rate variations on export and import flows between the United States and Canada, Mexico, and Brazil, and which forecasting models are best for making these forecasts?"

        Additional questions complemented the primary one: at which horizons is the predictive power most informative, and how does it vary across models? Which industries are most sensitive to exchange rate changes, and how does this differ between developed and emerging economies? The study also explored asymmetric effects on exports and imports in response to currency appreciation and depreciation, the effect of dollar depreciation on bilateral trade balances, and compared the forecasting ability of econometric models against machine learning models for non-stationary time series.

        The empirical results are reported in Section 4 and interpreted in Section 5. Random Forest and LightGBM outperformed ARIMA in 75% and 83% of the 24 series respectively, with Random Forest as the best-performing algorithm. The SHAP analysis identified the bilateral exchange rate as a relevant predictor in both ML models. Brazil emerged as the most exchange-rate-sensitive partner, Canada the least, and commodity flows showed greater responsiveness than high-technology products.

        The remainder of this paper is organized as follows. Section 2 reviews the theoretical framework. Section 3 describes the methodology along the six CRISP-DM phases. Section 4 presents the empirical results. Section 5 discusses the findings, and Section 6 concludes.
