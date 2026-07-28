# Formula 1 Driver Circuit Specialization

Portfolio-grade analytics project measuring where Formula 1 drivers outperform expectations across circuit characteristics, while controlling for constructor performance, starting position, and season effects.

This project measures **specialization**, not all-time driver greatness.

## Scope

- Seasons: 2004–2025
- Source: [Jolpica F1 API](https://jolpi.ca/)
- Unit of analysis: one driver per Grand Prix
- Modeling approach: interpretable regression

## Main takeaways

1. **Circuit specialization exists, but it is a secondary effect.** The baseline model explains 45.6% of driver-race performance variation using starting position, pit-lane starts, and constructor-season effects. Each circuit-specialization model explains about 1.6% of the remaining variation before adjustment and less than 0.4% after adjustment.
2. **Versatility and overall performance are different ideas.** The correlation between specialization magnitude and mean performance above expectation is only -0.18. A driver can be consistently strong across circuit types, strongly specialized, both, or neither.
3. **Hamilton is the clearest high-profile example of versatility.** He ranks third in versatility, performs about 2.1 percentage points above the model's expectation on average, and shows only a slight high-downforce preference.
4. **Verstappen has the highest mean outperformance in the eligible group.** He finishes about 4.8 percentage points above expectation on average, but ranks 16th in versatility. Strong overall performance does not require the flattest circuit profile.
5. **Alonso combines outperformance with a pronounced profile.** He ranks second in mean outperformance, while the model associates him with relatively stronger results at lower-speed, more technical, higher-downforce, and overtaking-difficult circuits.
6. **The directional labels must be read as preferences, not grades.** A negative slope means stronger relative performance toward the opposite end of that circuit dimension; it does not mean poor performance.

All effects are descriptive associations relative to the model's baseline, not causal estimates of driver ability.

![Driver specialization in both directions](README%20assets/01_driver_specialists.png)

![Versatility and overall performance](README%20assets/03_versatility_vs_outperformance.png)

## Reproducible workflow

Run notebooks in numerical order. Each notebook is self-contained and must finish its `Validation` and `Supercheck` sections with `PASS` before the next notebook is used.

1. [`01_download_raw_data.ipynb`](notebooks/01_download_raw_data.ipynb) — cache and validate raw API data
2. [`02_build_driver_race_dataset.ipynb`](notebooks/02_build_driver_race_dataset.ipynb) — build the canonical driver-race dataset
3. [`03_build_circuit_features.ipynb`](notebooks/03_build_circuit_features.ipynb) — merge and validate circuit characteristics
4. [`04_driver_track_model.ipynb`](notebooks/04_driver_track_model.ipynb) — estimate baseline performance and specialization
5. [`05_visualizations.ipynb`](notebooks/05_visualizations.ipynb) — produce publication-quality analytical figures
6. [`06_storytelling.ipynb`](notebooks/06_storytelling.ipynb) — synthesize the evidence into a polished, limitation-aware case study

## Installation

```bash
git clone https://github.com/lukeweeklund/f1-circuit-specialization.git
cd f1-circuit-specialization
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m ipykernel install --user --name f1-circuit-specialization
```

Open the repository in JupyterLab, Jupyter Notebook, or VS Code and select the
`f1-circuit-specialization` kernel. On Windows, activate the environment with
`.venv\Scripts\activate` instead.

## Execution

1. Start with `notebooks/01_download_raw_data.ipynb`.
2. Use **Kernel → Restart and Run All**.
3. Confirm that both `Validation` and `Supercheck` end with `PASS`.
4. Continue through the notebooks in numerical order.

Notebook 1 uses a cache-first download process. A first run generally takes
**25–45 minutes**, depending on API response times and rate limiting. Every
successful response is cached immediately, so an interrupted run resumes
without downloading completed requests again. Once the final raw CSV files
exist and `FORCE_REFRESH = False`, later runs skip the API pull and load the
saved files directly.

Notebooks 2–6 are local transformations and analyses; each will usually finish
in under a minute on a modern laptop.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── requirements.txt
├── notebooks/
│   ├── 01_download_raw_data.ipynb
│   ├── 02_build_driver_race_dataset.ipynb
│   ├── 03_build_circuit_features.ipynb
│   ├── 04_driver_track_model.ipynb
│   ├── 05_visualizations.ipynb
│   └── 06_storytelling.ipynb
├── data/
│   ├── external/
│   │   └── f1_circuit_metadata.csv
│   ├── raw/          # generated locally
│   ├── processed/    # generated locally
│   └── outputs/      # generated locally
└── README assets/
    └── publication-ready figures
```

## Data sources

- Historical race schedules, results, qualifying results, drivers,
  constructors, and circuits come from the
  [Jolpica Formula 1 API](https://api.jolpi.ca/ergast/).
- Circuit-characteristic scores in
  [`data/external/f1_circuit_metadata.csv`](data/external/f1_circuit_metadata.csv)
  are analyst-authored project inputs. They provide consistent 1–10 ratings
  for speed, technical demand, downforce demand, and overtaking environment.

The canonical circuit metadata is committed because it is required to
reproduce the feature engineering and model results.

## Data policy

API downloads, caches, processed datasets, and validation reports are intentionally excluded from version control. The required circuit metadata is included so the complete workflow can be reproduced from a fresh clone.

## Limitations

- **Descriptive, not causal or predictive.** Constructor-season effects use the full observed season, and the analysis explains historical results rather than forecasting unseen races.
- **Circuit traits are manually assigned and static.** The 1–10 scores represent one canonical profile per circuit. They do not capture layout revisions, resurfacing, weather, tire compounds, regulations, or year-specific setup demands.
- **Trait dimensions overlap.** Speed, technical demand, downforce, and overtaking environment are correlated. Each specialization model evaluates one dimension separately, so a slope should not be treated as an isolated causal effect.
- **The remaining signal is small.** Specialization models explain only a small fraction of baseline residual variation. Extreme driver slopes are useful leads for interpretation, not definitive rankings.
- **No uncertainty intervals are used in the rankings.** Drivers with at least 50 starts are included, but their estimates still have different precision because their careers cover different numbers of races, circuits, teams, and eras.
- **Era and career context are pooled.** A driver receives one slope per trait across the entire 2004–2025 sample. The model does not capture development over a career, regulation changes, or a driver's changing relationship with a team.
- **Starting position is an imperfect control.** It captures much of car and driver pace but is itself partly produced by driver performance. Controlling for it changes the question to race performance relative to grid position, not total weekend performance.
- **Finishing position is affected by events outside driver control.** Reliability, crashes, strategy, safety cars, penalties, and team orders can move the outcome. Finish-status diagnostics expose these groups but cannot remove every source of noise.
- **Eligibility introduces selection.** Requiring 50 starts improves stability but excludes short careers and newer drivers, and may favor drivers who remained in Formula 1 long enough to qualify.
- **Coverage begins in 2004.** Earlier careers and races are excluded because complete qualifying coverage was not available from the selected source.

## License

This project is available under the [MIT License](LICENSE).
