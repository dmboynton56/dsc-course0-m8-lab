# Aviation accident safety lab

Short analysis for an insurance-style client: among reported accidents, which professionally built fixed-wing airplanes show lower serious/fatal injury shares and lower hull destruction, and what shows up for weather versus phase of flight.

Exact tables, filters, and plots live in [`Aviation_Accidents_Cleaning.ipynb`](Aviation_Accidents_Cleaning.ipynb) and [`Aviation_Accidents_Data_Analysis.ipynb`](Aviation_Accidents_Data_Analysis.ipynb). Open in Jupyter or VS Code and run all cells top to bottom.

---

## What's in this repo

| Item | What it is |
| --- | --- |
| [`data/AviationData.csv`](data/AviationData.csv) | Raw NTSB/FAA-style extract |
| [`data/AviationData_cleaned.csv`](data/AviationData_cleaned.csv) | Filtered/feature table (~18k rows, 34 columns) |
| [`Aviation_Accidents_Cleaning.ipynb`](Aviation_Accidents_Cleaning.ipynb) | Clean data and save the CSV |
| [`Aviation_Accidents_Data_Analysis.ipynb`](Aviation_Accidents_Data_Analysis.ipynb) | Charts and commentary |
| [`figures/`](figures/) | Exported PNG plots |

---

## Cleaning notebook

1. Load the CSV with encoding that survives odd characters.
2. Keep airplanes only, professional (non-amateur) builds, events from 1983 onward.
3. Build metrics: totals from injury columns, injury fraction = (fatal + serious) ÷ onboard total when possible; destroyed flag from damage text.
4. Standardize Make/Model, drop rare makes, drop missing models, add `plane_type` = Make + Model.
5. Clean engine, weather, purpose, and phase fields for plotting.
6. Drop sparse columns targeted by the assignment (`Air.carrier`, `Schedule`).
7. Write [`data/AviationData_cleaned.csv`](data/AviationData_cleaned.csv).

---

## Analysis notebook

1. Load the cleaned CSV.
2. Label small versus large using 20+ people counted onboard as large (from summed injury tiers in each row).
3. Rank manufacturers by mean injury fraction and separately by destruction rate, with a minimum accident count per make.
4. Same idea for `plane_type` where there are at least 10 accidents in the slice.
5. Compare weather and phase of flight to injury and destruction (`factor_weather.png` and phase figures).
6. Save figures under [`figures/`](figures/).

---

## Recommendations from this dataset

These are summaries of accidents in this file only, not proof of which aircraft are “safest.” Pricing should still use fleet mix, hours, geography, training, etc.

### Large airplanes (20+ modeled occupants)

- Mean injury fraction (among makes with enough rows): leads include MCDONNELL DOUGLAS, BOMBARDIER / BOMBARDIER INC, BOEING, EMBRAER, then AIRBUS versus other large makes in-chart. See `makes_injury_frac_top15.png` and grouped tables in the analysis notebook.
- Destruction conditional on known outcome: BOMBARDIER INC and BOMBARDIER often look milder than BOEING, AIRBUS, or EMBRAER on demolition share on average — compare `makes_destruction_frac.png` before leaning on injury-only ranks.
- Example large `plane_type` strings with at least 10 accidents and low mean injury fractions include BOEING 777, 757, 787, 737-7H4, and BOMBARDIER INC CL-600-2B19 (plane-type aggregation cells; numbers drift slightly if notebooks are rerun).

### Small airplanes (under 20 modeled occupants)

- Lower mean injury among makes includes MCDONNELL DOUGLAS, BOMBARDIER, BOEING, AVIAT AIRCRAFT INC, MAULE on the favorable side of the small panels (mix of GA and some jets reported under low seat counts).
- Lower destruction often shows up on lighter trainers and tube-and-fabric lines (for example LUSCOMBE, GRUMMAN SCHWEIZER, STINSON, TAYLORCRAFT, AERONCA, ERCOUPE) — a different leaderboard than injury-only.
- Plane types with sample depth (n ≥ 10) and comparatively low mean injury include MAULE M-5-210C, some Maule variants, rare low-seat BOEING variants in this extract — check n next to each mean in the notebook.

### Weather and phase

- IMC and UNK sit higher than VMC on average for injury and destruction (`factor_weather.png`).
- Landing, approach, takeoff, maneuvering, and climb sit higher than cruise on average in the phase figures (energy/configuration risk during those segments).

---

## Figures

Mean injury fraction by make (small vs large panels):

![Makes: mean injury fraction](figures/makes_injury_frac_top15.png)

Destruction rate by make:

![Makes: destruction rate](figures/makes_destruction_frac.png)

Weather vs injury and destruction:

![Weather factors](figures/factor_weather.png)

Phase vs injury:

![Phase vs injury](figures/factor_phase_injury.png)

Phase vs destruction:

![Phase vs destruction](figures/factor_phase_destroy.png)

More plots (`small_makes_violin_injuries.png`, `large_makes_strip_injuries.png`, plane-type PNGs) are in [`figures/`](figures/).

---

## Limitations

1. No exposure denominator — rankings are conditional on mishaps appearing in NTSB data, not crashes per flight hour or per departure.
2. The 1983+ filter is not the same thing as today’s fleet age or retrofit mix.
3. `total_aboard` is inferred from injury-tier fields, not manifests; blanks and miscoding affect injury fraction.
4. Reporting differs by severity — what enters the archive is selective.
5. Correlation only — worse averages under IMC or in landing phases do not prove those facts caused severity; other factors hide in the same buckets.
