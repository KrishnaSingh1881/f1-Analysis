# From P14 to "Simply Lovely" - 2022 Belgian GP Analysis

Data-driven Formula 1 analysis project focused on the 2022 Belgian Grand Prix (Spa-Francorchamps), centered on Max Verstappen's race from P14 to P1.

The repository is notebook-first and produces publication-ready visual outputs from FastF1 race/telemetry data.

## Audit Snapshot

This README is based on a direct code and artifact audit of the repository.

What is working well:
- Clear analytical story arc from race context to statistical and telemetry evidence.
- Reproducible exported datasets in the data folder.
- Strong chart coverage: progression, distributions, regression, correlation, telemetry, weather.
- Companion pairwise notebook for VER-vs-driver comparisons.

Main caveats discovered:
- Notebook execution order matters; several cells rely on in-memory variables from earlier sections.
- Main notebook contains a historical error output for the final results table cell, even though fallback logic for column names is now present.
- No automated test suite or CI checks for notebook outputs.
- FastF1 API deprecation warning appears for pick_driver usage (currently still functional).

## Project Objective

Quantitatively evaluate why Verstappen's Belgian GP win was achievable despite a P14 start by comparing him against:
- PER (Sergio Perez)
- SAI (Carlos Sainz)
- RUS (George Russell)

Analyses performed:
- Race progression by lap
- Lap-time cleaning and descriptive statistics
- Normal distribution fit and z-score context
- Tire degradation slope via linear regression
- Tire age vs lap-time Pearson correlation
- Fastest-lap telemetry comparison (speed, throttle, brake)
- Weather context

## Repository Structure

Top-level:
- notebook.ipynb: Main end-to-end analysis notebook.
- notebook_ver_comparisons.ipynb: Pairwise VER-vs-PER/SAI/RUS comparisons.
- requirements.txt: Python dependencies.
- data/: Exported CSV datasets generated from FastF1 session data.
- charts/: Generated chart outputs (PNG).
- cache/: FastF1 cache for local/offline-reuse of downloaded session data.

## Technology Stack

- Python
- FastF1
- pandas
- numpy
- scipy
- matplotlib
- seaborn
- Jupyter / ipykernel

## Environment Setup

1. Create and activate a virtual environment.

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

2. Install dependencies.

```bash
pip install -r requirements.txt
```

3. Launch Jupyter.

```bash
jupyter notebook
```

## How to Run

### Main workflow (recommended first)

1. Open notebook.ipynb.
2. Run cells top-to-bottom.
3. Confirm that these CSVs are generated in data/:
   - laps_data.csv
   - weather_data.csv
   - session_results.csv
   - telemetry_VER.csv
   - telemetry_PER.csv
   - telemetry_SAI.csv
   - telemetry_RUS.csv
4. Confirm charts are generated in charts/.

### Pairwise workflow (optional, after main)

1. Open notebook_ver_comparisons.ipynb.
2. Run top-to-bottom.
3. Generates VER-vs-PER/SAI/RUS pairwise charts in charts/.

## Data Pipeline (Audited)

The main notebook follows this sequence:

1. Session load and caching
- Enables FastF1 cache in ./cache
- Loads 2022 Belgium race session with telemetry

2. Export stage
- Filters laps to VER/PER/SAI/RUS
- Converts lap times to seconds (LapTimeSeconds)
- Exports laps, weather, session results, and each driver's fastest-lap telemetry

3. Cleaning logic for lap-time stats/regression/correlation
- Remove pit-in/pit-out laps
- Remove lap 1
- Remove laps with z_score > 2 relative to each driver's own mean

4. Modeling and visualization
- Descriptive stats + boxplot + rolling standard deviation
- Normal fit and cross-driver normal curves
- Linear regression on TyreLife vs LapTimeSeconds
- Pearson correlation on TyreLife vs LapTimeSeconds
- Telemetry alignment by shared distance grid + trace comparisons

## Output Datasets

From data/:
- laps_data.csv: Core lap-level dataset for four drivers.
- session_results.csv: Full classified race results from FastF1 export.
- weather_data.csv: Session weather samples (air/track temperature, pressure, wind, etc.).
- telemetry_*.csv: Fastest-lap telemetry per analyzed driver.

## Chart Outputs

Primary outputs in charts/ include:

Main notebook outputs:
- race_progression.png
- final_results_table.png
- boxplot_laptimes.png
- rolling_sd.png
- ver_normal_fit.png
- all_drivers_normal.png
- tire_regression.png
- pearson_correlation.png
- telemetry_speed.png
- telemetry_throttle_brake.png
- weather_temperature.png

Pairwise outputs:
- tire_regression_ver_vs_per.png
- tire_regression_ver_vs_sai.png
- tire_regression_ver_vs_rus.png
- pearson_ver_vs_per.png
- pearson_ver_vs_sai.png
- pearson_ver_vs_rus.png
- telemetry_speed_ver_vs_per.png
- telemetry_speed_ver_vs_sai.png
- telemetry_speed_ver_vs_rus.png
- telemetry_throttle_brake_ver_vs_per.png
- telemetry_throttle_brake_ver_vs_sai.png
- telemetry_throttle_brake_ver_vs_rus.png

## Key Analytical Signals

Based on generated visuals and notebook logic:
- VER reaches P1 rapidly after starting P14.
- VER's clean-lap distribution is generally left-shifted (faster) with strong consistency markers.
- Tire degradation slope for VER is compared directly against peers and teammates.
- Pearson comparison frames how strongly tire age maps to lap-time loss per driver.
- Telemetry overlays provide sectional and control-input evidence (speed, throttle, brake).

## Known Issues and Risks

1. Historical notebook output includes one KeyError trace for result table columns.
2. Some notebook sections rely on prior variable state if executed out of order.
3. FastF1 version changes can alter column names or behavior.
4. Large telemetry CSVs/charts can increase runtime and memory use.

## Recommendations

1. Refactor common cleaning logic into a reusable Python module.
2. Add a scripted pipeline (for example run_analysis.py) to produce all outputs non-interactively.
3. Add regression tests for schema assumptions in exported CSVs.
4. Pin library versions more strictly for reproducibility.
5. Update deprecated FastF1 calls when moving to newer versions.

## Reproducibility Notes

- The cache folder is expected and improves rerun speed significantly.
- The analysis targets one event (2022 Belgian GP race session).
- If FastF1 source data/schema changes, re-check column assumptions before rerunning.

## Disclaimer

This project is intended for educational and analytical use. Conclusions are based on selected drivers, chosen filtering assumptions, and available FastF1 telemetry/timing data.

