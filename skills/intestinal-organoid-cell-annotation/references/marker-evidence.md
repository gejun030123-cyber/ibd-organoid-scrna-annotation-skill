# Candidate marker evidence for intestinal epithelial organoids

Use these as starting hypotheses. Confirm co-expression, detection fraction, specificity, and biological context in the actual dataset. Gene absence is weak evidence when a gene is lowly detected.

| Candidate identity or state | Positive evidence to inspect | Main alternatives and checks |
| --- | --- | --- |
| LGR5-positive stem | LGR5, OLFM4, ASCL2, SMOC2; SOX9 may support | Check broad stem program and low differentiated markers; a lone LGR5 or OLFM4 signal is insufficient. |
| Cycling TA | MKI67, TOP2A, PCNA plus intestinal progenitor context | Use cell-cycle scores to separate S versus G2/M; cycling status alone does not establish lineage. |
| Regenerative epithelial progenitor | OLFM4, ASCL2, SOX9 together with partial absorptive or secretory programs | Mixed FABP1/KRT20/VIL1 and REG4/TFF3/AGR2 can represent an intermediate state or a doublet; inspect cell-level coherence and QC. |
| Absorptive enterocyte maturation | FABP1, KRT20, VIL1; ALPI, SI, APOA1, APOA4, MTTP support mature function | Compare progenitor and mature clusters using prevalence and multiple functional markers; inspect intestinal region and culture effects. |
| Secretory progenitor / goblet | REG4, TFF3, AGR2 for secretory-associated program; MUC2, SPINK4, CLCA1 for goblet | REG4 alone does not prove a goblet identity. Compare mucus program, progenitor markers, and cell-level co-expression. |
| ER-stress epithelial state | DDIT3, ATF3, HSPA5, XBP1 | Treat as state; distinguish culture/dissociation stress and low-quality cells using QC, sample distribution, and broader stress signatures. |
| Inflammatory epithelial state | CXCL1, CXCL8, TNFAIP2, IFI6, AREG | Treat as state; check donor/condition consistency, ambient RNA, and independent NF-κB/TNF/JAK-STAT evidence. These genes are not interchangeable pathway readouts. |
| Stress-associated enterocyte | Enterocyte identity plus coherent stress program | Distinguish from ER stress and technical stress; retain underlying enterocyte identity. |

## Evidence table template

| cluster | cells; donors | proposed lineage / stage / state | positive markers and pct expressed | expected low markers | alternative | QC and sample support | confidence / action |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ... | ... | ... | ... | ... | ... | ... | ... |

Use “high / moderate / low” confidence with a short reason rather than a score that implies false precision. If marker tests are done cell by cell, avoid interpreting tiny p-values as donor-level reproducibility.
