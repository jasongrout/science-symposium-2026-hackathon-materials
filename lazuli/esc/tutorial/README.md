# ESC hands-on: the coronagraph exposure time calculator

Requirements: `esc_etc` (plus `matplotlib`, `pandas`, `scipy` and `jupyter`), installed on the
symposium JupyterHub. Nothing needs the network. A web version of the ETC is at
<https://simulators.schmidtobservatorysystem.org/esc/>.

**Jupyter AI.** The notebooks assume the Jupyter AI chat is open in the side panel. Boxes marked 🤖
suggest prompts; boxes marked ✏️ ask you to guess a number before computing it. Two exercises contain
a deliberately broken cell.

## Tutorials

| notebook | content |
|---|---|
| [01 — The ETC in one line](01_etc_in_one_line.ipynb) | `Simulation.from_default()`, `get_snr()`, `get_int_time_for_snr()`, the count-rate budget, S/N versus time and the plateau |
| [02 — Components and filters](02_components_and_filters.ipynb) | `Telescope`, `Sensor`, `Scene`, `Coronagraph`, `update()` / `reset()`, magnitude bands and systems, the twelve filters, the aperture |
| [03 — Contrast, gain and separation](03_contrast_gain_separation.ipynb) | raw contrast, post-processing gain, the mask throughput and IWA, time to S/N 5 versus contrast, RDI |
| [04 — Frame time and saturation](04_frame_time_saturation.ipynb) | read noise per frame, whole-frame inversion, `saturation_time()`, bright hosts, the noise budget |
| [05 — Zodi and exozodi](05_zodi_and_exozodi.ipynb) | how the backgrounds are parameterized, zodis to surface brightness, the exozodi sweep |

## Exercises

| exercise | science | covers | difficulty |
|---|---|---|---|
| [1 — A Jupiter twin](exercises/ex1_jupiter_twin.ipynb) | reflected light | 01–02 | ★☆☆☆☆ |
| [2 — The detectability map](exercises/ex2_detectability_map.ipynb) | contrast and working angle | 03 | ★★☆☆☆ |
| [3 — Bright hosts and frame times](exercises/ex3_bright_hosts_frames.ipynb) | Sirius, Pollux | 04 | ★★☆☆☆ |
| [4 — The known RV giants](exercises/ex4_rv_giants.ipynb) | target list | 01–04 | ★★★☆☆ |
| [5 — Exozodi tolerance](exercises/ex5_exozodi_tolerance.ipynb) | dust | 05 | ★★★☆☆ |
| [6 — Bring your own target](exercises/ex6_bring_your_own_target.ipynb) | yours | all | open |

Solutions are in [`exercises/solutions/`](exercises/solutions/). The two science-case notebooks
these grew out of are in [`backup/`](backup/); the other Lazuli science cases (IFS and WCC) are in
[`../../science_gallery/`](../../science_gallery/).

To regenerate the notebooks: `python build_notebooks.py`, then execute them with `jupyter nbconvert`.
