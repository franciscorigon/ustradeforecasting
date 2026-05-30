# Prompt — Natural-Writing Pass TCC2 (usar com Claude Opus)

> Coloca este prompt na íntegra no início da sessão do Claude Code com Opus.
> Não resumir, não pular seções.

---

## CONTEXTO DO PROJETO

Estás a ajudar Francisco Giordano Rigon (franciscorigon@gmail.com) a fazer o passe final de natural-writing do TCC2 de Sistemas de Informação da UNISINOS antes da entrega à orientadora.

**Título:** Forecasting the Effects of Exchange Rate Fluctuations on the United States' Trade Flows with Canada, Mexico and Brazil Using Machine Learning Models  
**Orientadora:** Profª Dra. Josiane Brietzke Porto  
**Prazo de entrega:** 30/05/2026 (revisão) | 05/06/2026 (FINAL)  
**Defesa:** Junho 2026

**Pasta do projeto:** `C:\Projects\trade-forecasting\`

---

## O QUE É ESTE PASSE

O TCC2 foi escrito em partes ao longo de semanas, com mistura de texto verbatim do TCC1 (já aprovado), texto adaptado do TCC1, e texto totalmente novo. O objetivo é:

1. **Eliminar completamente o "AI voice"** — frases corporativas, construções formulaicas, voz passiva desnecessária
2. **Alinhar ao estilo de escrita do Francisco** — usando o TCC1 como referência de estilo
3. **Não tocar no que é verbatim do TCC1** — já foi aprovado, não precisa mudar

---

## ARQUIVOS A TRABALHAR

Os arquivos fonte são os `.md` na pasta `tcc/`:

| Arquivo | Conteúdo | Status |
|---------|----------|--------|
| `tcc/00_resumo_abstract.md` | Abstract | 100% novo |
| `tcc/01_introduction.md` | Introduction | ~65% TCC1 adaptado, 35% novo |
| `tcc/02_theoretical_framework.md` | Theoretical Framework | ~85% verbatim/quase-verbatim TCC1 |
| `tcc/03_methodology.md` | Methodology | ~55% TCC1, 45% novo |
| `tcc/04_results.md` | Results | 100% novo |
| `tcc/05_discussion.md` | Discussion | 100% novo |
| `tcc/06_conclusion.md` | Conclusion | 100% novo |

**Referência de estilo:** `docs/tcc1/TCC I - Francisco G. Rigon.pdf`  
**Skill natural-writing:** `C:\Projects\trade-forecasting\.claude\skills\natural-writing\SKILL.md` — LÊ ESTE ARQUIVO ANTES DE COMEÇAR.

---

## ANÁLISE TCC1 vs TCC2 — O QUE JÁ FOI FEITO

### O que é verbatim ou quase-verbatim do TCC1 (NÃO TOCAR):

**§2 Theoretical Framework:**
- §2.1 Nominal exchange rate: definição + citação Kallianiotis — **IDÊNTICO ao TCC1**
- §2.1 REER: definição + citação Wahab/Mwange — **IDÊNTICO ao TCC1**
- §2.2 parágrafos sobre IFE, IRP, balance of payments — **QUASE-IDÊNTICO** (só removeu o long quote Mwange e o Spring-Ragain quote)
- §2.2 parágrafo final "Therefore, the combined use..." — **IDÊNTICO**
- §2.3 abertura "The structure of bilateral trade flows..." — **IDÊNTICO**
- §2.3.1 abertura e parágrafos centrais — **IDÊNTICO**
- §2.3.2 parágrafos sobre commodities, Goda, Bergin & Corsetti — **IDÊNTICO**
- §2.4 GDP/inflation/exchange rate/crises paragraphs — **IDÊNTICO**
- §2.5 abertura "Time series analysis aims to model..." — **IDÊNTICO**
- §2.5.1 ARIMA paragraphs — **IDÊNTICO**
- §2.5.2 Stationarity tests — **IDÊNTICO**
- §2.6 abertura "Recent advances in ML..." — **IDÊNTICO**
- §2.6.1 Random Forest parágrafos incluindo citação Sellami — **IDÊNTICO**
- §2.6.2 LightGBM parágrafos — **IDÊNTICO**
- §2.6.3 Comparison paragraph — **IDÊNTICO**
- §2.6.4 SHAP/Hyperparameters paragraph — **IDÊNTICO**

**§3 Methodology:**
- §3.1 abertura "This is the first phase of CRISP-DM..." — **IDÊNTICO**
- §3.1 parágrafo "In this study, the Business Understanding phase..." — **IDÊNTICO**
- §3.3 abertura "Data Preparation phase ensured the quality..." — **IDÊNTICO**
- §3.3 parágrafos sobre outlier detection (Tajmouati, Sudjianto, Dias) — **IDÊNTICO**
- §3.3 parágrafo feature engineering (Chowdhury, Lin) — **IDÊNTICO**
- §3.4 parágrafos sobre ARIMA modeling (ADF, AIC/BIC, backtesting) — **IDÊNTICO**
- §3.4 parágrafos sobre RF handling non-linear relationships — **IDÊNTICO**
- §3.5 abertura "Evaluation phase assessed predictive performance..." — **IDÊNTICO**
- §3.5 parágrafo "Although ML-based models may provide..." (Sudjianto, Abouzaid) — **IDÊNTICO**
- §3.6 parágrafos sobre documentation/versioning (Semmelrock, Stromer) — **IDÊNTICO**

**§1 Introduction:**
- Os parágrafos de objetivos específicos (a, b, c, d) — **IDÊNTICO ao TCC1** (só mudou letras dos objetivos)
- A citação Vranješ/Niggemann block quote — **IDÊNTICO** (mas foi removida do TCC2; não readicionar)

### O que é NOVO no TCC2 (PRECISA DE NATURAL-WRITING):

**Abstract (00_resumo_abstract.md):** 100% novo. Denso, técnico, mas pode ter AI voice.

**§1 Introduction — partes novas:**
- Parágrafo sobre USMCA/Brasil contrast (adaptado mas restructurado)
- Parágrafo sobre ML/ARIMA comparison (adaptado mas restructurado)
- Roadmap paragraph: "The remainder of this paper is organized as follows..."
- Forward-reference: "The empirical results in Section 4 confirm this relevance..."
- Parágrafo de justificativa dos 3 países: "The choice of Canada, Mexico and Brazil..."

**§3 Methodology — parágrafos de implementação (novos em cada subseção):**
- §3.2 parágrafo "In this analysis, data included USD/CAD..." — o parágrafo completo de implementação
- §3.2 parágrafo "The data covered the period from 2000 to 2024..." — implementação
- §3.3 parágrafo "In this study, the Data Preparation pipeline aggregated..." — implementação
- §3.4 parágrafo "The Modeling phase produced 72 forecasting models..." — implementação (ARIMA walk-forward, Optuna 30 trials, etc.)
- §3.5 parágrafo "In this study, the evaluation combined four elements..." — implementação
- §3.6 parágrafo final sobre o repositório público — novo

**§4 Results:** 100% novo — tratar mais agressivamente

**§5 Discussion:** 100% novo — tratar mais agressivamente  

**§6 Conclusion:** 100% novo — tratar mais agressivamente

---

## ESTILO DO FRANCISCO (DO TCC1) — REFERÊNCIA

Estas são as características da escrita do Francisco no TCC1. O TCC2 deve soar como o mesmo autor:

### Estrutura de frase
- Frases médias a longas, com cláusulas subordinadas — mas não labirínticas
- Mistura de frases curtas e longas para criar ritmo
- Uma ideia central por frase; detalhes em cláusulas subordinadas

### Vocabulary choices
- Académico mas acessível — sem jargão desnecessário
- Usa termos técnicos corretos (exchange rate, bilateral trade flows, hyperparameter optimization)
- Não usa sinónimos pomposos: "use" não "utilize", "show" não "demonstrate"
- Conectores naturais: "however", "thus", "therefore", "while", "although" — não formulaicos

### Voz e perspetiva
- "this study", "the study", "we" — nunca "I"
- Voz ativa quando possível
- Passive voice só quando o sujeito é irrelevante ou desconhecido

### Citações (ABNT)
- Integradas na frase: "(KIM; LE, 2024)", "(MWANGE et al., 2022)"
- Maiúsculas nos sobrenomes: "(BATARSEH et al., 2020)"
- Nunca alterar citações existentes — nem pontuação, nem ordem

### Parágrafos
- Primeiro parágrafo introduz o tópico
- Desenvolvimento com evidências/argumentos
- Frase final conecta ao próximo ponto ou fecha o argumento
- Indentação de 8 espaços no início de cada parágrafo (formato ABNT)

### O que o Francisco NÃO faz
- Não começa parágrafos com "It is important to note that..."
- Não usa "In order to" — usa "To"
- Não empilha adjetivos ("comprehensive", "robust", "seamless")
- Não termina seções com "This section has shown that..."
- Não usa "the aforementioned" ou "as previously mentioned"
- Não usa frases de transição corporativas ("Building on this foundation...")

---

## FRASES BANIDAS — AI VOICE (da skill natural-writing)

Eliminar imediatamente se encontradas:

**Inglês académico:**
- "It is worth noting that" → simplesmente afirma
- "It is important to mention" → simplesmente menciona
- "In order to" → "To"
- "Utilize" → "Use"
- "Leverage" → "Use"
- "Facilitate" → "Help" / "Enable"
- "Comprehensive" → ser específico
- "Robust" → descrever o que o torna forte
- "Significant" sem número → dar número ou cortar
- "Moreover," no início de parágrafo → reformular
- "Furthermore," no início de parágrafo → reformular
- "Additionally," no início de parágrafo → reformular
- "Notably," → integrar naturalmente
- "This section presents..." como abertura → vai direto ao conteúdo
- "Building on the above..." → vai direto
- "As can be seen from..." → integra a observação diretamente
- Passive voice sem necessidade: "were identified by" → "identified"
- "The results obtained" → "the results"
- "It was found that" → afirma diretamente

---

## PROCESSO — COMO TRABALHAR

**REGRA CRÍTICA:** Trabalha **um ficheiro de cada vez**, **um parágrafo/bloco de cada vez**. Para cada bloco:

1. Mostra o texto ATUAL
2. Identifica o problema (AI voice? passive? filler words? etc.)
3. Propõe a versão melhorada
4. Aguarda autorização do Francisco antes de editar o ficheiro
5. Só avança para o próximo bloco depois de confirmação

**Para secções verbatim do TCC1:** escreve uma linha de nota, por exemplo:
> *§2.1 Nominal exchange rate até §2.1 HS — verbatim do TCC1. Sem alterações.*

E segue em frente.

**Ordem:** Abstract → §1 → §2 → §3 → §4 → §5 → §6

---

## RESPOSTAS DO FRANCISCO ÀS QUESTÕES DE ALINHAMENTO

**Q1: Partes adaptadas do TCC1 (tense change, minor cuts):**  
→ **Não aplicar natural-writing.** Essas secções servem de baseline de estilo. Menciona onde estão e passa à frente.

**Q2: Abstract — estilo:**  
→ **Aplicar natural-writing**, mas mantendo o propósito académico do TCC. Não simplificar demasiado — é um abstract técnico.

**Q3: §4, §5, §6 (100% novos):**  
→ **Tratar mais agressivamente** — são as secções com mais AI voice e precisam do trabalho mais cuidadoso.

---

## NÚMEROS QUE DEVEM FICAR EXATOS (NÃO ALTERAR)

Estes números foram verificados contra `results/forecasts/metrics_all.csv`:

- RF MAPE: **0.9381%** (média das 24 séries) — arredondado para **0.94%** no texto
- LightGBM MAPE: **1.0276%** → **1.03%**
- ARIMA MAPE: **1.4143%** → **1.41%**
- Naïve MAPE: **0.32%**
- DM test: RF > ARIMA em **75%** (18/24); LightGBM > ARIMA em **83%** (20/24); RF > LightGBM em **83%** (20/24)
- Gap ARIMA vs RF (todas 24): **0.4763 pp** ≈ **0.48 pp**
- Gap ARIMA vs LightGBM: **0.39 pp**
- Gap excluindo Ljung-Box violations: **0.51 pp** (RF vs ARIMA)
- Ljung-Box violations: **CAN exports_commodities** (p=0.0006) e **CAN imports_total** (p=0.0146)
- Friedman test p-value: **0.417**
- Séries totais: **24** (3 países × 2 direções × 4 setores)
- Features: **73**
- Modelos treinados: **72**
- Período de treino: **Jan 2010 – Dez 2021** (132 meses)
- Período de teste: **Jan 2022 – Dez 2024** (36 meses)
- Observações mensais: **180**

---

## CONVENÇÕES QUE NÃO PODEM MUDAR

- Citações ABNT — nunca alterar formato ou conteúdo
- "this study" não "this paper" (exceto onde já diz "this paper" no TCC1)
- Tempo verbal: **passado** em todo o TCC2 (exceto em citações diretas verbatim)
- Indentação de parágrafos: **8 espaços** (não alterar — é ABNT)
- Section references: "Section 4", "Section 5" etc. (não "chapter")
- "Random Forest" com maiúsculas; "LightGBM" como está; "ARIMA" caps
- "SHAP" caps; "Diebold-Mariano" com hífen; "CRISP-DM" caps

---

## COMO COMEÇAR

1. Lê `C:\Projects\trade-forecasting\.claude\skills\natural-writing\SKILL.md`
2. Lê o Abstract em `tcc/00_resumo_abstract.md`
3. Apresenta análise do Abstract: o que tem de AI voice, o que propões mudar
4. Aguarda autorização
5. Faz a alteração
6. Segue para §1

**Não adiantes seções. Uma de cada vez, com autorização explícita.**

---

## NOTA FINAL

Este trabalho é a entrega final de uma licenciatura. Cada frase importa. Vai devagar, vai bem. O Francisco prefere que perguntes antes de teres dúvidas a avançares com uma mudança errada.

Se encontrares algo que te parece ambíguo — pergunta antes de agir.

---

## REGRAS APRENDIDAS EM SESSÃO (2026-05-30)

Estas regras foram validadas durante o passe natural-writing do TCC2. Aplicar em todas as sessões futuras.

### Sem travessões
Nunca introduzir "—" (em dash) na prosa. Repontuar com vírgula, dois-pontos ou parênteses.
Remover travessões existentes nas seções novas (§4, §5, §6). Seções verbatim do TCC1 ficam intocadas.

### Burstiness — variar comprimento de frase
Misturar frases curtas (5-10 palavras) com frases longas (20-30 palavras). Nunca três frases seguidas do mesmo comprimento. Quebrar paralelismos e listas enumeradas: não usar a mesma estrutura sintática duas vezes seguidas no mesmo parágrafo.

### Anti-fórmula — variar a própria técnica
Não aplicar sempre a mesma solução (ex.: "cortar adjetivo + encurtar"). Cada parágrafo pede uma abordagem diferente: às vezes reordenar cláusulas, às vezes fundir frases, às vezes partir uma frase longa em duas. A variação de técnica é tão importante quanto a variação de comprimento.

### Voz de autor único — espelhar o vocabulário do Francisco
Reusar os verbos e expressões que ele já usa no TCC1: "achieved", "registered", "showed", "confirmed", "produced", "yielded". Não introduzir sinônimos novos. O objetivo é que §4, §5, §6 soem escritos pelo mesmo autor do §1 e §2, não por uma segunda voz.

### Resultado de referência
Antes do passe: §4 marcava 77% de probabilidade de AI (Pangram). Após o passe: 30%. Meta para §5 e §6: abaixo de 40%.

### Estado do passe (atualizar conforme avança)
- Abstract: PRONTO
- §1 Introduction: PRONTO
- §2 Theoretical Framework: PRONTO (verbatim, sem alterações)
- §3 Methodology: PRONTO
- §4 Results: PRONTO
- §5 Discussion: PRONTO
- §6 Conclusion: PRONTO
