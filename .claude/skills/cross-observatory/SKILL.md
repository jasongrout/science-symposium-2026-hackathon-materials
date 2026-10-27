---
name: cross-observatory
description: Multi-facility science with the Schmidt Observatory System. Load when a question involves more than one observatory, asks how facilities compare, or requires combining outputs (depth, SNR, spectra, radio) across Lazuli, DSA-2000, Argus, or LFAST.
---

# Cross-Observatory Science — Schmidt Observatory System

## Facility quick-reference

| facility | wavelength / band | angular resolution | field of view | strengths |
|---|---|---|---|---|
| **Lazuli WCC** | optical (g–z, ~0.4–1.0 µm) | ~100 mas (space, defocused PSF) | ~1 deg² | wide-field optical imaging; transient discovery; transit photometry |
| **Lazuli IFS** | optical (0.4–0.68 µm) | 40–80 mas spaxels | 58×58 spaxels (narrow/wide field) | classification spectra; SN typing; kilonova spectral evolution |
| **Lazuli ESC** | optical + near-IR | coronagraphic | ~few arcsec contrast | exoplanet atmosphere transmission spectra; direct imaging context |
| **DSA-2000** | radio 0.70–2.00 GHz | ~3.5″ (full-band imaging) | 3.4° primary beam (1.35 GHz) | continuum, HI, polarization; FRB detection & localization; pulsar timing |
| **Argus Array** | optical g + ρ bands | ~3″ (ground, seeing-limited) | all-sky cadenced survey | variability; cadence / light-curve history; depth map at any position |
| **LFAST** | optical + near-IR | seeing-limited | fiber-fed (fixed zenith) | rapid spectroscopic follow-up; fiber throughput limited |

## Common multi-facility patterns

### Transient multi-messenger follow-up

1. **Argus** flags the transient (SurveyRun visit history, depth map sets context magnitude limit)
2. **WCC** provides precise optical position and multi-epoch photometry
3. **Lazuli IFS** classifies the transient (SN type, kilonova phase, AGN vs. stellar) via `LazuliSupernova` / `LazuliKilonova`
4. **DSA-2000** checks for radio counterpart (continuum / FRB search depending on transient class)

Handoff: WCC FITS WCS → DSA targeted pointing; Argus `run.visits(ra,dec)` → pre-trigger baseline light curve.

### FRB host galaxy identification

1. **DSA-2000** detects FRB and gives arcsecond localization
2. **WCC** images the error box (optical counterpart / host candidate)
3. **Lazuli IFS** takes a spectrum of the host candidate for redshift confirmation

ETC chain: run `dsa_etc` for FRB fluence limit → `wcc_etc.Simulation` for host imaging depth → `slicersim.lazuli_etc` for host redshift exposure time.

### Exoplanet atmosphere survey

1. **Lazuli ESC / `lazuli_exo`** simulates transmission spectrum SNR (`observe(planet, scenario, n_transits)`)
2. **WCC + `TransitModel`** estimates photometric transit precision for the same system (ingress timing, stellar variability)
3. **Argus** provides the long-baseline variability record of the host star (`run.inject(vis, stellar_model)`)

### SN Ia cosmology

1. **Argus** discovers SN candidates in survey (depth_map + inject with `sncosmo` template)
2. **WCC** provides multi-band light curve (g, r: use `wcc_etc.FluxModel` / `LightCurveSimulator`)
3. **Lazuli IFS** types the SN and measures redshift (`LazuliSupernova(model="salt", redshift=z)`)
4. **DSA-2000** constrains host galaxy HI properties (targeted HI cube)

## Comparing depth / SNR across facilities

Each tool returns SNR or limiting magnitude independently; combine them by position:

```python
# Argus depth at a position
from argus_sim.lightcurve import SurveyRun
run = SurveyRun("s3://…/argsim_5yr_…/")
dm = run.depth_map("g")        # limmag column = 5-sigma stacked depth per tile

# WCC depth for the same position, one pointing
import wcc_etc
sim = wcc_etc.Simulation(wcc_etc.Telescope(), wcc_etc.Sensor(),
                         wcc_etc.get_scene("star", mag=25, band="r"), time=300)
sim.run()
print(sim.mag_limit)           # 5-sigma point-source AB mag

# DSA continuum noise at the same sky position
import subprocess, json
result = json.loads(subprocess.check_output(
    ["python3", "-m", "dsa_etc.cli", "--ra", "150.12", "--dec", "2.21", "--json"],
    cwd="dsa/exposure-time-calculator/python"
))
print(result["full_band"]["sigma_uJy"])  # 1-sigma µJy/beam, full survey depth

# IFS exposure time for a given target SNR
import slicersim
t = slicersim.lazuli_sn_etc("salt", redshift=0.1, snr=10, x1=0, c=0)
```

## Data format handoffs

| from | to | what to pass |
|---|---|---|
| Argus `run.visits(ra,dec)` | WCC targeting | (ra, dec) in decimal degrees |
| WCC FITS image | DSA targeted obs | WCS centroid → `--ra` / `--dec` in dsa_etc CLI |
| DSA localization (arcsec) | IFS slit/spaxel | position error box; check it fits in 58×58 spaxels at 40–80 mas |
| `slicersim.LazuliSupernova` spectrum | Argus light-curve model | redshift + SALT2 params → `sncosmo` source → `run.inject` model |

## Tips

- Argus and DSA-2000 both cover the full sky at their wavelengths; WCC is pointed. Use Argus to ask "is there survey history here?" before planning WCC follow-up.
- DSA's primary beam is 3.4° FWHM at 1.35 GHz — it covers WCC's ~1 deg² field of view in one pointing.
- Lazuli IFS narrow field (40 mas spaxels, 58×58) = 2.3″ × 2.3″ on sky: fine for point sources but not for extended hosts. Switch to wide field (80 mas, 4.6″ × 4.6″) for resolved galaxies.
- LFAST is fixed at zenith — confirm the target transits through the LFAST field before scheduling it.
