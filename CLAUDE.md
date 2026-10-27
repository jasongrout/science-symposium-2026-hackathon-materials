# Science Symposium 2026 — Hackathon Materials

Notebooks, simulators, and sample data for the Schmidt Observatory System (TSOS) Science Symposium 2026 hackathon. The four observatories are **Lazuli** (space telescope: WCC wide-field camera + IFS slicer spectrograph + ESC coronagraph), **DSA-2000** (radio array), **Argus Array** (wide-field photometric survey), and **LFAST** (liquid-mirror telescope).

## Directory layout

| directory | observatory | content |
|---|---|---|
| `lazuli/wcc/` | Lazuli WCC | Tutorial notebooks for the wide-field context camera ETC and image simulator |
| `lazuli/ifs/` | Lazuli IFS | Tutorial notebooks for the slicer integral-field spectrograph |
| `lazuli/esc/` | Lazuli ESC | Coronagraph science case notebooks |
| `dsa/hackathon2026/` | DSA-2000 | HI cube, FRB mocks, pulsar timing notebooks |
| `dsa/exposure-time-calculator/` | DSA-2000 | Sensitivity calculator: web page + Python package (`dsa_etc`) |
| `argus/argus_survey_model/` | Argus Array | Survey simulator (`argus_sim`): sky coverage, depth, light curves |
| `lfast/` | LFAST | ETC notebook and fiber throughput tables |

## Key installed packages

### Lazuli WCC
- **`wcc_etc`** — exposure time calculator; entry point `Simulation(telescope, sensor, scene, time)`. Skills: `wcc-etc`.
- **`wcc_sim`** — end-to-end pixel-level image simulator; entry point `WCCObservation` / `simulate_field`. Skills: `wcc-sim`.

### Lazuli IFS (slicer)
- **`slicersim`** — IFS observation simulator; high-level entry points `LazuliSupernova`, `LazuliKilonova`, `LazuliTarget`, `lazuli_etc`, `lazuli_sn_etc`. Skills: `lazuli-ifs`.

### Lazuli ESC (coronagraph)
- **`lazuli_exo`** — exoplanet transmission spectroscopy ETC; high-level entry points `lazuli_exo.observe()`, `lazuli_exo.simulate()`, `list_planets()`, `list_scenarios()`. Skills: `lazuli-exo`.

### DSA-2000
- **`dsa_etc`** — sensitivity calculator CLI and Python API. Has its own `CLAUDE.md` at `dsa/exposure-time-calculator/CLAUDE.md` and a skill at `dsa/exposure-time-calculator/skill/dsa-sensitivity/SKILL.md`.

### Argus Array
- **`argus_sim`** — survey simulator. Source in `argus/argus_survey_model/src/argus_sim/`.

### Shared data
- **`lazuli_astrosim_data`** — bundled throughput curves, PSF files, and reference spectra used by `wcc_etc`, `wcc_sim`, and `slicersim`.
- S3 bucket `schmidt-observatory-system` is pre-mounted at `~/s3` on the symposium JupyterHub.
- Lazuli notebooks set `os.environ['SNCOSMO_DATA_DIR']` at the beginning to point to the read-only S3 bucket containing pre-downloaded data needed by `sncosmo`. If you need to download more filters, bandpasses, spectra, etc. to answer a user query, you should `cp -r ~/s3/schmidt-observatory-system/nov26-symposium/sncosmo_data_dir/ ~/` and write new files to that local user-level directory, and then update `$SNCOSMO_DATA_DIR` for your notebook.

## Notebooks and tutorials

Each instrument's tutorial notebooks are the best first reference:
- WCC: `lazuli/wcc/tutorial/01_tdamm_transient_on_host.ipynb` through `07_ism_halpha_emission_line.ipynb`
- IFS: `lazuli/ifs/tutorial/01_exposure_time_calculator.ipynb` through `06_cubes_and_fields.ipynb`
- ESC: `lazuli/esc/01_k2-18b_trappist-1e_atmospheres.ipynb` through `13_exozodi_tolerance.ipynb`

## Environment

- Python 3.13, conda env `notebook`
- All packages installed in `/srv/conda/envs/notebook/lib/python3.13/site-packages/`
- Web-based calculators also available at `https://simulators.schmidtobservatorysystem.org/`
