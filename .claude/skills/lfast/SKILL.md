---
name: lfast
description: LFAST (Large Fiber Array Spectroscopic Telescope) ETC. Load for questions about SNR, exposure time, or spectrograph configuration for the LFAST-20X fiber spectrograph on Kitt Peak.
---

# LFAST Exposure Time Calculator

LFAST (Large Fiber Array Spectroscopic Telescope) is a 20-telescope array on Kitt Peak. All ETC work is done in one self-contained notebook — there is no separate Python package.

**Entry point:** `lfast/ETC_schmidt_2026oct12.ipynb`

## How to use the notebook

Set parameters in **cell 3**, then re-run from that cell down. The notebook produces exposure time vs wavelength (400–1800 nm) at a target SNR per resolution element.

### Key parameters (cell 3)

| Parameter | Default | Notes |
|---|---|---|
| `T_EFF_K` | 5800 | Stellar effective temperature (K); blackbody SED |
| `V_MAG` | 15.0 | Apparent V-band magnitude (Vega) |
| `SNR_TARGET` | 50 | Target SNR per resolution element |
| `N_TELESCOPES` | 20 | 20 for the combined array; 1 for standalone 50 µm fiber |
| `GRID_MODE` | `'resel'` | `'uniform'` (fast, 1 nm grid) or `'resel'` (one point per resel, accurate) |

### Instrument choice — uncomment one block in cell 3

**WIYN Bench** (default, visible):
- `R_POW = 1800`, 400–1800 nm range, `S_opt = 0.3`, `READ_NOISE_E = 3.7`

**CSHELL** (NIR, R ~ 18 000–43 000):
- Coverage: 1.08–1.75 µm (narrow free spectral range, ~27–44 Å per setup)
- Native resolution R = 18 000; 21 500 with 200 µm slit; 43 000 with 100 µm slit

### Fiber choice

| Fiber | `FIBER_DIAMETER_ARCSEC` | `N_TELESCOPES` | Throughput files |
|---|---|---|---|
| 18 µm (default, combiner) | 1.48 | 20 | `Kopt_value_18.npy`, `Eta_fib_value_18.npy` |
| 50 µm (standalone) | 4.1 | 1 | `Kopt_value_50.npy`, `Eta_fib_value_50.npy` |

When switching fibers, change both `FIBER_DIAMETER_ARCSEC` / `N_TELESCOPES` in cell 3 **and** the `.npy` file names in cell 9.

## Custom SED

The default stellar model is a blackbody (`stellar_specific_intensity` in cell 5). To use a real spectrum, replace that function with one that returns $I_\lambda$ in W sr$^{-1}$ m$^{-2}$ nm$^{-1}$ — the calibration in cell 6 (`alpha`) handles the normalisation automatically.

Absorption lines can be injected without replacing the SED (cell 7):

```python
spectral_lines = [{'center_nm': 656.3, 'depth': 0.3, 'sigma_nm': 0.05}]
```

## Data files in `lfast/`

| File | Contents |
|---|---|
| `skytable_secz1.3.fits` | SkyCalc sky radiance and transmission, airmass 1.3, altitude 2640 m |
| `Kopt_value_{18,50}.npy` | Wavelength-dependent optical throughput for each fiber size |
| `Eta_fib_value_{18,50}.npy` | Point-source fiber injection efficiency for each fiber size |
| `Kopt_wavelength.npy`, `Eta_fib_wavelength.npy` | Wavelength axes for the above |
