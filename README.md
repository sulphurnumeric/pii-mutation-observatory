# PII Mutation Observatory

PII Mutation Observatory is an interactive developer tool for exploring how cooperative
prompt-mutation agents change the confidence of a PII detector. It visualizes every input,
agent strategy, accepted or blocked mutation, inherited path, and final comparison against
the detector's expected performance on the demo inputs.

## Build Week category

**Developer Tools** — the project helps model and safety engineers inspect classifier
robustness, mutation lineage, and agent cooperation.

## What it does

- Configures four or six mutation agents with one or more strategies per agent.
- Animates agents applying transformations to known-positive PII examples.
- Pauses for an explainable detector verdict after every attempted mutation.
- Shares the strongest successful strategy between agents at round boundaries.
- Preserves complete accepted and blocked mutation paths.
- Offers **Skip to Finish**, which computes missing attempts without discarding completed work.
- Ranks agents and compares expected baseline detection with post-agent outcomes.
- Includes a Python experiment backend using `Roblox/roblox-pii-classifier`, FLAN-T5, and
  `Roblox/RobloxGuard-Eval`.

## Quick demo

The standalone browser demo has no build step:

```bash
python -m http.server 8000
```

Open <http://localhost:8000/pii-swarm-demo-enhanced.html>.

## Python experiment backend

```bash
cd 👑
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python main.py --experiments 1 --agents 4 --rounds 1 --output latest_run.json
```

The first run downloads the Roblox PII classifier, FLAN-T5, and RobloxGuard-Eval. CUDA is
used when available; otherwise the backend runs on CPU.

## Automated walkthrough and video

```bash
npm install
npx playwright install chromium
npm run test:demo
npm run record:demo
```

The browser recording is written to `artifacts/demo/`. See
[`submission/DEMO_SCRIPT.md`](submission/DEMO_SCRIPT.md) for the under-three-minute narration.

## Codex and GPT-5.6

Codex was used as the primary engineering collaborator to inspect the inherited prototype,
debug strategy attribution, redesign the agent configuration, implement Skip to Finish,
add final performance analysis, create browser automation, and prepare the submission.

The repository contains an optional Responses API adapter and a reusable GPT-5.6 mutation
instruction in `👑/main.py`. For the checked-in demo run, GPT-5.6 performed six semantic
mutations in the primary Codex session. The results and real Roblox classifier scores are
recorded in `gpt56_codex_run.json`; `npm run test:codex-run` validates identifier preservation
and the artifact schema. This is session inference, not an API call, and the UI labels it as
such. The remaining browser strategies use the deterministic teaching simulator.

## Validation scope

The HTML is a deterministic teaching simulation with six known-positive inputs and a simple
explainable detector. Its “expected detection performance” is not dataset-wide accuracy,
precision, recall, or F1. The Python backend is the path for running the real classifier and
dataset; exported metrics should only be claimed after a labeled evaluation has run.

The checked-in `classifier_metrics.json` was produced from the real classifier using all 55
`Sharing Personal Information` positives and 110 deterministic seeded negatives:

```bash
source .venv/bin/activate
python scripts/evaluate_classifier.py --max-negatives 110 --batch-size 64 \
  --output classifier_metrics.json
```

It reports the full test-set prevalence separately from stratified evaluation metrics and
includes accuracy, balanced accuracy, precision, recall, specificity, F1, mean scores, and
confusion counts. Omit `--max-negatives` to evaluate all 2,873 rows; CPU execution is slow.

## Repository layout

- `pii-swarm-demo-enhanced.html` — standalone interactive visualization.
- `👑/main.py` — classifier-backed cooperative experiment runner.
- `👑/latest_run.json` — sample backend export.
- `scripts/record-demo.mjs` — automated visual walkthrough and recorder.
- `scripts/test-demo.mjs` — browser smoke test for configuration, skipping, and reports.
- `submission/` — Devpost copy, narration, and final authenticated-action checklist.

## Security and privacy

Use synthetic or authorized test data only. Never commit API keys, Codex authentication
files, real personal data, or private dataset credentials.
