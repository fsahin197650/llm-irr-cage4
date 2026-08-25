# Data and analysis code — *When Does Credit Assignment Help? A Rigorous Null Result for Large Language Model-Guided Reward Refinement in Multi-Agent Cyber Defense*

Fatih Şahin, Department of Computer Engineering, Istanbul Topkapi University
ORCID: 0000-0002-8036-3156

**DOI: [10.5281/zenodo.22077543](https://doi.org/10.5281/zenodo.22077543)**

This deposit contains every run, table and figure behind the paper, plus scripts that
regenerate the tables and figures from the raw runs alone. It is self-contained: nothing
here depends on the training codebase.

## What the paper reports

Eight reward designs — native, hand-crafted expert (SME), one-shot LLM (EUREKA-style),
iterative LLM (CARD-style), and a four-step LLM-IRR ladder — were trained under one fixed
PPO learner on the CAGE-4 enterprise scenario at three episode horizons, ten seeds each:
**240 runs**. Comparisons use reward-independent, ground-truth security metrics.

**No LLM-designed reward improves the defensive outcome at any horizon.** On the two
shorter horizons no method separates from any other. On the long horizon the hand-crafted
reward is lowest (7.25 compromised hosts [7.18, 7.32]) and the one-shot LLM reward is
significantly the worst (7.97 [7.90, 8.05]).

| tier | horizon | grand mean | between-method spread | mean seed std |
|---|---|---|---|---|
| H50 | 50 steps | 2.332 | 0.023 | 0.025 |
| H100 | 100 steps | 4.380 | 0.070 | 0.054 |
| H200 | 200 steps | 7.531 | 0.721 | 0.259 |

## Contents

Each folder is uploaded as its own archive so you can take only what you need.

| archive | size | contents |
|---|---|---|
| `raw_runs.zip` | ~1 MB | 510 per-run JSON files: 240 strong-PPO runs (all reported results) + 270 legacy-learner runs (Figures 2-3) |
| `tables.zip` | ~0.2 MB | Every table in the paper as CSV, plus `rebuild_tables.py` |
| `figures.zip` | ~2.4 MB | Figures 1-6 at 600 dpi, plus `make_figures.py` |
| `configs.zip` | ~10 KB | Experiment YAMLs, seed list, hyperparameter summary |
| `llm_cache.zip` | ~13 MB | 3,752 cached LLM prompt/response records (optional; see caveat below) |

## Tier naming

The paper labels the three difficulty tiers by horizon. All three are the *same* CAGE-4
enterprise scenario, differing only in episode length — they are not three separate
benchmarks. The internal configuration keys are preserved inside every run file.

| paper label | horizon | internal key |
|---|---|---|
| H50 | 50 steps | `cage2` |
| H100 | 100 steps | `cage4` |
| H200 | 200 steps | `cyborgpp` |

Note that `cyborgpp` here is an internal key for the 200-step tier and is unrelated to the
separately published CybORG++ gym.

## Reproducing the paper's numbers

```bash
unzip raw_runs.zip tables.zip figures.zip

cd tables  && python rebuild_tables.py --check   # recompute and diff against shipped CSVs
cd ../figures && python make_figures.py          # writes ./regenerated/
```

`rebuild_tables.py --check` reports `OK` for all seven tables. `make_figures.py` reproduces
Figures 2-6 **byte-identically** to the versions printed in the paper (verified by SHA-256).
Both need only Python 3.10+, `numpy`, and — for the figures — `matplotlib`. The bootstrap is
seeded (`seed=0`, 5,000 resamples), so confidence intervals are deterministic.

Figure 1 is a hand-drawn architecture schematic and has no generating script.

## Verifying integrity

`MANIFEST.sha256` lists a SHA-256 for every file:

```bash
sha256sum -c MANIFEST.sha256
```

## Caveats worth knowing

- **The legacy runs are included as evidence of a failure mode, not as results.** They come
  from a weak single-step REINFORCE learner whose hierarchical sub-policies were never
  trained; §5.5 of the paper explains why their apparent structure is an artifact. Strong-PPO
  runs carry `stats.learner = 1.0`; legacy runs do not.
- **`llm_cache/` is a superset.** It is the project-wide response cache, so it also holds
  entries from exploratory runs that are not part of the published results. The reward
  actually used by each published run is recorded in that run's own `final_reward_design`
  field, which is the authoritative record.
- **Shaped rewards are not comparable across methods.** `mean_reward` is present in the run
  files, but each method optimises a differently scaled objective; the paper uses it only to
  demonstrate that ranking by it is misleading (Figure 3). Use `mean_n_compromised`.
- **Every published run has `llm_failures = 0`** and executed against the real simulator;
  runs were rejected otherwise.

## Full training code

This deposit covers analysis and results. The training pipeline that produced the runs
(environment wrappers, PPO learner, LLM reward-refinement loop) lives in the project
repository referenced by the paper.

## Citing this deposit

> F. Şahin, *Data and analysis code for "When Does Credit Assignment Help? A Rigorous Null
> Result for Large Language Model-Guided Reward Refinement in Multi-Agent Cyber Defense"*,
> Zenodo, 2026. doi: 10.5281/zenodo.22077543

Please cite the accompanying paper as well.

## Licence

Data, tables and figures: CC BY 4.0. Analysis scripts: MIT. See `LICENSE.txt`.
