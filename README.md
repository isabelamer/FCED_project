# FCED_project - Statistics

# Human Influence in Rural Wildfire Ignitions

Statistical analysis of human-caused rural fires in four Portuguese municipalities (Chaves, Montalegre, Ourém and Tomar), comparing the period before 2017 (2001–2016) with the period after 2017 (2018–2025). The work combines descriptive statistics and inference techniques (proportion tests, t-tests and ANOVA) on the official rural fire records of the Instituto da Conservação da Natureza e das Florestas (ICNF).

The full analysis, with code, figures and discussion, is in the Jupyter notebook [`script.ipynb`](script.ipynb).

## Authors (Group 02)

- Benedita Barreto da Costa Gonçalves
- Isabela Moreira Merelles
- Maria Leonor Soares Carvalho
- Mariana Vale de Oliveira Coelho de Pinho

## Research question

> How did human-caused wildfire ignitions and their burnt area change before and after 2017, and how do they differ across four Portuguese municipalities?

2017 was the most severe wildfire year on record in Portugal and led to a reform of the prevention and suppression system (Resolução do Conselho de Ministros n.º 157-A/2017, Decreto-Lei n.º 12/2018 and Decreto-Lei n.º 82/2021). For this reason, 2017 is treated as a transition year and left out of every comparison. Because the reform was introduced gradually, the post-2017 period mixes years before and after Decreto-Lei n.º 82/2021, so the analysis describes change around 2017 and does not estimate the effect of any single measure.

### Study area

The four municipalities form two pairs of neighbouring municipalities in contrasting regions.

| Region | Municipalities | Main features |
|---|---|---|
| Alto Tâmega (North) | Chaves, Montalegre | Inland, mountainous, sparsely populated, extensive pastoral land use and traditional use of fire to renew pasture |
| Médio Tejo (Centre) | Ourém, Tomar | Lower altitude, more densely populated, denser network of settlements and roads, large share of pine and eucalyptus |

The two regions differ in the human factors proposed in the theme (population density, land use and accessibility), and the choice of two municipalities per region helps to separate regional contrasts from differences between individual municipalities.

### Secondary questions

| | Question | Variables | Methods |
|---|---|---|---|
| **Q1** | Did the proportion of negligent fires among human-caused fires change after 2017, and was the change the same in the critical period (June to September) and in the remaining months? | `Cause_Type`, `Period`, `Season_Risk_Period` | Two-proportion Z-test per season, Breslow-Day test, logistic regression, Mantel-Haenszel test |
| **Q2** | After 2017, did the mean number of daily negligent ignitions differ between weekends and weekdays? | `Daily_Ignitions_Count`, `Day_Type` | Welch's two-sample t-test over all calendar days and over active days |
| **Q3** | In 2018–2025, does the geometric mean burnt area per fire differ across the four municipalities? | `Log_Total_Burnt_Area`, `Municipality` | One-way ANOVA with Tukey HSD, Welch's ANOVA |
| **Q4** | Did the gap in burnt area between intentional and negligent fires narrow after 2017? | `Log_Total_Burnt_Area`, `Cause_Type`, `Period`, `FWI` | Two-way ANOVA, ANCOVA with FWI, HC3 standard errors, trend and step model |

## Data

The data come from the rural fire records of the Sistema de Gestão de Informação de Fogos Rurais (SGIF), published by the ICNF in the [statistics section of its website](https://www.icnf.pt/florestas/gfr/gfrgestaoinformacao/estatisticas). Each record is one fire occurrence, whatever its cause. The records are split across three files, which are combined into a single dataset before the analysis.

- `Registos_Incendios_SGIF_2001_2010.xlsx`
- `Registos_Incendios_SGIF_2011_2020.xlsx`
- `Registos_Incendios_SGIF_2021_2025.xlsx`

The raw files have 40 columns. Only nine are read (`Ano`, `Mes`, `Dia`, `AreaTotal_ha`, `FWI`, `DataHoraAlerta`, `Concelho`, `TipoCausa` and `Codigo_SGIF`), and the analysis variables are derived from them.

### Variables

| Variable | Type | Description |
|---|---|---|
| `Cause_Type` | Qualitative | Intentional or Negligent, from `TipoCausa`. Natural, unknown and unclassified causes and reignitions are excluded where the cause is needed |
| `Period` | Qualitative | Pre-2017 (2001–2016) or Post-2017 (2018–2025), from `Ano` |
| `Municipality` | Qualitative | Chaves, Montalegre, Ourém or Tomar, from `Concelho` |
| `Season_Risk_Period` | Qualitative (auxiliary) | Critical (June to September) or Non-Critical, from `Mes` |
| `Day_Type` | Qualitative (auxiliary) | Weekend (Saturday and Sunday) or Weekday, from `DataHoraAlerta` |
| `Daily_Ignitions_Count` | Quantitative | Number of negligent ignitions per municipality per day |
| `Total_Burnt_Area` | Quantitative | Total burnt area per fire, in hectares, from `AreaTotal_ha` |
| `Log_Total_Burnt_Area` | Quantitative | log10 of the burnt area, for fires above 0 ha |
| `FWI` | Quantitative | Fire Weather Index on the day of the fire |

Fires with 0 ha are kept in the analyses that count ignitions (Q1 and Q2) and excluded from the log-scale analyses (Q3 and Q4), since the logarithm of 0 is undefined. This affects 1 of 2,739 fires in Q3 and 3 of 6,519 fires in Q4.

## Repository structure

```
.
├── script.ipynb                  # full analysis
├── README.md
└── 01_dataset/
    ├── raw/                      # ICNF files (to be downloaded, see below)
    │   ├── Registos_Incendios_SGIF_2001_2010.xlsx
    │   ├── Registos_Incendios_SGIF_2011_2020.xlsx
    │   └── Registos_Incendios_SGIF_2021_2025.xlsx
    └── processed/                # created by the notebook
        ├── wildfires_processed.xlsx
        ├── daily_ignitions.xlsx
        ├── q1_cause_period_season.xlsx
        ├── q2_daily_ignitions.xlsx
        ├── q3_burnt_area_municipality.xlsx
        ├── q4_burnt_area_cause_period.xlsx
        └── sample_sizes.xlsx
```

## How to run

1. Clone the repository.

   ```bash
   git clone <https://github.com/isabelamer/FCED_project/tree/main>
   cd <FCED_project>
   ```

2. Download the three SGIF files from the [ICNF statistics page](https://www.icnf.pt/florestas/gfr/gfrgestaoinformacao/estatisticas) and place them in `01_dataset/raw/` with the file names listed above.

3. Install the dependencies (the notebook was run with Python 3.11).

   ```bash
   pip install numpy pandas scipy statsmodels matplotlib seaborn openpyxl jupyter
   ```

4. Open the notebook and run all cells in order. The `01_dataset/processed/` folder and its files are created in Section 2.3 and read again in each question.

   ```bash
   jupyter notebook script.ipynb
   ```

The notebook contains assertions that stop the execution if the processed data change in a way that would invalidate the numbers reported in the text (for example, a different sample size in Q3).

## Notebook structure

1. **Research Question.** Context, study area and secondary questions.
2. **Collect Data.** Data source, variable definitions and data preparation.
3. **Explore Data.** Descriptive statistics and figures for Q1 to Q4.
4. **Draw Inferences.** Assumptions, hypotheses, tests and robustness checks for Q1 to Q4.
5. **Formulate Conclusions.** One conclusion per question and an overall answer.
6. **Look Back and Ahead.** Limitations, alternative approaches and future work.
7. **Appendix A.** Additional robustness checks (municipality adjustment, Welch's ANOVA, clustered standard errors, alternative treatments of small fires and of the incomplete year 2025).

## Main results

All tests use α = 0.05.

**Q1.** In the critical period, the share of negligent fires among human-caused fires fell from 55.7% to 47.4% (Z = −4.79, p < 0.001). In the remaining months it rose from 82.6% to 88.2% (Z = 4.10, p < 0.001). The change differed significantly between seasons (Breslow-Day χ²(1) = 36.50, p < 0.001). Within municipalities, the increase outside the critical period holds, but the decrease in the critical period does not, as it reflects mainly the larger share of fires from Ourém after 2017.

**Q2.** After 2017, the mean number of daily negligent ignitions did not differ significantly between weekends and weekdays, either over all calendar days (Welch's t = 1.47, p = 0.142) or on days with at least one ignition (t = 1.32, p = 0.187). The same holds in each municipality and with standard errors clustered by date.

**Q3.** In 2018–2025, the geometric mean burnt area per fire differed across municipalities (F(3, 2734) = 140.4, p < 0.001, η² = 0.13). It was highest in Montalegre (0.50 ha), followed by Chaves (0.14 ha), Ourém (0.05 ha) and Tomar (0.04 ha), and Ourém and Tomar could not be distinguished. The ordering holds in every robustness check and in each of the eight years, although the size of the ratios depends partly on how very small fires are recorded.

**Q4.** The typical negligent fire was larger than the typical intentional fire in both periods (F(1, 6512) = 55.94, p < 0.001), and fires of both causes were smaller after 2017 (F(1, 6512) = 100.35, p < 0.001). The interaction was significant (F(1, 6512) = 5.95, p = 0.015) but negligible in size (partial η² = 0.0009) and disappeared once municipality, balanced periods of eight years or FWI were taken into account. The gap between causes therefore did not narrow, and the decrease in fire size was already under way before 2017.

**Overall.** Around 2017, the balance between negligent and intentional causes changed differently in summer and in the rest of the year, and the typical fire with a recorded human cause became smaller as part of a longer trend that fire weather does not explain. Municipality was the factor with the clearest and most stable association with fire size. None of these changes can be linked to a specific policy measure.

## Limitations

- The post-2017 period mixes years before and after Decreto-Lei n.º 82/2021, so no change can be attributed to a specific measure.
- The share of fires with a recorded human cause rose from 36.4% before 2017 to 90.7% after 2017, which affects the comparisons between periods (Q1 and Q4).
- Small burnt areas are often recorded as rounded values (0.01, 0.5, 1 and 2 ha), and recording precision differs between the two regions.
- The pre-2017 period covers 16 years and the post-2017 period only 8, with 2025 incomplete (last alert in early November 2025).
- Records are observational and not fully independent (shared weather, neighbouring municipalities and reignitions), which was partly addressed with clustered standard errors.

## References

Decreto-Lei n.º 124/2006, de 28 de junho. (2006). *Diário da República*, 1.ª série.

Decreto-Lei n.º 12/2018, de 16 de fevereiro. (2018). *Diário da República*, 1.ª série.

Decreto-Lei n.º 82/2021, de 13 de outubro. (2021). *Diário da República*, 1.ª série.

Fernandes, S., & Lourenço, L. (2019). Grandes incêndios florestais de março, junho e outubro (fora do período crítico) em Portugal Continental. *Territorium*, *26*(II), 15–34. https://doi.org/10.14195/1647-7723_26-2_2

Instituto da Conservação da Natureza e das Florestas. (n.d.). *Registos de incêndios rurais SGIF 2001–2010, 2011–2020 e 2021–2025* [Data set]. Retrieved November 15, 2026, from https://www.icnf.pt/florestas/gfr/gfrgestaoinformacao/estatisticas

Instituto da Conservação da Natureza e das Florestas. (2017). *10.º relatório provisório de incêndios florestais 2017: 01 de janeiro a 31 de outubro* (RIF10/2017). https://www.icnf.pt/api/file/doc/2c45facee8d3e4f8

Moore, D. S., McCabe, G. P., & Craig, B. A. (2017). *Introduction to the practice of statistics* (9th ed.). W. H. Freeman.

Resolução do Conselho de Ministros n.º 157-A/2017, de 27 de outubro. (2017). *Diário da República*, 1.ª série.
