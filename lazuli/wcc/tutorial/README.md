# WCC hands-on: simulating Wide-field Context Camera observations

Requirements: `wcc_etc`, `wcc_sim` and `wcc_phot` (plus `matplotlib` and `jupyter`), installed on
the symposium JupyterHub. The Gaia fields used by notebook 06 and exercises 5 and 6 are cached in
`gaia_cache/`, so no network access is needed. A web version of the ETC is at
<https://simulators.schmidtobservatorysystem.org/wcc/>.

**Jupyter AI.** The notebooks assume the Jupyter AI chat is open in the side panel. Boxes marked 🤖
suggest prompts; boxes marked ✏️ ask you to guess a number before computing it. Two exercises contain
a deliberately broken cell.

## Tutorials

| notebook | content |
|---|---|
| [01 — The ETC in one line](01_etc_in_one_line.ipynb) | `get_scene()`, `Simulation.from_sensorfilter()`, `get_image_snr()`, `get_image_exptime_for_snr()`, sweeps, the 5 sigma depth, the sensor:filter labels |
| [02 — The detector](02_detector_saturation_reads.ipynb) | the 16 ke- well, peak pixel, `is_saturated()`, `n_reads`, the noise budget, the bright limit, `update_sensor()` |
| [03 — Scenes](03_scenes_spectra_hosts.ipynb) | stellar templates and magnitude systems, parametric spectra, your own spectrum, the background, Sersic and NGC hosts, redshift |
| [04 — PSF, apertures, images](04_psf_apertures_images.ipynb) | `ImageSimulator`, the aperture and `optimize=True`, pointing jitter, the defocused modes `zwo:r+1` and `zwo:bb2` |
| [05 — Light curves](05_light_curves.ipynb) | `TransitModel`, `LightCurveSimulator`, binning, reads per point for bright hosts |
| [06 — Fields and photometry](06_fields_and_photometry.ipynb) | `simulate_field()` on a Gaia field, the truth catalogue, `run_photometry()`, `centroid_stars()`, FITS output |

## Exercises

Each is one science case. Pick the one closest to your own science; ex1 to ex4 need the ETC only,
ex5 and ex6 render full frames (a few minutes, ~1 GB of memory), ex7 is open-ended.

| exercise | science | covers | difficulty |
|---|---|---|---|
| [1 — A transient on a host galaxy](exercises/ex1_transient_on_host.ipynb) | TDAMM | 01–03 | ★☆☆☆☆ |
| [2 — Transit photometry of a bright star](exercises/ex2_transit_precision.ipynb) | exoplanets | 02, 05 | ★★☆☆☆ |
| [3 — Deep-field depth and stacking](exercises/ex3_deep_field_depth.ipynb) | cosmology / galaxies | 01, 02 | ★★☆☆☆ |
| [4 — Which H-alpha filter?](exercises/ex4_halpha_filter_choice.ipynb) | ISM / nearby galaxies | 03 | ★★★☆☆ |
| [5 — Recovering an AGN flare](exercises/ex5_agn_flare_recovery.ipynb) | TDAMM | 06 | ★★★★☆ |
| [6 — Astrometry in a crowded field](exercises/ex6_crowded_field_astrometry.ipynb) | stellar | 06 | ★★★★☆ |
| [7 — Bring your own target](exercises/ex7_bring_your_own_target.ipynb) | yours | all | open |

Solutions are in [`exercises/solutions/`](exercises/solutions/). The original science-case gallery
notebooks these exercises grew out of are in [`backup/`](backup/).

To regenerate the notebooks: `python build_notebooks.py`, then execute them with `jupyter nbconvert`.
