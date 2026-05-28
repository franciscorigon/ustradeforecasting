# 1 INTRODUCTION

        The purpose of this study was to investigate the impact of exchange rate volatility on bilateral trade flows between the U.S. and three important and distinct partners: Canada, Mexico, and Brazil (USITC, 2025).

        Canada and Mexico trade with the USA through USMCA, while Brazil trades without an equivalent free trade agreement (USTR, 2020). The comparison between partners with distinct economic profiles enabled the detection of differences in the pattern of foreign trade sensitivity to exchange rate fluctuations, contributing to the existing debate (AUBOIN; RUTA, 2011).

        The study used Random Forest and LightGBM to forecast bilateral trade flows and compared them to ARIMA in terms of predictive ability (SONG; CHEN, 2024). Trade flows and exchange rate dynamics are vulnerable to outlier events such as global financial crises and pandemics, and the influence of such events on predictive models was examined (BATARSEH et al., 2020). The research followed the CRISP-DM approach, from business understanding to deployment (MARTÍNEZ‐PLUMED et al., 2019).

        The primary research question was: "What is the impact of exchange rate fluctuations on export and import flows between the United States and Canada, Mexico, and Brazil, and which forecasting models are best for making these forecasts?"

        Additional questions complemented the primary one: which forecasting approach performs better for bilateral trade flows — traditional econometric models or machine learning algorithms? Which sectors are most sensitive to exchange rate changes, and how does this differ across developed and emerging economies? How does exchange rate sensitivity vary across country pairs, and which macroeconomic factors drive trade flow dynamics alongside the exchange rate?

        The empirical results are reported in Section 4 and interpreted in Section 5. Random Forest and LightGBM outperformed ARIMA in 75% and 83% of the 24 series respectively, with Random Forest as the best-performing algorithm. The SHAP analysis identified the bilateral exchange rate as a relevant predictor in both ML models. Brazil emerged as the most exchange-rate-sensitive partner, Canada the least, and commodity flows showed greater responsiveness than high-technology products.

        The remainder of this paper is organized as follows. Section 2 reviews the theoretical framework. Section 3 describes the methodology along the six CRISP-DM phases. Section 4 presents the empirical results. Section 5 discusses the findings, and Section 6 concludes.
