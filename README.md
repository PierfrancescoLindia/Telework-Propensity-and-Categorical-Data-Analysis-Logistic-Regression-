# Telework Propensity and Categorical Data Analysis (Logistic Regression)

This project analyses how **socio-demographic characteristics**, **life satisfaction** and **work–life balance** influence people’s **willingness to perform telework** (smart working).  

The study is based on the Italian *Time Use Survey 2013* and was developed as a project for the course *Dati Statistici per Dati Categoriali* at the University of Calabria.

---

## 1. Research question

The key question is:

> Which factors affect the probability that an individual would like to perform telework?

The analysis focuses on how:

- socio-demographic factors (age, gender, education, citizenship, geographical area),
- subjective well-being (life satisfaction, work–life balance, job and financial satisfaction),
- stress and living conditions

shape the **propensity to adopt telework** in a pre-pandemic context (year 2013).

---

## 2. Data

The project uses microdata from the **2013 Italian Time Use Survey**.  
Only individuals aged **15 years or more** and with valid answers on telework are retained.

### 2.1 Dependent variable

Original question (simplified):

> “Would you be interested in performing telework for (part of) your current activities?”

The answers are recoded into a **binary variable**:

- `telela_bin = 1` → the person would like to telework  
- `telela_bin = 0` → all other answers (would not like, cannot, does not know, already teleworking, etc.)

This allows us to use **binary logistic regression**.

### 2.2 Independent variables

The explanatory variables are grouped as follows.

#### Demographic variables

- `fascia_eta` – age class (youngest, young, adult, elderly)  
- `fascia_istruzione` – education level (low, medium, high)  
- `sesso` – gender (male, female)  
- `fascia_cittadinanza` – citizenship (Italian, foreign)  
- `regione` – macro-area (North-West, North-East, Centre, South, Islands)

#### Satisfaction and well-being variables

(Recoded into binary indicators: 1 = satisfied/positive, 0 = not satisfied/negative)

- `sodqnt_bin` – satisfaction with amount of free time  
- `votovi_bin` – general life satisfaction  
- `sodequ_bin` – satisfaction with work–life balance  
- `sodla_bin` – job satisfaction  
- `risec_bin` – satisfaction with financial situation

#### Other contextual variables (descriptive part)

- `stress_cat` – stress intensity  
- `strfon_cat` – main source of stress  
- `stanzem_cat` – dwelling size (small / medium / large)

Missing or undefined answers are treated explicitly.  
For some satisfaction variables, missing values are conservatively included in the “not satisfied” group, so that the “satisfied” category represents clearly positive responses.

---

## 3. Exploratory analysis

The first part of the project provides an **exploratory analysis** of all variables:

- frequency tables and bar plots for the dependent variable (interest in telework),
- distribution of age, gender, education, citizenship, region,
- distribution of each satisfaction and stress indicator.

Main descriptive findings:

- In 2013 only a **small minority** of individuals were already teleworking, and a limited share expressed interest in telework. A large portion declared that they **could not** telework because of the type of job.
- The sample is roughly balanced by gender, with a slight majority of women. Most respondents are Italian citizens and concentrated in Southern Italy and the North-West.
- Satisfaction indicators show substantial heterogeneity, which justifies their inclusion as explanatory variables in the models.

Tables are produced using `dplyr`, `knitr` and `kableExtra`, while plots are created with `ggplot2`.

---

## 4. Logistic regression models

The core of the analysis is based on **binary logistic regression**, where the log-odds of wanting to telework are modelled as a function of the explanatory variables.

### 4.1 Model 1 – Demographic variables

Model 1 includes only **socio-demographic predictors**:

- age class (`fascia_eta`)
- education level (`fascia_istruzione`)
- gender (`sesso`)
- citizenship (`fascia_cittadinanza`)
- macro-area (`regione`)

This model answers the question:

> “Which demographic groups are more (or less) likely to want telework?”

Main results (in terms of odds ratios):

- Both the youngest and the oldest age groups show a **lower propensity** to telework compared to adults.
- **Women** are more likely to want telework than men.
- Residents in the **South** and **Islands**, as well as **foreign citizens**, have **lower probabilities** of preferring telework.
- Individuals with **medium-low education** show a **higher probability** of wanting telework, possibly because telework is perceived as improving working conditions and reducing physical effort.

Goodness-of-fit:

- The residual deviance is lower than the null deviance, indicating improvement over the intercept-only model.
- The AIC (around 7639) is used as a reference for comparison with the extended model.

### 4.2 Model 2 – Demographic + satisfaction variables

Model 2 extends Model 1 by adding satisfaction-related predictors:

- `sodqnt_bin` – satisfaction with free time
- `votovi_bin` – life satisfaction
- `sodequ_bin` – work–life balance satisfaction
- `sodla_bin` – job satisfaction
- `risec_bin` – financial satisfaction

In symbolic form:

> logit( P(telela_bin = 1) )  
> = Model 1 terms  
> + γ₁ · sodqnt_bin  
> + γ₂ · votovi_bin  
> + γ₃ · sodequ_bin  
> + γ₄ · sodla_bin  
> + γ₅ · risec_bin.

Key findings:

- **Satisfaction with free time** is positively associated with telework propensity: people satisfied with their free time are more open to telework, consistent with the idea that telework helps maintain or improve time management.
- **Work–life balance satisfaction** has a positive and significant effect: individuals who already feel a good balance are more inclined to adopt telework.
- **Job satisfaction** is also positively related to the willingness to telework, suggesting that telework is seen as a further improvement of an already positive job situation.
- **Financial satisfaction** has a negative but not statistically significant effect.

Model 2 has:

- lower residual deviance than Model 1,
- a **lower AIC (around 7564)**, indicating a better trade-off between fit and complexity.

### 4.3 Model comparison

Model 1 and Model 2 are compared using:

- **AIC**: smaller for Model 2;
- **Likelihood ratio (deviance) test**:

  - Residual deviance Model 1: 7618.7  
  - Residual deviance Model 2: 7533.7  
  - LR statistic: 85.09 with 5 degrees of freedom  
  - p-value: 2.2e-16

The deviance test strongly rejects the null hypothesis that the additional satisfaction variables have no effect.  
Therefore, **Model 2 provides a significantly better explanation** of telework propensity.

---

## 5. Interpretation and conclusions

The empirical results suggest that, in 2013:

- Telework was **not yet widespread**, but it was perceived as a **complementary and advantageous working mode** by certain groups.
- Demographic factors matter: women, adults and individuals with medium-low education show higher interest in telework, while foreign citizens and residents in the South/Islands are less inclined.
- Satisfaction variables are crucial: people who are already satisfied with their job, free time and work–life balance are **more likely** to want telework.
- Telework is **not mainly requested by dissatisfied workers**; instead, it is perceived as a way to **further improve** an already acceptable or good situation.

The report also discusses how these pre-pandemic findings can be re-interpreted in the post-COVID era, where telework has become mainstream and is increasingly integrated into normal work arrangements.

---

## 6. Repository structure

A recommended structure for the GitHub repository is:

```text
.
├─ R/
│   └─ categorical_telework_logit.R      # Main R script (data prep, EDA, logistic models)
│
├─ data/
│   └─ UsoTempo_A2013_*.dta             # Time Use Survey microdata (if they can be shared)
│
├─ docs/
│   └─ report/
│       └─ Progetto_Dati_Categoriali_Telelavoro.pdf   # Full project report (Italian)
│
├─ .gitignore
├─ LICENSE
└─ README.md
