---
name: intestinal-organoid-cell-annotation
description: 从已完成聚类的肠道类器官 scRNA-seq 数据进行完整细胞注释：样本构成、COSG marker、表达比例、谱系与状态判读、稀有群复核、celltype_v1 映射和统一图表验证。适用于 IBD 与对照类器官。
---

# 肠道类器官 scRNA-seq 聚类后注释

从**已有 cluster** 开始。目标是形成可追溯的 `cluster → 谱系 / 分化阶段 / 状态 → marker 证据 → 置信度`，再保存 `celltype_v1`。不要从输入、过滤或 QC 全流程重新开始，除非用户另行要求；仅在可疑小群中回查已有 QC 指标。

## 操作顺序

完整执行 [聚类后工作流](references/post-clustering-workflow.md)：

1. 统计每个 cluster 的细胞数、sample 和 Dis/Ctrl 构成，先识别样本偏倚，不据此直接命名或宣称疾病特异。
2. 用 **COSG** 在 counts 层找每群 marker；先看 top 10，再看 top 30，并在全基因对象上补查。结合 raw counts mean、`pct_expressed` 和经典肠上皮 marker。原项目明确选择**不用 Wilcoxon**；不要把它偷偷加为必做步骤。
3. 按 [marker 判别参考](references/marker-evidence.md) 逐群比较：先谱系，再分化阶段，最后加 cycling、再生、代谢、应激或炎症状态。每群必须写最强替代解释。
4. 单独核对 cell-cycle scoring、相邻吸收/分泌群以及样本集中的稀有群。混合 marker 时先看是否为同细胞共表达或潜在 multiplet。
5. 形成 cluster 映射，写入新列 `celltype_v1`；用 marker dotplot/heatmap、UMAP 和按样本图验证，统一标签顺序与颜色。参考映射仅作第二层验证。
6. 注释完成后才画 sample 与 condition 的组成图；疾病比较优先按 sample 作为分析单位，不从 pooled cell proportion 直接作统计结论。

## 边界与交付

- 单个 marker、COSG 排名、UMAP 位置、参考图标签或 pooled 组别比例均不足以独立命名。
- 将谱系、分化阶段和细胞状态分别记录。图上的相邻关系不是已证实的发育轨迹。
- 输出逐群证据表、最强替代解释、置信度、待复核点、`celltype_v1` 映射和复用同一 palette 的图。
- 保留原 cluster 列和原对象；没有原始 marker 或细胞级数据时只给**候选注释**。
- [IBD 项目实例](examples/ibd-organoid.md)记录旧对话的 0–13 群、实际数值与配色。编号只适用于该次 Leiden 0.8 聚类，不可套用到新数据。
