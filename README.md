# ARTS_HysCal – automated calibration of OpenSees hysteretic material models to cyclic test data

ARTS_HysCal calibrates the **HystereticSM**, **Pinching4** and **DowelType** uniaxial materials of OpenSees to a measured
force–displacement or moment–rotation history through a graphical interface, without programming. A candidate material is
driven through the measured loading protocol in OpenSees and scored with a composite objective of the normalised force error
(NRMSE<sub>F</sub>), the cumulative dissipated-energy error (ε<sub>E</sub>) and the reversal-peak error (ε<sub>P</sub>);
initial values are estimated from the test itself; the search uses differential evolution (DE) or a genetic algorithm (GA)
followed by a local refinement. The method and its validation are described in:

> H. D. Nguyen, Q. Mei, Y. H. Chui, *ARTS_HysCal: an automated tool for calibrating OpenSees hysteretic material models to
> cyclic test data*, Advances in Engineering Software (submitted). <!-- update with volume/DOI when available -->

Developed by Hoang D. Nguyen, ARTS Group, University of Alberta (dachoang@ualberta.ca).

## Download the tool

Standalone applications (no Python installation needed) are in the [`release/`](release) folder:

| Platform | File | Notes |
|---|---|---|
| macOS (Apple Silicon, M1 or later) | [`ARTS_HysCal_macOS.zip`](https://github.com/HoangNguyen0712/ARTS_HysCal/raw/main/release/ARTS_HysCal_macOS.zip) (81 MB) | see *First start on macOS* below |
| Windows 10/11 (64-bit) | coming soon | |

**First start on macOS.** Unzip the file and move `ARTS_HysCal.app` to *Applications* (optional). The app is not signed with an
Apple Developer ID, so the first time right-click the app → **Open** → **Open**. If macOS reports that the app "is damaged" or
"cannot be opened", run once in Terminal (in the folder that contains the app):

```
xattr -cr ARTS_HysCal.app
```

This version runs until **8 April 2027**; an updated version will be posted here.

The source code is not distributed.

## Contents of this repository

```
release/                   the standalone applications
data/                      the three cyclic tests used in the paper (two columns: displacement [mm], force [kN])
results/
  T1/, T2/, T3/            the calibrations of Table 2: 3 material models x {DE, GA}, Balanced preset, 100 generations
  T1_preset/               the four calibrations of Table 3: HystereticSM on T1, DE, 30 generations, one folder per preset
  table3.csv               Table 2 of the paper (fit metrics of the nine calibrations and the GA repeats)
  table4.csv               Table 3 of the paper (HystereticSM on T1 with the four objective presets)
  figures/                 Figs. 5-8 of the paper (PDF, PNG and SVG)
```

In the CSV tables, *NRMSE* is NRMSE<sub>F</sub> and *Peak error* is the reversal-peak error ε<sub>P</sub> of the paper, both in % of
the largest measured force.

### Tests

| Test | File | Specimen | Displacement | Peak force | Character |
|---|---|---|---|---|---|
| T1 | `data/T1_ExperimentalData_65.csv` | riveted glulam connection, RC2 (Popovski and Symons, 2003) | ±34 mm | +144 / −149 kN | strongly pinched, large cyclic strength loss |
| T2 | `data/T2_ExperimentalData_95R.csv` | bolted glulam connection reinforced with self-tapping screws, BC2 (Chen et al., 2019) | ±52 mm | +248 / −243 kN | strongly pinched, 11 amplitude levels |
| T3 | `data/T3_HSSWCase2b_2.txt` | light wood-frame shear wall, Wall 2b (Derakhshan et al., 2022) | ±115 mm | +76 / −70 kN | mildly pinched, no cyclic strength loss |

### Files of one calibration

Each run folder holds the files the tool writes, named `<model>_<optimizer>_we<w_e>_wp<w_p>_gen<generations>_...`
(`HySM` = HystereticSM, `P4` = Pinching4, `DT` = DowelType):

| File | Content |
|---|---|
| `*_BestParameters.txt` | calibrated parameters as OpenSees `uniaxialMaterial` arguments; last line: objective J, NRMSE<sub>F</sub>, energy ratio, ε<sub>P</sub> |
| `*_SimulationData.txt` | simulated displacement and force at every protocol point (first row = initial state) |
| `*_History_Experiment.txt`, `*_History_Simulation.txt` | cumulative displacement, force and displacement of the test (thinned to the protocol) and of the model |
| `*_Engery_Experiment.txt`, `*_Engery_Simulation.txt` | cumulative displacement and cumulative dissipated energy |
| `*_Engery_CumTrap.txt` | total dissipated energy of test and model |
| `*_Convergence.txt` | best objective after each generation (last line: after the local refinement) |
| `*_Summary.txt` | settings, run time and all fit metrics of the run |
| `run.json` | the same, machine-readable, including the initial and calibrated parameter vectors |

## Reproducing the paper's results with the tool

1. Open the panel of the material model, load a data file from `data/`, select *Force-Displacement* and press
   **Protocol from Data** (default step: 0.5 % of the largest displacement).
2. Press **Estimate from Data**; do not edit the values.
3. Objective preset **Balanced** (w<sub>e</sub> = 0.5, w<sub>p</sub> = 1), optimiser **DE**, 100 generations, *Cores* = all,
   then **Run Calibration**. For Table 3, use HystereticSM on T1 with 30 generations and each of the four presets.

The runs in the paper were made on a 12-core Apple M4 Pro with *Cores* = all; DE then evaluates each generation in parallel and
updates the population once per generation, so a single-core run gives slightly different numbers with the same seed.
Software: OpenSeesPy 3.8.0, SciPy 1.18.0, Python 3.14.

## Citing

If you use the tool or these data, please cite the paper above and, for the genetic-algorithm procedure it builds on:

> H. D. Nguyen, Q. Mei, Y. H. Chui, A genetic algorithm-based calibration procedure for hysteresis loops in timber structures,
> Engineering Structures 346 (2026) 121622. https://doi.org/10.1016/j.engstruct.2025.121622

## Licence

Data and results in this repository: CC BY 4.0 <!-- confirm -->. The application is distributed under its own terms (see the About panel).
