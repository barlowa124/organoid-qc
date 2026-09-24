# organoid-qc

Transcriptomic fidelity scoring for organoid cultures: how faithfully does a
scRNA-seq profile of an organoid reproduce the cell-type composition and
marker expression of the human tissue it is meant to model?

Standard organoid QC is manual and qualitative — a few marker stains, a
microscopy read. This pipeline makes it quantitative and reproducible:
cluster the organoid, correlate each cluster against reference tissue
cell-type centroids, and report **per-population** fidelity signals —
coverage (missing cell types), unmapped fraction (aberrant populations), and
marker detection (immature phenotypes) — plus QC flags against config'd
thresholds.

## Quickstart

```bash
uv venv --python 3.11 && uv pip install -e ".[dev]"
.venv/bin/python -m snakemake --cores 2
```

Runs the deterministic **demo dataset** (synthetic organoid with known
ground truth: one missing cell type, one aberrant population) and writes:

- `results/fidelity.json` — per-cluster mappings + aggregate scores + flags
- `results/umap.png` — joint embedding, organoid cells colored mapped/unmapped

To score real data, set `dataset.mode: h5ad` in `config/config.yaml` and
point it at annotated `.h5ad` files — see `docs/data-sources.md` for
CELLxGENE/GEO access notes.

## Demo result

The demo culture intentionally fails QC, which is the point — the pipeline
recovers the planted failure modes: `celltype_coverage` 0.667 vs the 0.75
threshold (stem-like population missing), `unmapped_fraction` 0.25 (one
aberrant cluster, correlation -0.02 to every reference centroid), stem
marker detection 0.0. `qc_pass: false`.

## Interpretation

Fidelity ≠ function. A passing score means the transcriptome composition
matches the reference panel; it says nothing about barrier function,
secretion, or drug response. See `docs/evaluation.md` for the full list of
what a pass does not mean and known failure modes.

## Structure

```
config/config.yaml      markers, thresholds, dataset mode — single source of truth
workflow/Snakefile      demo_data/load -> preprocess -> score -> report
src/organoid_qc/
  data/synthetic.py     deterministic synthetic organoid + reference
  data/load.py          demo | h5ad modes
  qc/preprocess.py      filter -> normalize -> HVG -> PCA -> Leiden (shared gene space)
  qc/score.py           centroid correlation, cluster mapping, QC flags
  qc/report.py          joint UMAP + fidelity summary
tests/                  synthetic-data and scoring tests
docs/                   data sources, evaluation semantics
```

MIT licensed.
