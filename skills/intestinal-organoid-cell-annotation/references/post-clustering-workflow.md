# 聚类完成后的细胞注释操作流程

本流程从已有 Leiden/UMAP 对象开始，不重做原始导入、过滤、双细胞移除、标准化或 Harmony。以下用旧项目的 OmicVerse 对象名示范；新项目必须替换对象名和 cluster 键。每一步先核对输出再继续。旧项目选择 COSG，明确**不用 Wilcoxon**。

## 1. 固定聚类版本并看来源

旧项目使用 Harmony 后的邻居图、Leiden resolution=0.8，得到 adata_hvg.obs["leiden_0.8"] 的 0–13 群。先记录每群细胞数，再统计群内 sample_id 与 group 比例：

~~~python
import pandas as pd

cluster_key = "leiden_0.8"
cluster_n = adata_hvg.obs[cluster_key].astype(str).value_counts().sort_index()
sample_mix = pd.crosstab(
    adata_hvg.obs[cluster_key].astype(str),
    adata_hvg.obs["sample_id"],
    normalize="index",
)
group_mix = pd.crosstab(
    adata_hvg.obs[cluster_key].astype(str),
    adata_hvg.obs["group"],
    normalize="index",
)
display(cluster_n, (sample_mix * 100).round(1), (group_mix * 100).round(1))
~~~

解释组成时同时看全体细胞的组别基线。旧项目 ctrl 约 57.4%、dis 约 42.6%；“超过 50%”不是富集判据。Cluster 12 有 118 细胞，117 来自 NOR3；cluster 13 有 10 细胞，全部来自 NOR3。先标记为需要复核，不能因为都在 ctrl 组就称为“对照特异”。组成是警示和上下文，不代替 marker 证据，也不能把细胞数当成独立生物学重复。

**检查点**：cluster 计数与元数据一致；每群 sample/group 比例和为 100%；记录单样本主导的群。

## 2. COSG 找特异 marker

在 counts 层运行 COSG，先看每群 top 10，再看 top 30，并用全基因对象补查。旧项目先在 HVG 对象得到 adata_hvg.uns["leiden_0.8_cosg"]，后来在全基因对象得到 adata_pp.uns["leiden_0.8_cosg_full"]。

~~~python
import omicverse as ov

ov.single.find_markers(
    adata_hvg,
    groupby="leiden_0.8",
    method="cosg",
    n_genes=30,
    key_added="leiden_0.8_cosg",
    layer="counts",
    use_raw=False,
    pts=True,
)
~~~

若在 adata_pp 全基因矩阵重算，先按 cell ID 对齐 cluster 标签并确认 counts 层仍在：

~~~python
adata_pp.obs["leiden_0.8"] = (
    adata_hvg.obs["leiden_0.8"].reindex(adata_pp.obs_names)
)
assert adata_pp.obs["leiden_0.8"].notna().all()
assert "counts" in adata_pp.layers

ov.single.find_markers(
    adata_pp,
    groupby="leiden_0.8",
    method="cosg",
    n_genes=30,
    key_added="leiden_0.8_cosg_full",
    layer="counts",
    use_raw=False,
    pts=True,
)
~~~

全基因结果用于检查判别 marker 是否因 HVG 子集而遗漏。导出 top marker 前先查看 .uns 实际结构，不假定包版本的内部格式。

COSG 提名后，按群汇总 **raw counts mean + 群内 pct_expressed + 群外 pct_expressed + 经典肠上皮 marker**。平均表达和阳性细胞比例回答不同问题。检查 marker 是否由少数细胞或单一样本驱动；必要时直接比较相邻群。原项目不要添加 Wilcoxon 为默认步骤。

**检查点**：每群有 top 10/30 表；关键 marker 在全基因对象可查；记录所用 counts 层和群内外检测比例。

## 3. 先大谱系、再阶段、最后状态

按 [marker 判别参考](marker-evidence.md) 建立三列，而不是直接套一个名字：

| cluster | lineage | stage | state | 候选显示标签 | 最强替代解释 |
| --- | --- | --- | --- | --- | --- |
| … | absorptive | progenitor | metabolic | Metabolic absorptive progenitors | mature enterocyte 或技术混合 |

- **Stem/TA**：LGR5/OLFM4/ASCL2/SMOC2 等 stem 组合，与 MKI67/TOP2A/PCNA 增殖组合分开；在 TA 中结合已做的 cell-cycle scoring 区分 S 与 G2/M。
- **吸收谱系**：比较 FABP1/KRT20/VIL1 与 ALPI/SI/APOA1/APOA4/MTTP 等成熟功能 marker 的检测比例，区分 enterocyte progenitor、immature 与 mature。代谢或 stress 是叠加状态。
- **分泌谱系**：REG4/TFF3/AGR2 与仍存在的 progenitor marker 支持分泌前体；MUC2/SPINK4/CLCA1/FCGBP/RETNLB 组合支持 goblet。
- **再生、ER stress、炎症**：核对每群成组 marker、群内外比例和细胞级共表达。尤其比较 cluster 2 与 11、cluster 5 与 9/10，避免把 stress 当成新谱系。

每群写明“为什么不是最像的另一群”。例如 cluster 4 有 FABP1 但成熟吸收 marker 近乎没有；cluster 2 同时有 progenitor、吸收、再生和分泌特征，不能只按 REG4 或 FABP1 命名。UMAP 空间关系只用于合理性检查，不证明发育方向。

**检查点**：每个标签有成组支持 marker、表达比例及一个被比较的替代标签；证据不足时保留描述性或未定标签。

## 4. 针对疑难群做专项复核

| 比较 | 判别问题 |
| --- | --- |
| 0 vs 1 | 是否都是 TA，且 S/G2M 分数与周期比例确实不同？ |
| 2 vs 11 vs 12 | 再生混合程序、REG4+ 分泌前体、成熟 goblet 是否可由 progenitor 与黏液程序区分？ |
| 3 vs 其他 progenitor | 是否有完整 LGR5/OLFM4/ASCL2/SMOC2 stem 组合？ |
| 4 vs 6 vs 7 vs 8 | 成熟吸收 marker、残留 progenitor marker、代谢状态如何组合？ |
| 5 vs 9 vs 10 | ER stress、enterocyte stress、inflammatory 程序是否不同，底层谱系是否一致？ |
| 12 vs 13 | 是否为真实稀有群，还是 NOR3 特异、残余 doublet/multiplet 或聚类伪群？ |

Cluster 13 在旧对话中仅 10 细胞，median UMI 约 61,936、median genes 约 6,678，多个谱系程序同时很高。回查已有 QC/doublet 结果和细胞级共表达；保留 “Rare epithelial cells” 暂定标签，不用于核心结论。Cluster 12 虽几乎全来自 NOR3，仍有一致的 goblet marker，不能仅凭样本偏倚直接删除。不要用固定细胞数阈值替代判断。

## 5. 形成证据表并写入 celltype_v1

逐群记录 cluster、n cells、样本来源、COSG top marker、关键基因 raw counts mean 与群内外 pct_expressed、支持 marker、反证/缺失 marker、最强替代解释、最终标签、置信度和待核查问题。[历史案例](../examples/ibd-organoid.md)给出 0–13 的具体证据与映射。

~~~python
annotation_map = {
    "0": "S-phase TA cells",
    "1": "G2/M TA cells",
    "2": "Regenerative epithelial progenitors",
    "3": "LGR5+ Stem cells",
    "4": "Immature absorptive enterocytes",
    "5": "ER-stress epithelial cells",
    "6": "Mature absorptive enterocytes",
    "7": "Metabolic absorptive progenitors",
    "8": "Enterocyte progenitors",
    "9": "Stress-associated enterocytes",
    "10": "Inflammatory epithelial cells",
    "11": "REG4+ secretory progenitors",
    "12": "Goblet cells",
    "13": "Rare epithelial cells",
}
adata_hvg.obs["celltype_v1"] = (
    adata_hvg.obs["leiden_0.8"].astype(str).map(annotation_map)
)
assert adata_hvg.obs["celltype_v1"].notna().all()
~~~

这是旧项目映射，**不能用于新聚类**。保留 leiden_0.8 和之前标签；修改判断时新增版本或记录变更，不静默覆盖。

## 6. 图表验证与统一色板

用 marker dotplot、heatmap 和 UMAP 复核标签是否与表达比例、样本构成一致。固定 celltype_order 和 celltype_colors，将颜色按类别顺序写入 adata_hvg.uns["celltype_v1_colors"]；具体旧项目色值与顺序在[实例](../examples/ibd-organoid.md)。UMAP 与 sample/condition 柱状图复用同一色板。低饱和色、简洁边框、小点及右侧图例是旧项目的展示偏好，不是生物学证据。绘图与组成统计的具体代码见[统一配色和组成图](plotting-and-composition.md)。

分别画五个样本 IBD / IN / IP3 / NOR3 / Normal2 与 Dis / Ctrl 的 100% 堆叠组成图。图中仅为足够大的区块标百分比（旧方案约 ≥4%）。绘图前确认 condition_plot 已由 group 映射，避免旧 notebook 曾出现的 KeyError。汇总图是描述性展示：疾病 n=3、对照 n=2，正式组成比较优先以 sample 为单位计算比例。

## 7. 外部参考与下游分析边界

先完成自身数据的 marker 证据，再用肠上皮参考（旧计划提到 SCP259、scVI/scANVI/reference mapping）检查标签；参考差异可提示复核，不强制改名。若证据显示 artefact 或聚类问题，记录理由后再考虑去除或重聚类，不预设必须重跑 Harmony/Leiden。

细胞组成推断、pseudobulk DEG、PPARA/代谢/炎症通路和 TF 活动是**注释后的独立分析**。旧对话中的 PPARA 与代谢分数相关性及去除重叠基因检查，不反向证明细胞身份，也不纳入本 skill 的注释判定链。
