# IBD 肠道类器官 scRNA-seq 聚类后细胞注释 skill

从已有 Leiden 聚类出发，复用旧项目的 COSG marker、表达比例、逐群判读、cell-cycle、稀有群复核、标签映射和统一配色展示流程。**不包含原始输入、QC 或重新聚类的常规步骤。**

## Contents

- [`SKILL.md`](skills/intestinal-organoid-cell-annotation/SKILL.md)：入口与判读原则。
- [`聚类后流程`](skills/intestinal-organoid-cell-annotation/references/post-clustering-workflow.md)：从样本构成到 COSG、逐群证据、映射和图表验证。
- [`marker 参考`](skills/intestinal-organoid-cell-annotation/references/marker-evidence.md)：候选 marker 程序和相邻群的区别。
- [`配色与组成图`](skills/intestinal-organoid-cell-annotation/references/plotting-and-composition.md)：复用固定 palette，绘制 UMAP 与五样本/组别比例图。
- [`IBD 类器官实例`](skills/intestinal-organoid-cell-annotation/examples/ibd-organoid.md)：昨天两段聊天记录中的 0–13 群、关键表达比例、样本偏倚与固定色板。

使用时将 `skills/intestinal-organoid-cell-annotation` 文件夹复制到 Codex skills 目录，提供已聚类的 AnnData 或 marker 与细胞构成表。新数据不得沿用实例的 cluster 编号。仓库不含表达矩阵或患者层面的原始数据。

实例根据历史聊天摘要整理；未拿到当时的 notebook 或原始 AnnData，无法重新运行核验。marker 组合是注释证据，不是单基因判定规则。
