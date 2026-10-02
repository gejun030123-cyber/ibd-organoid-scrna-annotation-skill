# 历史 IBD 类器官实例：Leiden 0.8 的 0–13 群

本实例根据《单细胞分析比对流程》和《提取COSG标记基因》的聊天摘要整理。它保留当时的操作与已报告数值，**未拿到 notebook、AnnData 或 marker 原表重新计算**。下面的 cluster ID 只属于这一次聚类，不可照搬到别的数据集。

## 聚类后起点

五个样本：IBD、IN、IP3 属于 dis；NOR3、Normal2 属于 ctrl。已有 23,717 个细胞，Harmony 按 sample_id 校正后，以 Leiden resolution=0.8 得到 14 群。counts 保存在 layers["counts"]。HVG COSG 结果键为 adata_hvg.uns["leiden_0.8_cosg"]；全基因 COSG 结果键为 adata_pp.uns["leiden_0.8_cosg_full"]。实际判读结合 COSG、raw counts mean、pct_expressed 和肠上皮经典 marker，**没有使用 Wilcoxon**。

| cluster | cells | cluster | cells | cluster | cells | cluster | cells |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | 2877 | 1 | 2355 | 2 | 2307 | 3 | 2177 |
| 4 | 2157 | 5 | 2064 | 6 | 2028 | 7 | 1981 |
| 8 | 1794 | 9 | 1535 | 10 | 1314 | 11 | 1000 |
| 12 | 118 | 13 | 10 |  |  |  |  |

## 逐群判读依据

| 群 | 当时的名称 | 聊天记录中的关键证据 | 需要保留的区别与局限 |
| ---: | --- | --- | --- |
| 0 | S-phase TA cells | cell-cycle scoring：S_score mean 0.294、G2M_score mean 0.060；S 39.2%、G2M 29.2%、G1 31.6% | 与 1 共有 TA 身份，但周期分布不同。 |
| 1 | G2/M TA cells | S_score mean 0.681、G2M_score mean 1.202；G2M 63.4% | 保留 TA 身份，不单凭 MKI67 称为新谱系。 |
| 2 | Regenerative epithelial progenitors | OLFM4 67%、ASCL2 57%、SOX9 86%；FABP1 99.7%、KRT20 90%、VIL1 91%、HNF4A 80%；REG4 77%、TFF3 99%、AGR2 94%、LYZ 99.9%；CLDN4 99%、AREG 85%、ANXA1 64% | 同时有 progenitor、吸收、再生/分泌特征；不强行改叫 REG4 secretory 或 enterocyte。需核对细胞级共表达与 multiplet。 |
| 3 | LGR5+ Stem cells | LGR5 57%、OLFM4 69%、SMOC2 76%、ASCL2 76%、SOX9 84% | 采用本数据的 stem program，而非照参考图命名为 MAML3/CHRM3+。 |
| 4 | Immature absorptive enterocytes | FABP1 96%；ALPI、SI、APOA1、APOA4 几乎无表达 | 与 6 的成熟吸收功能程序区分。 |
| 5 | ER-stress epithelial cells | DDIT3、ATF3、HSPA5、XBP1 程序突出 | ER stress 是细胞状态；与 9 的 enterocyte stress、10 的炎症程序比较。 |
| 6 | Mature absorptive enterocytes | CYP3A5 94%、FABP1 99.95%、KRT20 99.4%、VIL1 97.2%、HNF4A 84%、MTTP 62.7% | 可描述为 CYP3A5+ mature enterocytes；比 4/8 有更完整成熟程序。 |
| 7 | Metabolic absorptive progenitors | OLFM4 58%、ASCL2 31%、SOX9 69%；FABP1 99.8%、KRT20 91%、VIL1 87%、CYP3A5 68%；PPARG 96.6%、PGK1 98.9%、CA12 76.9%、ANXA10 40% | 保留 progenitor 与吸收谱系双重证据；“metabolic”是描述性状态，需结合多个基因而非单一通路分数。 |
| 8 | Enterocyte progenitors | OLFM4 24%、ASCL2 8%、SOX9 27%；FABP1 94%、KRT20 60%、VIL1 50%、HNF4A 53%、CYP3A5 60% | progenitor 程序减弱、吸收分化出现；与 4/7/6 比较。 |
| 9 | Stress-associated enterocytes | 吸收上皮身份明显，ATF3、DDIT3 相对升高 | 保留 enterocyte 身份；与 5 的 ER stress 群区分。 |
| 10 | Inflammatory epithelial cells | CXCL1、CXCL8、TNFAIP2、IFI6、ATF3、ANXA1、AREG 高于 cluster 2 | 炎症程序不能仅由单个趋化因子判断；核对 donor/sample。 |
| 11 | REG4+ secretory progenitors | REG4 54%、TFF3 84%、AGR2 79%，仍有 OLFM4、ASCL2、SOX9 | 不是成熟 goblet；与 2 的混合再生程序和 12 的 goblet 程序比较。 |
| 12 | Goblet cells | MUC2、SPINK4、CLCA1、FCGBP、TFF3、RETNLB 组合 | 118 细胞中 117 来自 NOR3；有 goblet marker 证据，但不能据此称“ctrl 特异”。 |
| 13 | Rare epithelial cells | 10 细胞全来自 NOR3；median UMI 约 61,936、median genes 约 6,678；OLFM4/SMOC2/ASCL2/SOX9、FABP1/KRT20/VIL1/HNF4A、REG4/TFF3/AGR2/SPINK4/LYZ 与其他程序同时很高 | 可能是 rare transitional/high-RNA 群，也不能排除 residual multiplet；保留暂定标签，不作为核心结论。 |

上表中的百分数来自旧聊天摘要；它们不是本仓库重新计算的验证结果。对 cluster 2、7、13 的混合程序尤其要在原对象中检查是否由同一细胞共表达。

## 写入映射与空间核对

具体 annotation_map 见[工作流](../references/post-clustering-workflow.md)。旧对话已准备将它映射到 adata_hvg.obs["celltype_v1"]，然后绘制 annotation UMAP。讨论中的空间组织大致呈 stem / TA / progenitor 与吸收、分泌和状态群相邻；这只是可视化一致性检查，不能证明分化轨迹。

## 固定顺序与 palette

旧项目要求低饱和、相近谱系颜色相关、UMAP 和所有组成图完全同色。以下是最终衔接摘要记录的顺序和色值：

~~~python
celltype_order = [
    "LGR5+ Stem cells",
    "S-phase TA cells",
    "G2/M TA cells",
    "Regenerative epithelial progenitors",
    "Enterocyte progenitors",
    "Metabolic absorptive progenitors",
    "Immature absorptive enterocytes",
    "Mature absorptive enterocytes",
    "Stress-associated enterocytes",
    "ER-stress epithelial cells",
    "Inflammatory epithelial cells",
    "REG4+ secretory progenitors",
    "Goblet cells",
    "Rare epithelial cells",
]
celltype_colors = {
    "LGR5+ Stem cells": "#3B8C6E",
    "S-phase TA cells": "#C65D63",
    "G2/M TA cells": "#74A96B",
    "Regenerative epithelial progenitors": "#4F95A4",
    "Enterocyte progenitors": "#7895B2",
    "Metabolic absorptive progenitors": "#8B79A8",
    "Immature absorptive enterocytes": "#A78EB8",
    "Mature absorptive enterocytes": "#D58A55",
    "Stress-associated enterocytes": "#B5A0C2",
    "ER-stress epithelial cells": "#C69AB3",
    "Inflammatory epithelial cells": "#3E7F8C",
    "REG4+ secretory progenitors": "#98A6B7",
    "Goblet cells": "#345A8A",
    "Rare epithelial cells": "#B8B8B8",
}
~~~

五样本及 Dis/Ctrl 组成图应使用该 palette，100% 堆叠、白色分隔、只标较大区块（约 ≥4%）、图例置右。旧聊天曾出现 condition_plot 缺失的 KeyError；画组别图前先从 group 映射并检查无 NaN。正式比较细胞组成应以 3 个 disease 与 2 个 control **样本**为单位，不凭 pooled 细胞比例给出显著性结论。
