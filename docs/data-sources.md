# Data sources

## Demo mode (default)

`dataset.mode: demo` generates a deterministic synthetic pair: a reference
"tissue" with one population per marker key in `config.markers`, and an
organoid culture that recovers all-but-one type at reduced marker expression
(immature phenotype) plus one aberrant population matching no reference type.

The demo exists so the pipeline and tests run without downloads. Its scores
are fixture values, never biological findings.

## Real data: h5ad mode

Set `dataset.mode: h5ad` and provide two annotated AnnData files:

- `reference_h5ad` — human tissue scRNA-seq with a cell-type column
  (default `cell_type`; set `obs_columns.cell_type`).
- `organoid_h5ad` — organoid scRNA-seq; Leiden clusters are computed in
  preprocessing, or supply your own via `obs_columns.cluster`.

Practical sources:

- **CELLxGENE Census** (`cellxgene-census` package): query human tissue by
  `tissue`/`cell_type`, export `.h5ad` slices. Curated, consistently
  annotated — the best reference source.
- **GEO**: organoid atlas papers deposit count matrices + metadata; assemble
  into AnnData. Check each study's own cell-type labels before merging.
- **Human Cell Atlas data portal**: larger reference collections, more
  assembly work.

Update `config.markers` to the tissue's canonical marker panel — the panel
is part of the experiment definition, not a default to reuse blindly.
