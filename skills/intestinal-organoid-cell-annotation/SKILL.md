---
name: intestinal-organoid-cell-annotation
description: Annotate intestinal epithelial organoid scRNA-seq clusters, including IBD and control organoids, from marker programs, expression prevalence, QC, lineage context, and cell states. Use for assigning or reviewing cluster labels; do not use for unrelated downstream pathway or differential-expression analysis.
---

# Intestinal organoid cell annotation

Annotate clusters with an auditable evidence table. Treat lineage identity, differentiation stage, and transient state as separate axes. A single marker, reference-mapping label, or cluster rank is insufficient evidence.

## Inputs

Use a clustered AnnData object or equivalent tables with: cluster assignment, sample/donor and condition, normalized expression for visualization, uncorrected counts for QC and appropriate statistical tests, per-cluster marker results, and cell-level QC/doublet information. Ask for missing essentials if they prevent a defensible label. Record species, intestinal region, culture conditions, integration method, clustering resolution, and the expression layer used for each analysis; these affect marker interpretation.

## Workflow

1. **Check technical validity.** Review cells per cluster, genes/UMIs, mitochondrial fraction, doublet calls, and sample contribution. Flag clusters driven by one sample, low quality, ambient RNA, or mixed incompatible lineage markers. Revisit broad epithelial identity before fine labels if non-epithelial cells may remain.
2. **Discover cluster markers.** Obtain positive and depleted markers with effect size, detection fraction inside and outside the cluster, and statistical support. COSG can prioritize specific markers, as used in the source workflow; cross-check with a conventional differential-expression method rather than treating either ranking as ground truth. Use the appropriate expression representation and biological replicates for the question.
3. **Evaluate programs.** Compare coherent sets of positive markers, expected absent or low markers, expression prevalence, and specificity across neighboring clusters. Inspect dot plots, heatmaps, and per-gene distributions; distinguish a cluster-wide program from a few outlier cells. Consult [marker evidence](references/marker-evidence.md) for candidate programs and ambiguities.
4. **Place clusters in context.** Assign a broad epithelial lineage, then differentiation stage and state. Interpret stem, cycling transit-amplifying, absorptive, and secretory programs alongside stress, inflammatory, metabolic, and cell-cycle programs. Do not treat a state as a new lineage without independent evidence. A proposed developmental ordering is a hypothesis unless supported by trajectory or lineage evidence.
5. **Check alternatives.** Compare each proposed label with its nearest plausible alternatives and write down the discriminating evidence. Review reference mapping as corroboration, especially when the reference differs in organ, culture, disease, or developmental context.
6. **Validate and report.** Produce a per-cluster evidence table with candidate and alternative labels, positive and negative markers, detection fractions, QC/sample support, confidence, and unresolved questions. Show a marker dot plot or heatmap and an embedding colored by labels, QC, and sample. Check label stability across sensible clustering resolutions and donors when data allow. Use condition proportions only after accounting for sample/donor effects.

## Decision rules

- Prefer a descriptive provisional label when evidence is mixed. State what would resolve it.
- Separate S-phase and G2/M cycling subclusters only when cell-cycle scores and markers support that distinction; keep their shared TA identity explicit.
- For small or rare clusters, verify marker coherence, QC, doublets, and sample reproducibility before making a biological claim. Do not use a universal cell-count cutoff as proof of validity; the source example's 10-cell rare cluster was displayed but excluded from central conclusions.
- Do not infer causality, differentiation direction, or disease specificity from an annotation alone. Disease/control comparisons require biological replicates and suitable statistics.
- Save labels to a new versioned column (for example `celltype_v1`) and preserve original cluster IDs and previous labels. Do not overwrite the only copy of an AnnData object.

## Output format

Return: (1) a cluster-to-label table, (2) the evidence and strongest alternative for each cluster, (3) confidence and QC caveats, and (4) a short validation and next-step list. Distinguish observations from interpretation. If raw marker/QC evidence is unavailable, label the result as a proposed annotation plan rather than validated annotation.

For the historical IBD organoid example, read [examples/ibd-organoid.md](examples/ibd-organoid.md). Its cluster IDs are specific to that conversation and have not been revalidated from source data here.
