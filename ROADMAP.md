# Protostar Climate Risk Intelligence: Roadmap

**Deadline:** Nov 10, 11:59 PM ET = **Nov 11, 10:59 AM Dhaka**
**Task:** Using data up to 2018, forecast 2019–2025 for 210 U.S. cities.
**Targets (from `annual`):** `Avg Temp`, `ToT Prcp`, `Max Tmax`, `cdd`
**Baselines to beat:** historical average (climatology) and linear trend

**Judging rubric (accuracy is not scored directly):**

| Criterion | Weight |
|---|---|
| Reuse & Extensibility | 25% |
| Interoperability | 20% |
| Reproducibility | 20% |
| Scientific / Societal Value | 15% |
| Feedback to the Ecosystem | 15% |
| Technical Creativity | 5% |

---

## Day 1 (Nov 5): Loading → Split

### 1. Sanity checks (dataset card values = reproducibility check)
- [x] `daily` has 10,245,754 rows
- [x] `annual` has 28,052 rows
- [ ] 210 unique `Name` values
- [x] `cdd` is NaN in 1,206 rows
- [ ] Pittsburgh mean `cdd` over 1989–2018 ≈ 811

### 2. Check types and gaps
- [x] Nulls per column (`isna().sum()`)
- [ ] First/last year per city
- [ ] (Later, only if using `daily`) convert `Date` to datetime

### 3. Define targets
- [x] `Avg Temp`, `ToT Prcp`, `Max Tmax`, `cdd`; set `daily` aside for now

### 4. Quality filter (≤ 10 missing days, applied per target)
- [x] `ToT Prcp` uses `Miss Prcp`
- [x] `Max Tmax` uses `Miss Tmax`
- [x] `Avg Temp` and `cdd` use `Miss Tmax` + `Miss Tmin`
- Result: ~5–6% of values masked per target

### 5. Start year
- [x] Keep 1950 onward (earlier records are noisy, with many station moves)

### 6. Quick EDA
- [ ] Plot the 4 targets for 3–4 cities
- [ ] Plot the cross-city mean per year with a 10-yr rolling mean
- [ ] Plot the count of valid cities per year
- Expect: warming in `Avg Temp` and `cdd`; precipitation mostly noise

### 7. Time-based split (never random)
- [x] Fold A: train ≤ 2011, test 2012–2018 (13,020 / 1,470 rows)
- [x] Fold B: train ≤ 2004, test 2005–2011 (11,550 / 1,470 rows)
- [ ] Fit any scaler/imputer on train only; remove unused `SimpleImputer` import

---

## Day 2 (Nov 6): Baselines
- [ ] Climatology: each city's mean of the last 30 training years
- [ ] Linear trend: per-city line fit on years ≥ 1970, extended to the test years
- [ ] Score MAE and RMSE per target on both folds
- [ ] Save the results table (goes into the README)

## Day 3 (Nov 7): Beat the baselines
- [ ] Tune trend start year (1950 / 1970 / 1980) and climatology window (10 / 20 / 30)
- [ ] Try a trend + climatology blend (precipitation likely wants mostly climatology)
- [ ] Optional: pooled ML model (ridge or gradient boosting) using year, lat/lon, recent mean and slope
- [ ] Pick the best method **per target**; confirm it wins on **both** folds

## Day 4 (Nov 8): Final forecasts
- [ ] Refit the chosen method on all data ≤ 2018
- [ ] Predict 2019–2025: 210 cities × 7 years × 4 targets = 5,880 values
- [ ] Add uncertainty intervals (spread from backtest errors)
- [ ] Sanity-plot forecasts against history for a few cities
- [ ] Check the Space's About tab for the exact `submission.csv` format and whether it's still required

## Day 5 (Nov 9): Package (most rubric points)
- [ ] Notebook runs top to bottom, with markdown headers per section
- [ ] Pinned `requirements.txt`
- [ ] README.md sections:
  - [ ] Data used and filtering (1950+, ≤ 10 missing-days mask per target)
  - [ ] Method, and why a time-based split was used
  - [ ] Backtest results vs baselines
  - [ ] Limitations: station changes, missing days, airport stations ≠ whole city
  - [ ] **Feedback to the ecosystem**:
    - Raw `ToT Prcp` / `Max Tmax` have values even in years with 100+ missing days (e.g., Aberdeen 1894), so they can silently bias results
    - The missing-days rule doesn't say which `Miss` column applies to which target
    - Unclear / changing submission requirements (CSV vs GitHub + Markdown + video)
    - Anything else that caused friction

## Day 6 (Nov 10): Submit
- [ ] Someone else runs the notebook from scratch (reproducibility test)
- [ ] Create a public HF dataset with `submission.csv` + `README.md`
- [ ] Submit through the Space, aiming for the evening of Nov 10 (Dhaka time) to leave a buffer