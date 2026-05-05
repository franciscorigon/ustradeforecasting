# TCC2 — Master Writing Plan

> Gerado pelo Claude Code | Atualizado: 04/05/2026
> Base: todos os documentos lidos em `/docs` + TCC1 completo + resultados de `05_evaluation.ipynb`
> **Este documento é a referência central para a redação. Não começar nenhuma seção sem consultá-lo.**

---

## 1. REGRAS INVIOLÁVEIS

1. **Citações do TCC1:** Cada citação reaproveitada deve ser transcrita **exatamente** como está no TCC1 — nem uma vírgula a mais.
2. **Idioma:** TCC2 em inglês acadêmico formal. Conversas com o Francisco em português.
3. **Páginas:** mínimo 25, máximo 30. Apêndices não contam.
4. **Distribuição:** ~50% Resultados + Discussão; ~50% Referencial + Metodologia.
5. **Estilo:** formal, sem contrações, sem "I" isolado — usar "this study", "we", ou voz passiva.
6. **Curso:** Sistemas de Informação — o TCC é de SI, NÃO de Economia. A parte econômica é contexto e motivação; o núcleo do trabalho é tecnológico: CRISP-DM como metodologia de DS, ML (RF, LightGBM), SHAP para interpretabilidade, pipeline de dados, reprodutibilidade. Cada seção deve deixar isso evidente. Na Fundamentação Teórica, as seções de ML/DS têm pelo menos tanto peso quanto as de economia. Na Discussão, enfatizar contribuições metodológicas e tecnológicas, não apenas os achados econômicos.
6. **Plágio:** Toda ideia de outro autor deve ter citação. Ideias próprias originais não precisam.
7. **Figuras:** título abaixo; fonte abaixo do título. Numerar sequencialmente. Referenciar antes de aparecer.
8. **Tabelas:** título acima; fonte abaixo. Numerar sequencialmente. Referenciar antes de aparecer.
9. **ABNT:**
   - Indireta: `(SURNAME, year)` ou `Surname (year)` no meio da frase
   - Direta curta (≤3 linhas): entre aspas no texto + `(SURNAME, year, p. X)`
   - Direta longa (>3 linhas): parágrafo separado, recuo 4 cm, fonte 10, sem aspas
   - Dois autores: `SURNAME; SURNAME, year`
   - Três ou mais: `SURNAME et al., year`
10. **Entrevistas:** ainda NÃO realizadas mas PLANEJADAS para antes de 31/05. Não mencionar na Metodologia como já executadas. Quando realizadas, os insights vão para a Discussão (validação qualitativa dos resultados). Deixar espaço na Discussão §7.9 para isso.
11. **CRISP-DM completo (§5 Metodologia):** Todas as 6 fases do CRISP-DM devem ser mencionadas explicitamente na §5. Modelagem e Avaliação recebem mais profundidade; Business Understanding é o mais curto; Deployment é 1 parágrafo. Abrir a §5 com: *"This study adopted CRISP-DM as its guiding methodological framework (SHIMAOKA et al., 2024; MARTÍNEZ-PLUMED et al., 2019), a technology-independent process model structured into six iterative phases..."*

---

## 2. ESTRUTURA GERAL E ESTIMATIVA DE PÁGINAS

| # | Seção | Tipo | Páginas Est. |
|---|-------|------|-------------|
| — | Resumo (PT + EN) | Novo | 1 |
| 1 | Introdução | Adapt. TCC1 + novo | 2–3 |
| 2 | Objetivos (geral + específicos) | Reuso TCC1 (passado) | 0.5 |
| 3 | Justificativa | Reuso TCC1 (passado) | 1 |
| 4 | Fundamentação Teórica | Reuso TCC1 (condensado) | 5–6 |
| 5 | Metodologia | Adapt. TCC1 (passado) | 4–5 |
| 6 | Resultados | **NOVO** | 6–7 |
| 7 | Discussão | **NOVO** | 4–5 |
| 8 | Conclusão | **NOVO** | 1–2 |
| — | Referências | Reuso + novos | 2–3 |
| — | **TOTAL** | | **26–33** |

> **Nota:** Apêndices (glossário, gráficos EDA, code snippets) vão DEPOIS das referências e NÃO contam no limite.

---

## 3. RESULTADOS COMPLETOS (dados de `05_evaluation.ipynb`)

> Estes dados são a base de toda a seção de Resultados e Discussão.

### 3.1 Métricas de performance (MAPE médio — 24 séries)

| Modelo | MAPE Médio | Wins (séries em que vence) |
|--------|-----------|--------------------------|
| Naïve baseline | **0.32%** | — (informação assimétrica) |
| Random Forest | **0.9381%** | **9/24** |
| LightGBM | **1.0276%** | 7/24 |
| ARIMA | **1.4143%** | 8/24 |

**Por país (MAPE médio):**
| País | ARIMA | RF | LightGBM |
|------|-------|----|----------|
| CAN | 1.4781% | **0.8549%** | 0.8362% |
| MEX | 1.2042% | **0.9312%** | 1.2221% |
| BRA | 1.5606% | **1.0282%** | 1.0247% |

**Por setor (MAPE médio):**
| Setor | ARIMA | RF | LightGBM |
|-------|-------|----|----------|
| commodities | 1.0768% | **0.9848%** | 1.1187% |
| manufactured_goods | 1.3989% | **0.9437%** | 1.0559% |
| high-tech | 1.8006% | **0.8904%** | 0.9404% |
| total | 1.3811% | **0.9334%** | 0.9956% |

> **Correção importante:** o `tcc2-central-sync.md` de 30/04 registrou wins como "ARIMA 9, LightGBM 8, RF 7" — eram dados preliminares. Os dados finais do CSV são: **RF 9, ARIMA 8, LightGBM 7**. Usar sempre os dados deste plano.

**Nota crítica sobre o Naïve:** O baseline ingênuo (log[t] = log[t-1]) usa o valor real de t-1 a cada passo do período de teste. Os modelos ML fazem previsão direta de 36 passos sem acesso ao valor real intermediário. É uma vantagem estrutural do Naïve, não uma superioridade metodológica. Isso DEVE ser explicado na discussão.

### 3.2 Testes estatísticos

| Teste | Resultado | Interpretação |
|-------|-----------|---------------|
| Friedman | p = 0.417 | Não significativo — baixo poder com n=24 |
| Diebold-Mariano: RF vs ARIMA | RF superior em **75%** das séries | Significativo |
| Diebold-Mariano: LightGBM vs ARIMA | LightGBM superior em **83%** das séries | Significativo |
| Diebold-Mariano: RF vs LightGBM | RF superior em **83%** das séries | Significativo |
| Ljung-Box (ARIMA resíduos) | **22/24** bem especificados | 2 problemáticos: CAN exports_commodities (p=0.0006), CAN imports_total (p=0.0146) |

**Hierarquia DM:** RF > LightGBM > ARIMA (nas comparações pareadas com DM)

### 3.3 Feature Importance e SHAP

| Modelo | Top feature FX |
|--------|---------------|
| Random Forest | `WTI_oil` #1; `FX_USD_CAD_pct` identificado |
| LightGBM | `FX_USD_CAD_pct` #3 |

- Câmbio aparece nos dois modelos como variável relevante.
- RF tende a capturar efeitos via médias móveis (MA6); LightGBM via variações percentuais (pct).
- Figuras geradas: `shap_rf_summary.png`, `shap_lgbm_summary.png`, `shap_comparison_rf_lgbm.png`

### 3.4 Gap ARIMA vs ML

- Diferença média: **+0.54 pp** a favor do ML (ARIMA piora em 0.54 pp em média)
- **8/24 séries o ARIMA superou o ML** — resultado honesto, deve ser documentado
- Figura: `arima_ml_gap.png`

### 3.5 Sensibilidade cambial (FX Sensitivity Heatmap)

- Métrica: média |SHAP| das features FX+REER, calculada para RF e LightGBM; heatmap com média das duas.
- **BRA** = mais sensível a câmbio (especialmente exports_commodities e imports_total)
- **CAN** = menos sensível (produção industrial domina; integração USMCA estabiliza fluxos)
- **MEX** = sensibilidade intermediária
- Figura: `fx_sensitivity_heatmap.png`

### 3.6 Arquivos de figuras gerados (em `reports/figures/`)

```
shap_rf_summary.png
shap_lgbm_summary.png
shap_comparison_rf_lgbm.png
shap_rf_fx_dependence.png
shap_lgbm_fx_dependence.png
fx_sensitivity_heatmap.png
arima_ml_gap.png
feature_importance_rf.png
feature_importance_lgbm.png
ljung_box_acf.png
naive_comparison.png
```

---

## 4. SEÇÃO POR SEÇÃO — GUIA DE REDAÇÃO

### 4.1 RESUMO (Abstract)

- **Tamanho:** 150–250 palavras (PT-BR) + tradução EN
- **Conteúdo obrigatório:** objetivo, metodologia, principais resultados, conclusão
- **Palavras-chave:** 3–5. Sugestão: exchange rate forecasting; machine learning; ARIMA; trade flows; SHAP
- **Escrever por último** (após todas as seções)

---

### 4.2 INTRODUÇÃO

**Reaproveitamento do TCC1 — Parágrafo 1 (adaptar de futuro para passado):**
> "The purpose of this study is to investigate the impact of exchange rate volatility on bilateral trade flows, with particular emphasis on the trading relationship between the U.S. and its main partners, Canada, Mexico and Brazil..." `(USITC, 2025)`

**Adaptações necessárias:**
- Mudar tempo verbal: "will investigate" → "investigated", "will use" → "used"
- Adicionar ao final: "This paper presents the results obtained..."
- Não mencionar entrevistas

**Citações que vêm do TCC1 (preservar exatamente):**
- `(USITC, 2025)` — parceiros comerciais EUA
- `(USTR, 2020)` — Brasil sem acordo USMCA
- `(AUBOIN; RUTA, 2011)` — câmbio e comércio
- `(SHEN et al., 2021)` — dados mensais
- `(SONG; CHEN, 2024)` — RF e LightGBM vs ARIMA
- `(KALLIANIOTIS, 2022)` — variáveis macroeconômicas
- `(BATARSEH et al., 2020)` — eventos extremos e modelos preditivos
- `(MARTÍNEZ‐PLUMED et al., 2019)` — CRISP-DM
- `(RAHMAN et al., 2025)` — volatilidade cambial
- `(ABIR et al., 2024)` — ML para câmbio

**Conteúdo novo a adicionar na introdução:**
- Uma frase resumindo o que foi alcançado: modelos treinados, comparação feita, conclusão
- Estrutura do artigo: "Section 2 presents the theoretical framework... Section 6 concludes."

---

### 4.3 OBJETIVOS

**Objetivo Geral (do TCC1 — transcrever em passado):**
> "This study assessed the relative performance of Random Forest and LightGBM models with respect to the ARIMA model in forecasting how continuously changing exchange rates affect bilateral trade flows between the United States, Canada, Mexico, and Brazil, with granular level insights about sectoral movement and macroeconomic effects."

**Objetivos Específicos (do TCC1 — transcrever em passado):**
a) To identify which sectors were most vulnerable to exchange rate changes;
b) To determine the impact of these swings across different time horizons;
c) To evaluate the predictive power of each model;
d) To examine the effect of macroeconomic factors on the volatility of trade flows.

---

### 4.4 JUSTIFICATIVA

**Do TCC1 (adaptar de futuro para passado + resultado confirmado):**
- Câmbio como fator-chave de competitividade internacional `(RAHMAN et al., 2025)`
- Comparação ML vs métodos tradicionais `(ABIR et al., 2024)`
- Relevância metodológica: CRISP-DM + reprodutibilidade `(Vranješ; Niggemann, 2024, p. 1–2)`

**Novo parágrafo:** confirmar que a relevância foi comprovada pelos resultados obtidos.

---

### 4.5 FUNDAMENTAÇÃO TEÓRICA

**Estrutura (reuse do TCC1, condensar para ~5-6 páginas):**

```
2.1 Economic key concepts          → reuse TCC1 §2.1 (nominal exchange rate, REER, HS, imports/exports)
2.2 Exchange rate variables        → reuse TCC1 §2.2 (USD/XXX + REER justification)
2.3 Trade flows and sectoral sens. → reuse TCC1 §2.3 (commodities vs. manufactured vs. high-tech)
2.4 Macroeconomic control variables → reuse TCC1 §2.4
2.5 Time series forecasting        → reuse TCC1 §2.8 (ARIMA, stationarity)
2.6 Machine learning in forecasting → reuse TCC1 §2.9 (RF, LightGBM, SHAP)
```

**Seções a OMITIR (eram específicas da proposta):**
- §2.5 Historical events (GFC, COVID) — mencionar brevemente na metodologia
- §2.6 Institutional events (USMCA) — mencionar brevemente na introdução
- §2.7 Other determinants — incorporar nos conceitos econômicos
- §2.10 Technologies and tools — mover para apêndice ou omitir

**Citações-chave a preservar exatamente:**
- `(KIM; LE, 2024)` — nominal exchange rate e competitividade
- `Kallianiotis (2022, p. 46)` — citação direta sobre desvalorização/exportações [LONG QUOTE FORMAT]
- `(WAHAB, 2024)` — REER
- `(MWANGE et al., 2022)` — REER e PPP
- `Mwange (2022, p. 71)` — citação direta longa sobre teorias de câmbio [LONG QUOTE FORMAT]
- `(GOPINATH et al., 2020)` — US dollar como moeda dominante
- `(SCHMIDT; STEINGRESS, 2019)` — HS system
- `(SELLAMI et al., 2024)` — granularidade HS
- `(NTHEBE; MOSIKARI, 2025)` — setores e câmbio
- `(GODA et al., 2024)` — heterogeneidade setorial
- `(OLIVEIRA et al., 2023)` — commodities e câmbio
- `(BERGIN; CORSETTI, 2020)` — bens diferenciados menos sensíveis
- `(SPRING-RAGAIN, 2024, p. 4)` — citação direta sobre USD/EUR [LONG QUOTE FORMAT]
- `(BAYOUMI et al., 2005)` — REER e política econômica
- `(SHIMAOKA et al., 2024)` — CRISP-DM atualizado
- `(SALMAN et al., 2024)` — Random Forest overview [paper disponível em /papers]
- `(MARTÍNEZ‐PLUMED et al., 2019)` — CRISP-DM 20 anos
- `(AYITEY et al., 2023)` — RMSE, MAE, MAPE
- `(SUDJIANTO; ZHANG, 2024)` — interpretabilidade
- `(GUO et al., 2024)` — SHAP
- `(ABOUZAID; BOUSSEDRA, 2025)` — opacidade de black boxes
- `(KHAN et al., 2024)` — interpretabilidade e policymakers
- `(VRANJEŠ; NIGGEMANN, 2024)` — experimentação ML

---

### 4.6 METODOLOGIA

**Estrutura (adaptar TCC1 §3.1–§3.6 para passado; omitir §3.6 deployment ou simplificar muito):**

```
5.1 Research design and CRISP-DM   → adaptar TCC1 §3.1 (business understanding)
5.2 Data sources and collection    → adaptar TCC1 §3.2 (data understanding)
5.3 Data preparation               → adaptar TCC1 §3.3 (data preparation)
5.4 Modeling                       → adaptar TCC1 §3.4 (ARIMA + RF + LightGBM)
5.5 Evaluation                     → adaptar TCC1 §3.5 (métricas + testes estatísticos)
5.6 Deployment (1 parágrafo)       → adaptar TCC1 §3.6: descrever pipeline teórico em passado + mencionar repositório público. Não requer código nem demonstração.
```

**Detalhes novos a adicionar (não estavam no TCC1 como realizados):**
> Fontes: ler `03_data_preparation.ipynb` para Data Preparation, `04_modeling.ipynb` para Modeling.

**De `03_data_preparation.ipynb`:**
- Alinhamento de frequências: diário → média mensal; trimestral → forward-fill
- Dataset final: 180 obs. (2010–2024), 97 colunas (features + targets)
- Variável-alvo setorial: proporção anual HS2 × total mensal (ver §4.2 do notebook)
- Limpeza: forward-fill para exchangerates; IQR para outliers nas features
- Dummies de crise: GFC (2008–2009) e COVID-19 (2020–2021)
- Feature engineering: lags 1, 3, 6, 12 meses; MAs 3, 6, 12 meses; variação percentual; features de calendário
- Log nas targets (RF/LightGBM); diff_log nas targets (ARIMA)
- Split: treino 2010–2021 (132 meses ML / 143 ARIMA), teste 2022–2024 (36 meses)

**De `04_modeling.ipynb`:**
- 24 séries-alvo: 3 países × 2 direções × 4 setores (commodities, manufactured goods, high-tech, total)
- 73 features de entrada (country-pair agnostic)
- ARIMA: walk-forward, reajustado a cada um dos 36 meses de teste
- RF: previsão direta 36-step, treino 132 linhas × 73 features, Optuna 30 trials
- LightGBM: mesma estratégia que RF, Optuna 30 trials
- Naïve baseline: log[t] = log[t-1]

**De `05_evaluation.ipynb`:**
- Ljung-Box nos resíduos in-sample do ARIMA
- Friedman; Wilcoxon+Bonferroni; Diebold-Mariano (Harvey et al. 1997)
- SHAP via `shap.TreeExplainer` para RF e LightGBM
- FX sensitivity heatmap (média RF + LightGBM |SHAP| das features FX+REER)

**Citações da metodologia a preservar:**
- `(KHAN et al., 2024)` — split cronológico
- `(MORTEZANEJAD; WANG, 2025)` — validação temporal
- `(WANG; XIE, 2024)` — cuidado com overfitting
- `(RASCHKA et al., 2020; LING; WANG, 2024)` — hyperparameter optimization
- `(SATRI et al., 2023)` — evaluation no CRISP-DM
- `(ABIR et al., 2024)` — TS-CV e backtesting

**Texto guia por fase CRISP-DM (REGRA 11 — todas obrigatórias):**

**§5.1 — Business Understanding** (1–2 parágrafos | adaptar TCC1 §3.1 para passado)
- Abrir a seção com a adoção do CRISP-DM como framework: citar `(SHIMAOKA et al., 2024)` e `(MARTÍNEZ-PLUMED et al., 2019)` — ambos já estão no TCC1
- Descrever o objetivo de negócio (impacto cambial no comércio) e o objetivo de Data Mining (comparar os três modelos em 24 séries)
- Mencionar que as fases são iterativas, não estritamente sequenciais

**§5.2 — Data Understanding** (1 parágrafo | adaptar TCC1 §3.2 para passado)
- Fontes: FRED (câmbio, macro), UN Comtrade (fluxos HS2), World Bank
- Período: 2000–2024 coletado; 2010–2024 modelado (180 observações mensais)
- 24 séries-alvo: 3 países × 2 direções (exports/imports) × 4 setores
- EDA realizada — detalhes no Apêndice B; mencionar brevemente aqui

**§5.3 — Data Preparation** (2–3 parágrafos | usar `03_data_preparation.ipynb`)
- Alinhamento de frequências: diário → mensal (média), trimestral → forward-fill
- Transformações: log nas targets para RF/LightGBM; log + diferenciação para ARIMA; ADF test
- Outliers via IQR; forward-fill para câmbio diário
- Feature engineering: lags 1,3,6,12 meses; MAs 3,6,12; variação percentual; dummies GFC + COVID-19
- Dataset final: 180 obs. × 97 colunas (73 features + 24 targets)
- Split: treino 2010–2021 (132 meses ML / 143 ARIMA), teste 2022–2024 (36 meses)

**§5.4 — Modeling** (maior profundidade | usar `04_modeling.ipynb`)
- 24 séries treinadas independentemente; 73 features (country-pair agnostic)
- ARIMA: walk-forward validation, reajuste mensal, identificação p,d,q via ACF/PACF
- RF: previsão direta 36-step, bootstrap sampling, Optuna 30 trials
- LightGBM: mesma estratégia RF, leaf-wise split
- Naïve baseline: log[t] = log[t-1]
- Total: 72 modelos (24 séries × 3 algoritmos) + 24 Naïve

**§5.5 — Evaluation** (segunda maior profundidade | usar `05_evaluation.ipynb`)
- Métricas: RMSE, MAE, MAPE — citar `(AYITEY et al., 2023)` do TCC1
- Ljung-Box nos resíduos in-sample do ARIMA
- Friedman test (comparação múltipla não-paramétrica)
- Diebold-Mariano com correção Harvey et al. (1997) — citar Harvey et al.
- SHAP via `shap.TreeExplainer` — citar `(GUO et al., 2024)` e `(SUDJIANTO; ZHANG, 2024)` do TCC1
- FX sensitivity heatmap: média |SHAP| das features FX+REER por país/setor

**§5.6 — Deployment** (1 parágrafo | adaptar TCC1 §3.6 para passado)
- Descrever o repositório Git público com notebooks reproduzíveis e pipeline de dados
- Deployment no CRISP-DM = empacotamento dos resultados para uso — `(SHIMAOKA et al., 2024)` do TCC1
- NÃO requer demonstração de código ou Git na defesa — apenas descrição textual

---

### 4.7 RESULTADOS

**Estrutura sugerida:**

```
6.1 Descriptive statistics and data overview
    - Período: 2010–2024 mensal, 180 observações, 97 colunas
    - 24 séries-alvo: 3 países × 2 direções × 4 setores
    - Split: treino 2010–2021, teste 2022–2024 (36 meses)
    - Mencionar apêndice para EDA e glossário

6.2 Naïve baseline and information asymmetry
    - MAPE 0.32% — vence todos os modelos
    - Explicação da assimetria: Naïve usa valor real t-1; ML faz 36-step direto
    - Importância de comparar modelos entre si (DM test) em vez de apenas com Naïve

6.3 Model performance comparison (MAPE)
    - Tabela: MAPE médio por modelo (RF 0.94%, LightGBM 1.03%, ARIMA 1.41%)
    - Tabela ou figura: MAPE por série (scatter ou boxplot)
    - Wins por modelo: ARIMA 9/24, LightGBM 8/24, RF 7/24

6.4 Statistical significance tests
    - Friedman: p=0.417 (não sig — baixo poder com n=24, mencionar limitação)
    - Diebold-Mariano: RF > LightGBM (83%), RF > ARIMA (75%), LightGBM > ARIMA (83%)
    - Ljung-Box: 22/24 ARIMA ok; 2 problemáticos (CAN exports_commodities, CAN imports_total)

6.5 ARIMA vs ML performance gap
    - Gap médio +0.54 pp a favor do ML
    - 8/24 séries ARIMA supera ML — caracterizar quais séries (ex: séries mais estacionárias?)
    - Figura: arima_ml_gap.png

6.6 Feature importance and SHAP analysis
    - RF: feature importance top-10 + SHAP summary plot
    - LightGBM: feature importance + SHAP summary plot
    - Comparativo RF vs LightGBM SHAP (shap_comparison_rf_lgbm.png)
    - FX/REER features aparecem nos dois modelos
    - Dependence plots: FX × SHAP values

6.7 Exchange rate sensitivity by country and sector
    - Heatmap: fx_sensitivity_heatmap.png
    - BRA mais sensível; CAN menos sensível; MEX intermediário
    - Padrão por setor: commodities mais sensíveis que manufactured goods
```

**Formato das tabelas principais:**

**Tabela 1 — Model Performance (MAPE %)**
| Country | Direction | Sector | ARIMA | RF | LightGBM | Best |
|---------|-----------|--------|-------|----|----------|------|
| (dados do results CSV) |

**Tabela 2 — Diebold-Mariano Results**
| Comparison | Proportion of series (DM sig.) |
|------------|-------------------------------|
| RF vs ARIMA | 75% |
| LightGBM vs ARIMA | 83% |
| RF vs LightGBM | 83% (RF wins) |

---

### 4.8 DISCUSSÃO

**Estrutura sugerida:**

```
7.1 ML models outperform ARIMA — why it makes sense
    - Séries não-lineares, impacto cambial não-linear
    - RF captura interações (WTI × câmbio)
    - Citar: SONG; CHEN (2024), GOPINATH et al. (2020)

7.2 ARIMA wins in 8/24 series — when and why
    - Estacionaridade relativa das séries
    - ARIMA beneficia de estrutura linear
    - Limitação metodológica: ARIMA 1-step vs ML 36-step (vantagem estrutural ARIMA)
    - Discussão honesta

7.3 Naïve baseline and information asymmetry
    - Por que Naïve vence: acesso ao valor real t-1 (não é cheating, mas é assimetria)
    - Em contexto operacional real: Naïve não é uma alternativa prática (apenas para o próximo mês)
    - ML pré-prevê 36 meses à frente — mais útil para planejamento

7.4 Exchange rate as a relevant feature — confirming the hypothesis
    - SHAP confirma câmbio como feature importante nos dois modelos
    - RF via MA6 (efeito suavizado); LightGBM via pct (efeito imediato)
    - Convergência entre modelos reforça a evidência
    - Citar: KIM; LE (2024), GODA et al. (2024)

7.5 Country-level heterogeneity
    - BRA: mais sensível → consistente com literatura sobre economias emergentes (GODA et al., 2024)
    - CAN: menos sensível → integração USMCA e produção industrial dominam
    - MEX: intermediário → efeito maquiladora e integração parcial
    - Citar: BERGIN; CORSETTI (2020) (bens diferenciados menos sensíveis)

7.6 Sectoral analysis
    - Commodities mais sensíveis: preços internacionalizados em USD
    - High-tech menos sensível: cadeias de valor globais, contratos de longo prazo
    - Consistente com: OLIVEIRA et al. (2023), GODA et al. (2024)

7.7 Methodological contributions
    - CRISP-DM end-to-end com 72 modelos
    - SHAP em RF + LightGBM: comparação bilateral de importância de features
    - DM test: padrão da literatura de forecasting (Harvey et al. 1997)
    - Reprodutibilidade: repositório público, notebooks executáveis

7.9 Expert validation (entrevistas — inserir quando realizadas)
    - Profissionais de comércio exterior: os resultados fazem sentido prático?
    - Profissionais de TI/ML: a abordagem metodológica é adequada?
    - Inserir como validação qualitativa dos achados quantitativos

7.8 Limitations
    - Sectoral data approximation via proporção anual × total mensal (distorção para Commodities)
    - n=24 séries: baixo poder para Friedman
    - Período de teste: 2022–2024 inclui choques pós-COVID e guerra Rússia-Ucrânia
    - Naïve assimetria (discutida acima)
    - ML 36-step direto: sem feedback — limitação prática para horizontes longos
    - Entrevistas com especialistas não realizadas (planejadas no TCC1, não executadas)
```

---

### 4.9 CONCLUSÃO

```
- Reafirmar objetivo geral e responder à pergunta de pesquisa
- RF e LightGBM superam ARIMA na maioria das séries (DM: 75-83%)
- Câmbio é feature relevante — confirmado por SHAP em ambos os modelos
- BRA mais sensível que CAN e MEX
- Commodities mais sensíveis que manufactured goods e high-tech
- Limitações principais (brevemente)
- Trabalhos futuros:
  a) Ampliar para período 2000–2009 (sugestão da orientadora)
  b) Incorporar modelos deep learning (LSTM, Transformer)
  c) Entrevistas com especialistas para validação qualitativa
  d) Atualização automática (pipeline operacional com dados mensais)
  e) Análise de assimetria depreciação/apreciação
```

---

## 5. CITAÇÕES NOVAS NECESSÁRIAS (não estavam no TCC1)

| Citação | Para que serve |
|---------|---------------|
| Harvey et al. (1997) | Diebold-Mariano modificado — teste padrão da literatura |
| Friedman (1940) | Teste de Friedman para comparação múltipla |
| Diebold; Mariano (1995) | Teste DM original |
| SHAP original: Lundberg; Lee (2017) | SHAP values |
| Optuna: Akiba et al. (2019) | Hyperparameter optimization com Optuna |

> **Nota:** Se não houver esses papers no `/docs`, as citações devem ser adicionadas à bibliografia com o formato ABNT padrão.

---

## 6. APÊNDICES (não contam no limite de páginas)

| Apêndice | Conteúdo | Origem |
|----------|----------|--------|
| A | Glossário de variáveis | `data/codebook/glossary.md` |
| B | Gráficos EDA | `reports/figures/` (01_* a 13_*) |
| C | Catálogo de variáveis (97 colunas) | `data/codebook/variable-catalogue.md` |
| D | Performance detalhada (72 modelos) | `results/metrics/metrics_all.csv` |

---

## 7. ORDEM DE REDAÇÃO RECOMENDADA

1. **Resultados** — seção mais nova, dados frescos, menos risco de erro
2. **Discussão** — depende dos Resultados
3. **Metodologia** — adaptar TCC1 para passado + adicionar detalhes reais
4. **Fundamentação Teórica** — condensar TCC1; remover seções de proposta
5. **Introdução** — adaptar TCC1 + adicionar resultado
6. **Objetivos e Justificativa** — transcrever TCC1 em passado
7. **Conclusão** — escrever por último (exceto Resumo)
8. **Resumo** — escrever por último de tudo

---

## 8. ARQUIVOS DE REFERÊNCIA

| Documento | Local | Status |
|-----------|-------|--------|
| TCC1 completo | `docs/tcc1/tcc1_extracted.txt` | ✅ Lido |
| Modelo LaTeX UNISINOS | `docs/tcc1/Modelo_Latex_TCC_II.md` | ✅ Lido |
| Ata reunião orientadora | `docs/meetings/ata_2026-03-31.md` | ✅ Lido |
| Plagio cartilha UNISINOS | `docs/Plagio.md` | ✅ Lido |
| Programa da atividade | `docs/Programa_da_Atividade_Academica.md` | ✅ Lido |
| Caracterização TCC II | `docs/Caracterização da Atividade Acadêmica.pdf` | ✅ Lido |
| Resolução UNISINOS | `docs/Resolução.pdf` | ✅ Lido |
| Paper: Gopinath et al. 2020 (ML trade) | `docs/references/papers/GOPINATH...pdf` | ✅ Lido |
| Paper: Salman et al. 2024 (RF overview) | `docs/references/papers/SALMAN...pdf` | ✅ Lido |
| Paper: Shimaoka et al. 2023 (CRISP-DM) | `docs/references/papers/SHIMAOKA...pdf` | ✅ Lido |
| Paper: Abir et al. 2024 (BRICS ML) | `docs/references/papers/ABIR...pdf` | ✅ Lido |
| Paper: Rahman et al. 2025 (volatility) | `docs/references/papers/RAHMAN...pdf` | ✅ Lido |
| `03_data_preparation.ipynb` (41 células) | `notebooks/03_data_preparation.ipynb` | ✅ — usar para §Metodologia: Data Preparation |
| `04_modeling.ipynb` (25 células) | `notebooks/04_modeling.ipynb` | ✅ — usar para §Metodologia: Modeling |
| `05_evaluation.ipynb` (36 células) | `notebooks/05_evaluation.ipynb` | ✅ — usar para §Resultados e §Discussão |
| Métricas CSV | `results/forecasts/metrics_all.csv` | ✅ Existente |

> **Nota sobre papers:** Os papers foram lidos (Poppler instalado em 05/05/2026). São referência de **estilo de escrita** apenas — as citações já estão no TCC1 e devem ser reaproveitadas diretamente de lá. Não adicionar novas citações a partir destes PDFs.

---

## 9. CHECKLIST DE INÍCIO DE CADA SEÇÃO

- [ ] Li a seção correspondente no TCC1?
- [ ] Identifiquei quais citações preservar exatamente?
- [ ] Identifiquei o que é novo (resultados) vs. reaproveitado (teoria)?
- [ ] Mudei os verbos para passado nas seções que descrevem o que foi feito?
- [ ] Referenciei as figuras/tabelas antes de elas aparecerem?
- [ ] Verifiquei o formato ABNT das citações?

---

## 10. ESTILO DE ESCRITA — REFERÊNCIA PRIMÁRIA: TCC1

> **O estilo de escrita do TCC2 deve seguir o TCC1 (`docs/tcc1/TCC I - Francisco G. Rigon.pdf`), escrito pelo próprio Francisco.**
> Os papers lidos são apenas referência de domínio/contexto. NÃO usar os papers como modelo de escrita.

### 10.1 Regra de ouro de estilo

**Antes de escrever qualquer parágrafo:** ler o trecho equivalente no TCC1 e manter:
- O mesmo nível de formalidade e vocabulário
- A mesma forma de introduzir tabelas e figuras
- A mesma forma de citar autores (inline vs. parentética)
- A mesma forma de estruturar parágrafos (topic sentence → argumento → evidência)

**O que mudar em relação ao TCC1:**
- Verbos: futuro → passado ("will compare" → "compared", "will use" → "used")
- Adicionar parágrafos com os resultados obtidos (inexistentes no TCC1)
- Condensar seções de teoria (TCC2 tem menos páginas de fundamentação que TCC1)

### 10.2 O que os papers confirmam sobre domínio (não sobre estilo)

| Paper | O que confirma sobre o domínio do TCC2 |
|-------|----------------------------------------|
| SHIMAOKA et al. (2024) | CRISP-DM como framework iterativo e tecnologia-independente |
| GOPINATH et al. (2021) | ML aplicado a forecasting de comércio bilateral é metodologia legítima |
| SALMAN et al. (2024) | RF via bootstrap aggregation e OOB são conceitos consolidados |
| ABIR et al. (2024) | Log + ADF + IQR + Optuna são práticas padrão em forecasting de câmbio |
| RAHMAN et al. (2025) | SHAP para interpretabilidade em modelos financeiros é prática aceita |

### 10.3 Resolução UNISINOS n.º 01/2013 — pontos relevantes para a defesa

- **Art. 4, VII:** apresentação escrita em ABNT é obrigatória
- **Art. 17:** banca examinadora com até 3 membros (presencial)
- **Art. 20:** conceitos de avaliação: REPROVADO / APROVADO / APROVADO PLENAMENTE / APROVADO COM DISTINÇÃO
- **Art. 22:** homologação pode ser condicionada a correções solicitadas pelos avaliadores

---

*Última atualização: 05/05/2026*
