# INTERVIEW SCRIPT

        To validate the results and reduce confirmation bias, semi-structured interviews were conducted with professionals from two profiles: foreign trade and international business practitioners, and technology professionals. Questions were designed to be open-ended and non-leading, allowing participants to draw on their own experience rather than respond to the study's framing. The script covers four themes: the practical applicability of multi-month trade forecasts, the relevance of the macroeconomic variables used, the interpretability and adoption of ML models, and the operational conditions under which each approach performs best.

## Questionnaire for foreign trade / international business professionals

        This study found that Random Forest and LightGBM outperformed ARIMA in 75% to 83% of the tested bilateral trade series, but ARIMA remained competitive (and occasionally superior) in structurally stable, low-volatility series. Under what conditions would you rely on a traditional statistical forecast rather than a machine learning model for actual trade planning or policy decisions?

        Brazil registered the highest exchange rate sensitivity among the three trading partners, while Canada registered the lowest, a difference attributed partly to USMCA integration dampening currency shock transmission. Does this pattern match your experience of how exchange rate movements affect trade operations differently across these three markets?

        Commodity trade flows registered the highest exchange rate sensitivity in this study; high-technology products registered the lowest, consistent with USD-denominated pricing in commodity markets and long-term contracts in differentiated goods. How does your organization manage currency risk differently across these product categories?

        A direct 36-month-ahead forecast without intermediate realized values differs fundamentally from month-to-month projection. In your field, for what types of decisions would this kind of long-horizon trade forecast be most valuable?

        The SHAP analysis ranked WTI crude oil prices and bilateral exchange rates as the most influential predictors, above GDP and inflation in terms of model weight. Does this variable hierarchy reflect how trade risk is actually assessed in your field, or are there signals you consider more relevant that the model did not capture?

## Questionnaire for technology professionals

        This study compared ARIMA using a one-step walk-forward protocol against Random Forest and LightGBM using a direct 36-step forecast, an asymmetry in information availability that complicates symmetric evaluation. In your experience, how do you determine whether a comparison between models with different prediction protocols is methodologically fair?

        The SHAP analysis revealed that Random Forest and LightGBM weighted the same exchange rate signal differently: Random Forest favored six-month moving averages while LightGBM responded more to monthly percentage changes. How do you communicate this type of model-specific explanation to non-technical stakeholders without losing the technical nuance?

        Working with 73 features, including lagged trade values, exchange rate levels and changes, moving averages across multiple windows, and macroeconomic indicators for three country pairs, introduces significant complexity in a time series context. What validation strategies do you apply to prevent data leakage or overfitting in settings of this scale?

        Rather than relying on aggregate error metrics alone, this study used the Diebold-Mariano test with a small-sample correction to compare model forecasts, finding statistically significant ML superiority in 75% to 83% of the tested series. In your experience, what level of statistical evidence is typically required before an organization replaces an established forecasting method with an ML alternative?

        The entire pipeline (data ingestion, preprocessing, 72 model fits, SHAP evaluation and versioned output) is documented in a public repository. What are the main engineering challenges in transitioning a research pipeline of this type into an automated production system with monthly retraining?
