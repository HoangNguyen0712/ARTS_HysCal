# ARTS_HysCal – automated calibration of OpenSees hysteretic material models to cyclic test data

ARTS_HysCal calibrates the **HystereticSM**, **Pinching4** and **DowelType** uniaxial materials of OpenSees to a measured
force–displacement or moment–rotation history through a graphical interface, without programming. A candidate material is
driven through the measured loading protocol in OpenSees and scored with a composite objective of force error, cumulative-energy
error and peak-force error; initial values are estimated from the test itself; the search uses differential evolution or a
genetic algorithm followed by a local refinement. The method and its validation are described in:

> H. D. Nguyen, Q. Mei, Y. H. Chui, *ARTS_HysCal: an automated tool for calibrating OpenSees hysteretic material models to
> cyclic test data*, Advances in Engineering Software (submitted). <!-- update with volume/DOI when available -->

Developed by Hoang D. Nguyen, ARTS Group, University of Alberta (dachoang@ualberta.ca).

## Download the tool

Standalone applications (no Python installation needed):

| Platform | File | Notes |
|---|---|---|
| macOS (Apple Silicon) | `HysteresisCalibrationTool.app` – see **Releases** → <!-- link --> | first start: right-click → Open |
| Windows 10/11 (64-bit) | `HysteresisCalibrationTool.zip` – see **Releases** → <!-- link --> | unzip, run `HysteresisCalibrationTool.exe` |

<!-- If the executables are hosted elsewhere (Zenodo, group web page), put the link and DOI here. -->

The user guideline (theory, methodology, step-by-step use, troubleshooting) is in [`docs/`](docs/).
The source code is not distributed.

## Contents of this repository

```
data/                      the three cyclic tests used in the paper (two columns: displacement [mm], force [kN])
results/
  T1/, T2/, T3/            all calibrations of Table 2: 3 material models x {DE, GA}, Balanced preset, 100 generations
  T1_presets/              the four calibrations of Table 3: HystereticSM, DE, 30 generations, one folder per preset
  Table2_fit_metrics.csv   Table 2 of the paper
  Table3_objective_presets.csv   Table 3 of the paper
  test_descriptors.csv     the descriptors of the three tests quoted in Section 4.1
  figures/                 Figs. 3 and 5-8 (PDF and PNG)
docs/                      user guideline
```

### Tests

| Test | File | Displacement | Peak force | Character |
|---|---|---|---|---|
| T1 | `data/T1_ExperimentalData_65.csv` | ±34 mm | +144 / −150 kN | strongly pinched, large cyclic strength loss, asymmetric |
| T2 | `data/T2_ExperimentalData_95R.csv` | ±52 mm | +248 / −243 kN | strongly pinched, 11 amplitude levels |
| T3 | `data/T3_HSSWCase2b_2.txt` | ±115 mm | +76 / −70 kN | mildly pinched, no cyclic strength loss |

<!-- add one line per test with the specimen description and the reference of the test programme -->

### Files of one calibration

Each run folder holds the files the tool writes, named `<model>_<optimizer>_we<w_e>_wp<w_p>_...`
(`HySM` = HystereticSM, `P4` = Pinching4, `DT` = DowelType):

| File | Content |
|---|---|
| `*_BestParameters.txt` | calibrated parameters as OpenSees `uniaxialMaterial` arguments; last line: objective J, NRMSE, energy ratio, peak error |
| `*_SimulationData.txt` | simulated displacement and force at every protocol point (first row = initial state) |
| `*_History_Experiment.txt`, `*_History_Simulation.txt` | cumulative displacement, force and displacement of the test (thinned to the protocol) and of the model |
| `*_Engery_Experiment.txt`, `*_Engery_Simulation.txt` | cumulative displacement and cumulative dissipated energy |
| `*_Engery_CumTrap.txt` | total dissipated energy of test and model |
| `*_Convergence.txt` | best objective after each generation (last line: after the local refinement) |
| `*_Summary.txt` | settings, run time and all fit metrics of the run |
| `run.json` | the same, machine-readable, including the initial and calibrated parameter vectors |

## Reproducing the paper's results with the tool

1. Load a data file from `data/`, select *Force-Displacement*, press **Protocol from Data** and accept the proposed step.
2. Press **Estimate from Data**; do not edit the values.
3. Objective preset **Balanced** (w_e = 0.5, w_p = 1), optimizer **DE**, 100 generations, then **Run Calibration**.

The runs in the paper were made on a 12-core Apple M4 Pro with the *Cores* setting at "all"; DE then evaluates each generation
in parallel and updates the population once per generation, so a single-core run gives slightly different numbers with the
same seed (Section 4.3 of the paper). Software: OpenSeesPy 3.8.0, SciPy 1.18.0, Python 3.14.

## Citing

If you use the tool or these data, please cite the paper above and, for the genetic-algorithm procedure it builds on:

> H. D. Nguyen, Q. Mei, Y. H. Chui, A genetic algorithm-based calibration procedure for hysteresis loops in timber structures,
> Engineering Structures 346 (2026) 121622. https://doi.org/10.1016/j.engstruct.2025.121622

## Licence

Data and results in this repository: CC BY 4.0 <!-- confirm -->. The application is distributed under its own terms (see the About panel).
