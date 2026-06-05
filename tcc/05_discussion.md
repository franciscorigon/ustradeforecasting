# 5 DISCUSSION

        This section interprets the results from two perspectives: methodological, in the practice of Information Systems and applied machine learning, and economic, within the literature on exchange rate transmission and bilateral trade flows. The primary research question (what impact do exchange rate fluctuations exert on U.S. bilateral trade flows with Canada, Mexico, and Brazil, and which models best capture those dynamics) receives an affirmative empirical answer, situated within the theoretical predictions of the exchange rate transmission literature and the comparative forecasting evidence in the field. The discussion moves from model comparison to feature-level interpretability, country and sectoral heterogeneity, methodological contributions, and limitations.

## 5.1 Machine learning gains over ARIMA

        Random Forest and LightGBM outperformed ARIMA in 75% and 83% of series, respectively, as confirmed by the Diebold-Mariano test. This result is consistent with the structural properties of non-parametric models in settings where trade dynamics are non-linear. ARIMA restricts the relationship between past and future values to a fixed-coefficient combination of lagged terms and disturbances. Tree-based ensembles, by contrast, approximate non-linear interactions among the 73 features without explicit parametric assumptions, capturing joint effects of exchange rates, energy prices and macroeconomic variables.

        Crude oil prices (WTI) ranked as the most influential feature in Random Forest, alongside exchange rate and industrial production variables, pointing to an interaction that ARIMA's fixed-coefficient structure cannot capture. Energy prices and currency dynamics jointly affect commodity-intensive trade corridors through non-linear channels that ARIMA's additive structure cannot model. Tree splits capture these interactions implicitly, exploiting information that ARIMA cannot access by construction.

        High-technology series showed the largest gap (0.91 pp for Random Forest), followed by manufactured goods (0.46 pp). These categories show heterogeneous demand, sensitivity to global value chains and exposure to multiple macroeconomic factors, conditions under which non-parametric models hold a comparative advantage (GOPINATH, M. et al., 2021). Commodities still favored ML but showed the smallest divergence, consistent with the linear pass-through of international prices into traded volumes.

## 5.2 When ARIMA outperforms: eight series and the role of structure

        ARIMA outperformed both ML models in 8 of 24 series, tempering any sweeping claim of methodological superiority. The series in which ARIMA prevailed show a consistent pattern: lower volatility, near-stationary behavior after differencing and no structural breaks within the test window. Mexican total exports (MAPE 0.40% under ARIMA) exemplify this profile, a series whose dynamics are well captured by a parsimonious linear specification.

        This comparison also involves a structural asymmetry: ARIMA's walk-forward one-step procedure favors shorter horizons relative to the 36-step direct forecast used by RF and LightGBM, examined in detail in Section 5.3.

        The Ljung-Box flag on two Canadian series (exports of commodities, total imports) merits separate consideration. Canadian total imports showed ARIMA errors substantially above the model's average; Canadian exports of commodities, by contrast, saw ARIMA outperform RF despite the flag, illustrating that misspecification and performance degradation are not mechanically linked. Excluding both series, the average gap between ARIMA and Random Forest is 0.51 percentage points (consistent with Section 4), confirming that the ML advantage is not an artifact of ARIMA misspecification. A properly specified linear model remains a competitive baseline for well-behaved series.

## 5.3 The Naïve baseline and information asymmetry

        A lower MAPE of 0.32% does not indicate that the Naïve model forecasts better. The protocol asymmetry described in Section 4.2, not a methodological flaw, accounts for the apparent baseline advantage.

        As an upper-bound informational reference rather than a competing forecasting strategy, the Naïve baseline cannot substitute for multi-step tools. Trade strategy and macroeconomic planning typically require multi-month lead times incompatible with a Naïve approach that demands the previous observation. Random Forest and LightGBM, though penalized in this benchmark, generate forecasts directly applicable to such planning horizons.

        Paired statistical tests, such as the Diebold-Mariano employed here, are therefore necessary when comparing models with structurally different prediction protocols.

## 5.4 Exchange rate as a relevant predictor

        Exchange rate fluctuations exert a measurable influence on bilateral trade flows. The SHAP analysis offers consistent evidence: in both Random Forest and LightGBM, exchange rate variables, particularly the USD/CAD percentage change, appeared consistently among the top-ranked predictors, alongside the WTI crude oil price and country-specific industrial production indices.

        Random Forest gave greater weight to moving-average representations of exchange rates, with six-month windows prominent, capturing medium-term trend persistence; LightGBM placed higher weight on percentage-change features, responding more sensitively to short-term directional shifts. Both models converged on exchange rate variables as top predictors, through different representations of the same signal.

        Bilateral exchange rate movements are not a peripheral input but a structurally relevant determinant of monthly trade flow dynamics, a result confirmed across two independent models through complementary interpretability tools. The finding confirms the variable selection rationale in Section 2.2: USD/XXX and REER as the primary FX signals of bilateral price competitiveness.

## 5.5 Country-level heterogeneity in exchange rate sensitivity

        Brazil, Canada and Mexico differ markedly in FX sensitivity, reflecting the structural and institutional features of each bilateral relationship. Brazil's elevated sensitivity is consistent with its structural profile as an emerging economy: less diversified export structures, higher commodity dependence and the absence of a preferential trade agreement with the United States produce greater FX exposure.

        Canada's low FX sensitivity stems from deep productive integration under USMCA. When exchange rates fluctuate, integrated production processes, particularly in automotive and industrial machinery, show price stickiness and contractual rigidities that dampen the immediate transmission of currency shocks to traded volumes (BERGIN; CORSETTI, 2020).

        Mexico's intermediate position is consistent with its hybrid integration profile: USMCA provides partial insulation analogous to Canada's, while the maquiladora-driven manufacturing structure and the commodity component retain residual sensitivity to exchange rate movements.

## 5.6 Sectoral patterns in exchange rate transmission

        Sectoral disaggregation revealed a consistent ordering of FX sensitivity, stable across the three country pairs: commodities at the higher end, manufactured goods intermediate and high-technology at the lower end.

        Commodity flows showed the highest sensitivity, driven by the predominance of USD-denominated pricing in markets such as crude oil, soybeans and iron ore. When the dollar appreciates or depreciates, traded volumes adjust through arbitrage and substitution effects with limited domestic insulation.

        High-technology products showed the lowest sensitivity, reflecting the differentiated nature of these goods, the prevalence of long-term contracts and integration within global value chains. Bergin and Corsetti (2020) document that relationship-specific investments and bilateral pricing arrangements partially insulate differentiated goods from short-run currency fluctuations. The same mechanism operated in U.S. trade with Canada, Mexico and Brazil. Manufactured goods occupied an intermediate position, combining standardized inputs (more exposed to exchange rate arbitrage) and differentiated components within integrated supply chains.

## 5.7 Methodological contributions

        Beyond the findings on exchange rate sensitivity, this study offers methodological contributions for Information Systems research applied to economic forecasting. A complete CRISP-DM pipeline was implemented across all six phases, applied to monthly macroeconomic forecasting with multiple country pairs and sectoral granularity (SHIMAOKA et al., 2024; MARTÍNEZ‐PLUMED et al., 2019). Heterogeneous data sources, reproducible feature engineering and systematic model evaluation were integrated across the pipeline. The novelty lies not in the methodology itself but in its application to bilateral macroeconomic trade forecasting, a domain where structured ML pipelines remain underrepresented in the SI literature.

        SHAP was applied to both Random Forest and LightGBM, enabling a comparative interpretability analysis beyond the practice of analyzing only the best-performing model. In IS, where models inform policy and business decisions, interpretability is a design requirement, not an optional complement to predictive accuracy. Random Forest weighted moving-average FX representations; LightGBM weighted percentage-change features. These complementary perspectives on the same signal confirm the finding through two independent inductive approaches (KHAN et al., 2024).

        Statistical comparisons relied on the Diebold-Mariano test with the Harvey et al. (1997) small-sample correction, providing rigorous pairwise results that do not depend on aggregate metric averages. It exposed the predictive hierarchy that the non-significant Friedman result alone would have obscured, a distinction with direct IS implications: deployment decisions must rest on statistically validated performance differences, not on aggregate metric rankings.

        Source code, datasets, trained models and evaluation outputs are publicly available in a versioned repository (https://github.com/franciscorigon/us-trade-forecasting, accessed June 2026). Reproducibility, often neglected in applied economic research, is a central methodological requirement in Information Systems and was treated here as a first-class deliverable.

        The pipeline directly serves exporting firms seeking to anticipate how currency movements affect trade volumes, financial institutions supporting hedging decisions, and government agencies grounding trade policy analysis in reproducible, sector-level evidence.

        Beyond its technical contributions, the work carries a social dimension that extends well beyond policy and corporate audiences. Exchange rate dynamics and trade flow data are typically confined to specialized reports, inaccessible to anyone without econometric training. The SHAP-based visualizations and structured CRISP-DM outputs change that. Academic communities, educators and public audiences can engage with the outputs directly, tracing how machine learning captures the economic signal of currency movements across sectors and partner countries, without requiring either econometric or computational expertise. Making complex economic and technological reasoning legible to non-specialist audiences is a contribution with educational reach that neither economics nor information systems alone could achieve.

## 5.8 Limitations

        Sectoral target series were estimated by combining annual HS2 sectoral proportions with monthly aggregate flows, an approximation that introduces systematic distortion in periods of structural change within a given year. The effect is most pronounced for commodities, where intra-annual price volatility and shipment lumpiness deviate from the annual proportional structure. Future work could replace this approximation with monthly HS2 disaggregated series once available.

        Three major macroeconomic disturbances characterize the test window (January 2022 to December 2024): the post-pandemic recovery, the energy price shock following the 2022 Russia–Ukraine conflict and the subsequent U.S. monetary tightening cycle. Although these disturbances are part of the realized empirical record, they may produce performance estimates not fully representative of more stable regimes. Additional out-of-sample windows would help characterize the regime sensitivity of the comparative results.

        RF and LightGBM used a direct 36-step forecast protocol to reflect the operational scenario of multi-month trade planning, where intermediate realized values are unavailable; this design, intentional and documented in Section 5.3, limits symmetric comparison with ARIMA's walk-forward procedure. Future work could add recursive multi-step retraining for a fully comparable evaluation.
