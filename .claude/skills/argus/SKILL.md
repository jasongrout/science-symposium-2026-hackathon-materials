---
name: argus
description: Argus Array survey simulator and visit store. Load for questions about Argus sky coverage, per-position depth, cadence, light curve injection, or running the survey simulator. Package is argus_sim; source lives in argus/argus_survey_model/src/argus_sim/.
---

# Argus Array Survey Simulator (`argus_sim`)

The Argus Array is a wide-field photometric survey telescope. ArgusSim has two distinct parts; most science users only need the second.

## Part 1 — Visit store (science users start here)

The visit store is a pre-computed survey run (current: v5.4, 5-year survey) stored as Parquet on S3 or local disk. Open it with `SurveyRun`; no GPU needed.

```python
import numpy as np
from argus_sim.lightcurve import SurveyRun, bin_lightcurve

run = SurveyRun("s3://schmidt-observatory-system/argus/sim/argsim_5yr_beta_rho_uniform_v5.4/")
# or: run = SurveyRun("~/s3/argus/sim/argsim_5yr_beta_rho_uniform_v5.4/")  # NFS mount on JupyterHub
```

### Key methods

```python
vis = run.visits(ra, dec, band="g")          # all 60-s slots covering (ra,dec); one row per slot+OTA
vis = run.visits(ra, dec, band=["g","rho"])  # filter to bands
vis = run.visits(ra, dec, mjd_min=…, mjd_max=…)

run.depth_map("g", period="all")    # per-tile 5-sigma depth; period may be "all", "y1", "y2", …
run.night_log(ra, dec)              # why nights have no data (weather, Moon, footprint)
run.nights()                        # nightly summary: depth, area, Moon, weather

snr   = run.snr(vis, mag)           # per-visit SNR for a source of this mag
sigma = run.sigma_mag(vis, mag)     # per-visit photometric uncertainty (mag)
lim   = run.limit_mag(vis)          # per-visit 5-sigma limiting magnitude
```

### Light curve injection

Any function `f(mjd, band) → AB_magnitude` works as a model:

```python
def rr_lyrae(mjd, band, P=0.5672, mean=16.5, amp=0.8):
    ph = (mjd / P) % 1.0
    shape = np.where(ph < 0.15, ph / 0.15, 1 - (ph - 0.15) / 0.85)
    return mean + amp / 2 - amp * shape

lc = run.inject(vis, rr_lyrae, rng=np.random.default_rng(1))
nightly = bin_lightcurve(lc, by="night")
```

`lc` is an astropy Table with columns `mjd`, `band`, `mag`, `mag_err`, `snr`. `sncosmo` and `redback` models plug in directly.

### S3 credentials / custom endpoint

```python
run = SurveyRun("s3://bucket/prefix/", storage_options={"anonymous": True})
# or: storage_options={"access_key": …, "secret_key": …, "region": …}
```

## Part 2 — Survey simulator (instrument / cadence studies)

Runs the full schedule from scratch; needs a GPU for reasonable speed (CuPy). Most users don't need this.

```python
import argus_sim
from astropy.time import Time

survey = argus_sim.Survey(
    start=Time("2027-01-01"),
    end=Time("2027-06-01"),
)
survey.run()   # generates the visit store
```

Override the instrument or survey with a `argussim.toml` in the working directory (`asim defaults` writes the template).

## Instrument quick-reference

| parameter | value |
|---|---|
| aperture per telescope | 280 mm |
| filter bands | g, ρ (red-optical) |
| exposure time | 60 s (base cadence) |
| read noise | 1.2 e⁻ |
| dark current | 0.0022 e⁻/s |
| sky grid | HEALPix nside=512 (nested) |
| visit store format | Parquet (pyarrow) |

## Tutorials

`argus/argus_survey_model/notebooks/`:
- `tutorial_survey_data.ipynb` — reading the visit store, depth maps, light curve injection
- `tutorial_depth_calculation.ipynb` — single-exposure depth from throughput + noise
