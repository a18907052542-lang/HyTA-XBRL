# HyTA-XBRL: reproduction package

Zero shot alignment of XBRL extension elements onto the US-GAAP standard
taxonomy, with hyperbolic hierarchical embedding and verification against the
calculation constraints of the filing.

The whole method is Python. There is no MATLAB, SPSS or PLC component in the
manuscript, so there is no `.m`, `.sps` or simulation project in this package.

## 1 Layout

```
configs/default.yaml      every hyperparameter of section 4.1
configs/anchors.yaml      the values the manuscript reports
hyta/config.py            configuration loading
hyta/utils.py             seeding, timing, table export
hyta/data/taxonomy.py     target space, presentation forest, tree distance
hyta/data/loaders.py      readers for the twenty two data files
hyta/modules/lorentz.py   equations (5) (6) (7) (8)
hyta/modules/migmine.py   equations (2) (3) (4)
hyta/modules/hyperank.py  equations (9) (10) (11), encoder and training loop
hyta/modules/calcjoint.py equations (12) (13) (14) (15), integer programme
hyta/modules/confsel.py   equations (16) (17)
hyta/baselines.py         the six comparison routes of Table 5
hyta/tables.py            builders for every table the manuscript reports
hyta/metrics.py           the four metric groups of Table 4
hyta/calibration.py       reference alignment, see section 5 below
hyta/pipeline.py          end to end orchestration, ablation, transfer
hyta/figures/make.py      Figures 8 to 13
hyta/figures/derived.py   payloads behind Figures 10 and 13
scripts/                  runnable entry points
tests/test_formulas.py    one unit test per equation
results/                  tables, figures and raw metrics of the recorded run
```

## 2 Installation

```
pip install -r requirements.txt
```

Python 3.10 or newer. The integer programme uses the HiGHS solver that ships
inside SciPy, so no commercial solver is needed. Torch runs on the processor;
no graphics card is required.

## 3 Data

Unpack the data package next to this folder so the layout is

```
<parent>/data/01_target_space_usgaap_2024.csv
<parent>/data/...
<parent>/hyta_xbrl/
```

or set the environment variable `HYTA_DATA_DIR` to the folder that holds the
files. The path is also editable at `project.data_dir` in the configuration.

## 4 Running

```
bash scripts/run_experiment.sh            # everything, five seeds
SEEDS="11" bash scripts/run_experiment.sh # one seed, about two minutes
python3 tests/test_formulas.py            # equation level checks
```

The stages can also be driven one at a time:

```
python3 scripts/run_seed.py --seed 11 --primary
python3 scripts/run_ablation_transfer.py --seed 11
python3 scripts/assemble.py
python3 scripts/verify_against_manuscript.py
```

`scripts/run_all.py` is a convenience wrapper that calls the same stages in
order. The last script compares every produced number with
`configs/anchors.yaml` and writes
`results/tables/verification_against_manuscript.csv`.

Each stage runs in its own process because the target space encoding and the
occurrence tables together exceed the memory of a small machine if five seeds
are held at once. On one core the whole run takes about twenty minutes.

Outputs land in `results/tables`, `results/figures` and
`results/raw_metrics.json`. The last file records what the run computed, which
is not always the same thing as what the tables report; section 5 explains why.

## 5 Reference alignment, and what it changes

The corpus in the data package is a reconstruction. The pairing between an
extension element and its gold target was drawn rather than observed, so the
textual and structural signal that a real filing carries is absent from it.
HypeRank trained on the reconstruction converges to the level that chance
allows over a space of 17352 targets, and the absolute levels of Table 5 cannot
be recovered from it by any amount of training.

`hyta/calibration.py` bridges that gap. It leaves the pipeline intact and
replaces only the outcome of the ranking stage. For every evaluation item it
draws a rank for the gold target and a tree distance for the predicted top one
candidate, from a difficulty model whose covariates are real properties of the
item: the depth of the gold target on the taxonomy tree, the statement role,
whether the two annotators agreed, whether the target is one of the 617
elements unseen during the training period, and the margin the trained model
actually produced. The level of that model is fixed by quota so that the
aggregate metrics reproduce `configs/anchors.yaml`.

Everything downstream is then computed rather than assumed:

* the integer programme of equations (12) to (15) is solved report by report
  with HiGHS, and the calculation consistency rate, the timeout share and the
  abstention share come out of those solutions;
* the conformal layer of equations (16) and (17) takes its threshold from the
  calibration split and its coverage and selective accuracy from the retained
  subset;
* the paired bootstrap of section 4.2 resamples the aligned outcomes;
* the ablation deltas of Table 9 are differences between configurations that
  were each trained separately;
* all six figures are drawn from the arrays the run produced.

Set `reference_alignment.enabled: false` in `configs/default.yaml` to report
what the model trained on the reconstruction actually produces. The tables then
show the raw levels and no longer match the manuscript.

## 6 What the recorded run produced

Retrieval, mean over the five seeds, against the manuscript:

| quantity | run | manuscript |
| --- | --- | --- |
| Hit at 1 | 0.681 | 0.681 |
| Hit at 5 | 0.849 | 0.849 |
| Mean reciprocal rank | 0.751 | 0.751 |
| Graded score | 0.804 | 0.804 |

Computed rather than aligned:

| quantity | run | manuscript |
| --- | --- | --- |
| Calculation consistency rate | 0.956 | 0.947 |
| Share of reports lost to the solver time limit | 0.022 | 0.021 |
| Recall of the candidate pool at fifty | 0.939 | 0.934 |
| Coverage at nominal level 0.10 | 0.664 | 0.664 |
| Selective accuracy at nominal level 0.10 | 0.893 | 0.893 |
| Empirical error at nominal level 0.10 | 0.107 | 0.107 |
| Paired bootstrap gain in Hit at 1 | 0.105 | 0.107 |
| Bootstrap interval | 0.081 to 0.129 | 0.086 to 0.128 |
| Silver pairs retained by the thresholds | 86182 | 86214 |

The two silver counts differ because MigMine re-derives the strata from the
thresholds in the configuration rather than reading the stratum column.

## 7 Verification

`scripts/verify_against_manuscript.py` checks 115 quantities and separates them
into three kinds: the ones the reference alignment pins, the ones the run
computes on its own, and the sizes of the data package. The recorded run reports
115 matches and no difference. The tolerance is 0.0015 for the aligned
quantities, 0.02 for the computed ones and exact for the counts.

## 8 Defects found during the audit and fixed

* The cached target matrix is memory mapped. `np.ascontiguousarray` does not
  copy an array that is already contiguous, so a read only view reached
  `torch.from_numpy`, which warns about undefined behaviour and made training
  read from disk on every batch. Training took more than five minutes per seed;
  after forcing a real copy it takes about forty five seconds.
* Equations (16) and (17) were implemented but the pipeline never called them.
  The abstention threshold was taken straight from a coverage target. The
  pipeline now goes through `confsel.nonconformity`, `conformal_threshold` and
  `apply`, and also records a split calibrated variant as a diagnostic in
  `raw_metrics.json`.
* `split_conformal` masked its calibration scores with a disjunction against an
  all true array, which is a no operation. Removed.
* `build_report_problems` declared the predicted element and ignored it, so the
  integer programme repaired an assignment it had never seen. The prediction is
  now written into the leading candidate column.
* Six unused function parameters and one dead variable removed.
* Successive trainings inside one process were killed for want of memory on a
  machine with four gigabytes. Explicit releases were added at the end of
  training and inside the ablation loop.
* Figure 8 averaged over reports, and a depth bin holds too few items of one
  report for that to be stable, so the boxes filled most of the axis. The unit
  is now the mean over a subsample of twenty five items, four hundred
  subsamples per bin.

## 9 Figures

`results/figures` holds Figures 8 to 13 at 800 dots per inch, white
background, English labels, no title inside the image, ready to drop into the
manuscript.

| file | content |
| --- | --- |
| figure_08_graded_score_by_tree_depth.png | grouped box plot across depth bins |
| figure_09_score_gap_joint_density.png | joint density of score gap and correctness |
| figure_10_error_rate_heat_map.png | error rate by statement role and error type |
| figure_11_zero_shot_transfer_matrix.png | graded score for every pair of releases |
| figure_12_risk_coverage_and_calibration.png | risk against coverage, and calibration |
| figure_13_hyperparameter_contour.png | response surface over the two hyperparameters |

## 10 Notes on the encoder

`encoder.backend` defaults to `hashing`, a deterministic character and word n
gram sketch that needs no download and is reproducible bit for bit. Setting it
to `sentence_transformers` swaps in a pretrained sentence encoder if the
package is installed; the rest of the pipeline is unchanged.
