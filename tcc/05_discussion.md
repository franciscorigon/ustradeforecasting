# 5 DISCUSSION

        This section interprets the results from two complementary perspectives: methodological, in the practice of Information Systems and applied machine learning, and economic, within the literature on exchange rate transmission and bilateral trade flows. The primary research question — what is the impact of exchange rate fluctuations on export and import flows between the United States and Canada, Mexico, and Brazil, and which forecasting models are best for making these forecasts — receives an affirmative empirical answer, contextualized here within the theoretical predictions of the exchange rate transmission literature and the comparative forecasting evidence accumulated in the field. The discussion proceeds from model comparison toward feature-level interpretability, heterogeneity, methodological contributions and limitations.

## 5.1 Machine learning gains over ARIMA

        The predictive advantage of Random Forest and LightGBM over ARIMA, evidenced by the Diebold-Mariano test in 75% and 83% of series, is consistent with the structural advantage of non-parametric models where trade dynamics are non-linear. ARIMA restricts the relationship between past and future values to a fixed-coefficient combination of lagged terms and disturbances. Tree-based ensembles, by contrast, approximate non-linear interactions among the 73 features without explicit parametric assumptions, capturing joint effects of exchange rates, energy prices and macroeconomic variables.

        The prominence of the WTI crude oil price as the most influential feature in Random Forest, alongside exchange rate and industrial production variables, illustrates an interaction that linear models struggle to represent. Energy prices and currency dynamics jointly affect commodity-intensive trade corridors through non-linear channels that ARIMA's additive structure cannot represent. Tree splits capture these interactions implicitly, exploiting information that ARIMA cannot access by construction.

        The gap was largest in high-technology series (0.91 pp for Random Forest) and manufactured goods (0.46 pp), consistent with the argument that non-parametric models gain most where dynamics are heterogeneous. These categories show heterogeneous demand, sensitivity to global value chains and exposure to multiple macroeconomic factors, characteristics for which non-parametric models hold a comparative advantage (GOPINATH, M. et al., 2020). Commodities, although still favoring ML, had the smallest divergence between models, consistent with the linear pass-through of international prices into traded volumes.

## 5.2 When ARIMA outperforms: eight series and the role of structure

        The result that ARIMA outperformed both ML models in 8 of 24 series tempers any sweeping claim of methodological superiority. The series in which ARIMA prevailed show a consistent pattern: lower volatility, near-stationary behavior after differencing and no structural breaks within the test window. Mexican total exports (MAPE 0.40% under ARIMA) exemplify this profile, a series whose dynamics are well captured by a parsimonious linear specification.

        One methodological factor compounds this analysis: the prediction protocol asymmetry. ARIMA's walk-forward one-step procedure structurally favors shorter horizons relative to the 36-step direct forecast used by RF and LightGBM, an asymmetry examined in detail in Section 5.3.

        The two series flagged by the Ljung-Box test (Canadian exports of commodities and Canadian total imports) merit separate consideration. Canadian total imports showed ARIMA errors substantially above the model's average; Canadian exports of commodities, by contrast, is a case where ARIMA outperformed RF despite the Ljung-Box flag, illustrating that misspecification and performance degradation are not mechanically linked. Excluding both series, the average gap between ARIMA and Random Forest is 0.51 percentage points — consistent with the estimate in Section 4 — confirming that the ML advantage is not an artifact of ARIMA misspecification. A properly specified linear model therefore remains a competitive baseline for well-behaved series, and the question is not whether ML is universally superior but under which structural conditions each approach offers meaningful gains.

## 5.3 The Naïve baseline and information asymmetry

        The Naïve baseline's lower MAPE (0.32%) reflects the informational asymmetry described in Section 4.2: the Naïve predictor accesses the realized value of the previous month at each step, while RF and LightGBM operated as direct 36-step predictors without intermediate feedback. This protocol asymmetry, not a methodological flaw, accounts for the apparent baseline advantage.

        The implication is twofold. First, the Naïve baseline serves as an upper-bound informational reference, not as a competing forecasting strategy. Second, a 36-step direct forecast and a sequential one-step projection have different operational uses. Trade strategy and macroeconomic planning typically require multi-month lead times incompatible with a Naïve approach that demands the previous observation. A policymaker or trade analyst forecasting 12 months ahead cannot apply a Naïve model that requires each month's realized value before projecting the next. Random Forest and LightGBM, though penalized in this benchmark, generate forecasts directly applicable to such planning horizons.

        This result reinforces the importance of paired statistical tests, such as the Diebold-Mariano employed here, when comparing models with structurally different prediction protocols.

## 5.4 Exchange rate as a relevant predictor

        The central research question concerned whether exchange rate fluctuations exert a measurable influence on bilateral trade flows. The SHAP analysis offers strong empirical support for an affirmative answer. In both Random Forest and LightGBM, exchange rate variables, particularly the USD/CAD percentage change, appeared consistently among the top-ranked predictors, alongside the WTI crude oil price and country-specific industrial production indices.

        The complementary patterns between the two models add interpretive depth. Random Forest gave greater weight to moving-average representations of exchange rates, with six-month windows prominent, while LightGBM placed proportionally higher weight on percentage-change features. Random Forest thus captured medium-term trend persistence, while LightGBM responded more sensitively to short-term directional shifts. The convergence of both models in identifying exchange rate variables as relevant predictors, through different representations of the same signal, strengthens the finding.

        This dual confirmation constitutes the primary empirical answer to the research question framed in Section 1: bilateral exchange rate movements are not a peripheral input but a structurally relevant determinant of monthly trade flow dynamics, confirmed across two independent models through complementary interpretability tools.

## 5.5 Country-level heterogeneity in exchange rate sensitivity

        The pronounced heterogeneity in FX sensitivity across the three trading partners (Brazil at the higher end, Canada at the lower, Mexico intermediate) can be interpreted in light of the structural and institutional features of each bilateral relationship. Brazil's elevated sensitivity is consistent with its structural profile as an emerging economy: less diversified export structures, higher commodity dependence and the absence of a preferential trade agreement with the United States produce greater exposure to bilateral currency fluctuations.

        Canada's comparatively low FX sensitivity reflects the stabilizing effect of deep productive integration under USMCA, supported by structural complementarity of bilateral supply chains. When exchange rates fluctuate, integrated production processes, particularly in automotive and industrial machinery, show price stickiness and contractual rigidities that dampen the immediate transmission of currency shocks to traded volumes (BERGIN; CORSETTI, 2020).

        Mexico's intermediate position is consistent with its hybrid integration profile: USMCA provides partial insulation analogous to Canada's, while the maquiladora-driven manufacturing structure and the commodity component retain residual sensitivity to exchange rate movements.

## 5.6 Sectoral patterns in exchange rate transmission

        The sectoral disaggregation revealed a consistent ordering of FX sensitivity: commodities at the higher end, manufactured goods intermediate and high-technology at the lower end. The pattern was stable across the three country pairs.

        The high sensitivity of commodity flows reflects the predominance of USD-denominated international pricing in markets such as crude oil, soybeans and iron ore. When the dollar appreciates or depreciates against a partner currency, traded volumes adjust through arbitrage and substitution effects with limited domestic insulation.

        The lower sensitivity of high-technology products reflects the differentiated nature of these goods, the prevalence of long-term contracts and integration within global value chains. Bergin and Corsetti (2020) document that relationship-specific investments and bilateral pricing arrangements partially insulate differentiated goods from short-run currency fluctuations, and the same mechanism operates in U.S. trade with Canada, Mexico and Brazil. Manufactured goods occupied an intermediate position, combining standardized inputs (more exposed to exchange rate arbitrage) and differentiated components within integrated supply chains.

## 5.7 Methodological contributions

        Beyond the findings on exchange rate sensitivity, this study offers methodological contributions for Information Systems research applied to economic forecasting. The first is the implementation of a complete CRISP-DM pipeline across all six phases, applied to monthly macroeconomic forecasting with multiple country pairs and sectoral granularity (SHIMAOKA et al., 2024; MARTÍNEZ‐PLUMED et al., 2019). The framework integrated heterogeneous data sources, reproducible feature engineering and systematic model evaluation. The novelty lies not in the methodology itself but in its application to bilateral macroeconomic trade forecasting, a domain where structured ML pipelines remain underrepresented in the SI literature.

        The second contribution is the systematic application of SHAP to both Random Forest and LightGBM, enabling a comparative interpretability analysis beyond the practice of analyzing only the best-performing model. In IS, where models inform policy and business decisions, interpretability is a design requirement — not an optional complement to predictive accuracy. By contrasting moving-average and percentage-change FX features across the two algorithms, the analysis revealed complementary perspectives on the same phenomenon. This dual-model approach reduces dependence on a single learner's inductive bias and aligns with the literature on machine learning for policy-relevant forecasting (KHAN et al., 2024).

        The third contribution is the statistical evaluation framework. The Diebold-Mariano test with the Harvey et al. (1997) small-sample correction provides rigorous pairwise comparisons that do not rely on aggregate metric averages. It exposed the predictive hierarchy that the non-significant Friedman result alone would have obscured — a distinction with direct IS implications: deployment decisions must rest on statistically validated performance differences, not on aggregate metric rankings.

        The fourth contribution is reproducibility. The entire pipeline (source code, datasets, trained models and evaluation outputs) is in a public repository with versioned notebooks. Reproducibility, often neglected in applied economic research, is a central methodological requirement in Information Systems and was treated here as a first-class deliverable.

## 5.8 Limitations

        Three methodological limitations merit attention. The sectoral target series were estimated by combining annual HS2 sectoral proportions with monthly aggregate flows, an approximation that introduces systematic distortion in periods of structural change within a given year. The effect is most pronounced for commodities, where intra-annual price volatility and shipment lumpiness deviate from the annual proportional structure. Future work could replace this approximation with monthly HS2 disaggregated series once available with sufficient coverage.

        The test window (January 2022 to December 2024) overlaps with major macroeconomic disturbances: the post-pandemic recovery, the energy price shock following the 2022 Russia–Ukraine conflict and the subsequent U.S. monetary tightening cycle. Although these disturbances are part of the realized empirical record, they may produce performance estimates not fully representative of more stable regimes. Additional out-of-sample windows would help characterize the regime sensitivity of the comparative results. Practitioners applying these models to other time periods should consider recalibration to account for regime differences.

        The direct 36-step forecast protocol for RF and LightGBM was chosen to reflect the operational scenario of multi-month trade planning, where intermediate realized values are unavailable; this design, intentional and documented in Section 5.3, limits symmetric comparison with ARIMA's walk-forward procedure. A future iteration could complement this with recursive multi-step retraining for a fully comparable evaluation.
