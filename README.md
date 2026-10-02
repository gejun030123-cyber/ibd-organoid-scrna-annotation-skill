# Intestinal organoid scRNA-seq cell annotation skill

A reusable Codex skill for evidence-based annotation of intestinal epithelial organoid single-cell RNA-seq clusters, with an IBD organoid example.

## Contents

- [`skills/intestinal-organoid-cell-annotation/SKILL.md`](skills/intestinal-organoid-cell-annotation/SKILL.md): instructions for the annotation workflow.
- [`references/marker-evidence.md`](skills/intestinal-organoid-cell-annotation/references/marker-evidence.md): candidate marker programs and common ambiguities.
- [`examples/ibd-organoid.md`](skills/intestinal-organoid-cell-annotation/examples/ibd-organoid.md): labels discussed in the source conversation, explicitly marked as unverified against raw data.

To use the skill, copy the `intestinal-organoid-cell-annotation` folder into your Codex skills directory or reference its `SKILL.md` in a task. Supply a clustered AnnData object or exported marker and QC tables. No patient data, expression matrix, or sample identifiers are included in this repository.

The marker lists are hypotheses to test in the supplied dataset, not diagnostic rules or proof of lineage. The example cluster numbers must never be transferred to another dataset.
