# IBD organoid annotation example from the referenced conversation

The prior conversation proposed the following `celltype_v1` labels. This repository does not contain the original AnnData object, marker tables, figures, or donor metadata, so the mapping below is **historical and unverified here**. Cluster IDs depend on that specific clustering run and should never be copied into another analysis.

| Cluster | Proposed label | Evidence to verify in source data |
| ---: | --- | --- |
| 0 | S-phase TA cells | TA/progenitor program with S-phase score and markers |
| 1 | G2/M TA cells | TA/progenitor program with G2/M score and markers |
| 2 | Regenerative epithelial progenitors | Coherent progenitor program plus partial differentiation, excluding doublets |
| 3 | LGR5+ Stem cells | LGR5, OLFM4, ASCL2, SMOC2 program |
| 4 | Immature absorptive enterocytes | Absorptive program below mature functional marker level |
| 5 | ER-stress epithelial cells | DDIT3, ATF3, HSPA5, XBP1 program and QC review |
| 6 | Mature absorptive enterocytes | FABP1, KRT20, VIL1 with ALPI/SI/APOA1/APOA4/MTTP support |
| 7 | Metabolic absorptive progenitors | Absorptive progenitor markers and independent metabolic program evidence |
| 8 | Enterocyte progenitors | Early absorptive differentiation with retained progenitor program |
| 9 | Stress-associated enterocytes | Enterocyte identity plus stress program, distinct from cluster 5 |
| 10 | Inflammatory epithelial cells | CXCL1/CXCL8/TNFAIP2/IFI6/AREG program and sample review |
| 11 | REG4+ secretory progenitors | REG4 with secretory/progenitor context; compare with goblet |
| 12 | Goblet cells | MUC2, SPINK4, CLCA1 program |
| 13 | Rare epithelial cells | Prior discussion reported 10 cells; inspect QC, doublets, markers, donors; exclude from core claims until supported |

To turn this into a validated case study, add per-cluster marker tables with detection fractions, QC and donor counts, dot plots, and the rationale for each label. Do not publish sample-level or patient-level information without the owner's approval.
