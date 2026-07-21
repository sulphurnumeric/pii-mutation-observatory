# PII Mutation Observatory

PII Mutation Observatory is an interactive developer tool for exploring how cooperative
prompt-mutation agents change the confidence of a PII detector. It visualizes every input,
agent strategy, accepted or blocked mutation, inherited path, and final comparison against
the detector's expected performance on the supplied inputs.

## What it does

* Configures four or six mutation agents with one or more strategies per agent.
* Animates agents applying transformations to known-positive PII examples.
* Pauses for an explainable detector verdict after every attempted mutation.
* Shares the strongest successful strategy between agents at round boundaries.
* Preserves complete accepted and blocked mutation paths.
* Offers **Skip to Finish**, which computes missing attempts without discarding completed work.
* Ranks agents and compares expected baseline detection with post-agent outcomes.
* Includes a Python experiment backend using `Roblox/roblox-pii-classifier`, FLAN-T5, and
  `Roblox/RobloxGuard-Eval`.

## Browser interface

The standalone browser interface has no build step:

```bash
python -m http.server 8000
```

Open http://localhost:8000/pii-swarm-demo-enhanced.html.

## Python experiment backend

The included `👑.zip` archive must be extracted before running the Python backend.

After extraction:

```bash
cd 👑
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python main.py --experiments 1 --agents 4 --rounds 1 --output latest_run.json
```

On Windows PowerShell, activate the virtual environment with:

```powershell
.venv\Scripts\Activate.ps1
```

The first run downloads the Roblox PII classifier, FLAN-T5, and RobloxGuard-Eval. CUDA is
used when available; otherwise the backend runs on CPU.

## Codex and GPT-5.6

Codex was used as the primary engineering collaborator to inspect the inherited prototype,
debug strategy attribution, redesign the agent configuration, implement Skip to Finish,
add final performance analysis, and improve the overall project structure.

The repository contains an optional Responses API adapter and a reusable GPT-5.6 mutation
instruction in `👑/main.py`.

GPT-5.6 performed six semantic mutations during development. The results and real Roblox
classifier scores are recorded in `gpt56_codex_run.json`.

The generated mutations preserve the identifiers required for classifier testing while
applying semantic and structural transformations to the surrounding text.

## Validation scope

The browser interface is a deterministic teaching simulation with six known-positive inputs
and a simple explainable detector. Its “expected detection performance” is not dataset-wide
accuracy, precision, recall, or F1.

The Python backend runs the real classifier and dataset. Exported evaluation metrics should
only be treated as classifier performance results after a labeled evaluation has completed.

The checked-in `classifier_metrics.json` was produced from the real classifier using all 55
`Sharing Personal Information` positives and 110 deterministic seeded negatives:

```bash
source .venv/bin/activate
python scripts/evaluate_classifier.py --max-negatives 110 --batch-size 64 \
  --output classifier_metrics.json
```

On Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
python scripts/evaluate_classifier.py --max-negatives 110 --batch-size 64 --output classifier_metrics.json
```

The results include:

* Accuracy
* Balanced accuracy
* Precision
* Recall
* Specificity
* F1 score
* Mean classifier scores
* Confusion-matrix counts
* Full test-set prevalence

Omit `--max-negatives` to evaluate all 2,873 rows. CPU execution may be slow.

## Repository layout

* `pii-swarm-demo-enhanced.html` — standalone interactive visualization.
* `👑.zip` — compressed Python backend; extract it before use.
* `👑/main.py` — classifier-backed cooperative experiment runner.
* `👑/latest_run.json` — sample backend export.
* `gpt56_codex_run.json` — GPT-5.6 mutation results and classifier scores.
* `classifier_metrics.json` — saved real-classifier evaluation metrics.
* `scripts/evaluate_classifier.py` — labeled classifier evaluation utility.
* `requirements.txt` — Python dependencies.

## Security and privacy

Use synthetic or authorized test data only.

Never commit:

* API keys or `.env` files
* Codex authentication files
* Real personal information
* Private dataset credentials
* Model access tokens
* Sensitive experiment outputs
