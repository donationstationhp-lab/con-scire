# con-scire

*to know together*

Standalone bridge connecting AXIOM organizational health assessments with
Donation Station item lifecycle data through Power Connection numerology.

## What it does

A donation item's intake date yields a Power Connection born number. That born
number maps to a governing AXIOM dimension. The organization's health at that
dimension is the relational reading for the item. The same reading applies to
donors (by donation date) and to the organization itself (by founding date).

Each lifecycle stage is governed by specific AXIOM positions:

| Stage | Governing Positions |
|---|---|
| intake | P1 KNOWLEDGE + P9 BORN |
| qc | P2 WISDOM + P8 BUILD/DESTROY |
| storage | P4 CULTURED FREEDOM + P3 UNDERSTANDING |
| distributed | P6 EQUALITY + P7 CONSCIOUSNESS |

## Usage

```
python bridge.py                               # stage health summary
python bridge.py --station                     # bridge all items
python bridge.py --donors                      # bridge all donors
python bridge.py --item DS-0001                # one item
python bridge.py --stage distributed           # one stage
python bridge.py --origin                      # org self-referential reading
python bridge.py --origin --founded 2018-06-04 # override the founding date
python bridge.py --org assessments/file.json   # specific AXIOM assessment
python bridge.py --api http://localhost:5000   # live Donation Station API
python bridge.py --data .donation_station_data.json
```

`--origin` answers a self-referential question: does the org's founding date
map to an AXIOM position the org is currently strong at? `--founded` overrides
the founding date on the assessment (or supplies one if it's missing).

### Makefile

```
make test                        # run the test suite
make health                      # stage health summary
make station                     # bridge all items
make donors                      # bridge all donors
make origin                      # org self-referential reading
make origin FOUNDED=2018-06-04   # override the founding date
make stage STAGE=distributed     # one stage
make item ID=DS-0001             # one item
```

Every target except `test` accepts `ORG=<file>` to read a local assessment
file directly instead of hitting the Donation Station API (`API=`, defaults
to `http://localhost:5000`).

### Assessments directory

`assessments/*.json` holds AXIOM assessment files, one per organization.
`load_latest_assessment()` (used when no `--org`/`ORG=` is given) picks the
most recent file per org by filename, matching on the org's sanitized name
(e.g. `Donation_Station_HP*.json`).

Ecosystem-level rollups — multi-org files with `"type": "ecosystem"` instead
of a single `"organization"` — are named `ecosystem_*.json` and are excluded
from per-org resolution and the digest workflow, since they aren't a single
org's assessment. `load_assessment()` still normalizes them into the standard
shape on demand, so `--org assessments/ecosystem_*.json` (or `make origin
ORG=...`) works directly against an ecosystem file too.

### Weekly digest

`.github/workflows/digest.yml` runs every Monday 09:00 UTC (or on manual
dispatch) and writes one stage-health summary per org found in
`assessments/` to the workflow run's job summary.

## Dependencies

Python 3.10+ to run `bridge.py`. Tests use `pytest`. No other external
packages required.

Reads AXIOM assessment JSON files (`assessments/` by default or `--org`).
Connects to Donation Station via local data file or live API (`--api`).
