# piprkit — Workplan, Feature Inventory & Roadmap

## Current Snapshot

| Component              | Status                  | Description                                                                                                                                 |
| ---------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Core Data Types        | ✅ Implemented           | `PupilMeasurement`, `PupilSeries`                                                                                                           |
| Light Stimulus Helpers | ✅ Implemented           | `LightStimulus`, `LightStimuliSeries`                                                                                                       |
| Data Loaders           | ✅ Implemented           | `load_real_series`, `load_simulated_pupil`, `simulate_pupil_measurement`                                                                    |
| Preprocessing          | ✅ Implemented           | Rolling mean/median, rate-of-change, interpolation, trimming                                                                                |
| Fitting                | ⚠️ Partially Implemented | `BaseFit` and phase fits (baseline, latency [stub], constrict [partial], sustain, redilation); `PupilFit` wrapper may return NaNs           |
| Metrics                | ⚠️ Partially Implemented | Window-based metrics (`baseline`, `pipr_6s`, `pipr_xs`, `peak_constriction`, `time_to_peak`) implemented; advanced metrics are placeholders |
---
## Detailed inventory (by module)

### piprkit.core
- `pupil_base.py` (✅ Implemented)  
	- ✅ Core storage and helpers for time & size arrays.
	- ✅ Methods: set_time_and_size, get_time, get_size, plot, interpolate, trim_time, trim_size, drop_nan.
	- ✅ Light stimulus attach/plot helpers (set_light_stimulus, get_light_stimulus, plot_light_stimulus).

- `pupil_measurement.py` (⚠️ Partially Implemented)
	- ✅`PupilMeasurement` class. Metrics, filters, and blink operations are exposed through composition-based namespace accessors: `.metrics` (`PupilMetrics`), `.filters` (`PupilFilters`), `.blinks` (`PupilBlinks`).
	- ⚠️Blink handling via `.blinks`: set_blinks/get_blinks/remove_blinks implemented. `find_blinks()` is NotImplemented and intentionally warns.

- `pupil_series.py`(✅ Implemented)  
	- ✅ `PupilSeries` class: supports multiple stimuli via LightStimuliSeries and `split(prepulse, postpulse)` to produce per-stimulus `PupilMeasurement`s.

### piprkit.utils
- `light_stimuli.py` (✅ Implemented)
	- ✅`LightStimulus` and `LightStimuliSeries` classes: add/get/plot, time offset management implemented.

- `utils.py` (✅ Implemented)  
	- ✅`check_time_series`: verifies monotonic, non-duplicated time arrays.

### piprkit.data
- `loaders.py` (⚠️ Partially Implemented)
	- ✅`load_real_series`, `load_simulated_pupil`: load CSV example data and return `PupilSeries` / `PupilMeasurement`.
	- ✅`simulate_pupil_measurement`: programmatic simulator using phase model functions from fitting modules.
	- ⚠️ More options to load pupil data. Currently limited to using two `numpy arrays`. Currently not implemented in `loaders`, but in `piprkit.core` directly.

### piprkit.preprocessing
- `filtering.py` (✅ Implemented)  
	- ✅ `PupilFilters` namespace (accessed via `measurement.filters`) with: rolling_filter, rolling_mean, rolling_median, get_rate_of_change, limit_rate_of_change.
    - ⚠️These operate in-place on the wrapped PupilMeasurement. Optionally, it should return a copy.

### piprkit.analysis
- `analysis/fitting/base_fit.py` (⚠️ Partially Implemented)
	- ⚠️ `BaseFit` abstract class: infrastructure for param handling and prediction. Goodness_of_fit and an optimization helper need work.
	- ❌Automatic phase detection is not implemented and raises NotImplementedError if start/end not provided.

- `analysis/fitting/phase_fits.py`(⚠️ Partially Implemented)
	- ✅`FitBaseline`: model & fit implemented (with warning if not 10s baseline).
	- ❌`FitLatency`: stub: model returns NaNs and fit() warns that latency fitting not implemented.
	- ❌`FitConstrict`: model implemented; fit() tries but then warns and forces NaN params (TODO noted). This is a known bug/risk.
	- ⚠️`FitSustain`: implemented; fit method present. Has issues.
	- ⚠️`FitRedilation`: implemented; fit method present. Has issues.

- `analysis/fitting/pupil_fit.py`(⚠️ Partially Implemented)
	- ⚠️`PupilFit` convenience wrapper assembles phase fits and runs them. Current functionality is limited due to phasefit implementations.

- `analysis/pupil_metrics.py`(⚠️ Partially Implemented)
	- ✅ Several window-based metrics implemented: calculate_baseline, apply_baseline_correction, get_average_size, pipr_xs/pipr_6s, peak_constriction, time_to_peak.
	- ❌ Many high-level/advanced metrics are placeholders raising NotImplementedError (transient_plr, plr_latency, constriction_v, pupil_escape, redilation_v, plateau, auc_early, auc_late, pipr_duration, net_pipr).

### docs & examples (⚠️ Partially Implemented)
- ⚠️ Need check on completeness, structure, and relevance of information 
- ⚠️ Example notebooks live in `docs/source/examples/example_ipynb/` and include measurement and series example sets. Need extra formatting changes and explanations.
- ⚠️ Ensure each code block is independently runnable.
- Pupil series incorrectly called pupil measurements 
- path/usernames uit warnings
- 

### tests
- ⚠️ `tests/` exists with unit tests. Completeness needs to be determined.

---

# Not implemented Errors:
- Pupil metrics:
  - transient_plr
  - plr_latency
  - constriction_v
  - pupil_escape
  - redilation_v
  - plateau
  - auc_early
  - auc_late
  - pipr_duration
  - net_pipr
- Base fit:
  - Automatic phase detection
- Blinks:
  - find_blinks