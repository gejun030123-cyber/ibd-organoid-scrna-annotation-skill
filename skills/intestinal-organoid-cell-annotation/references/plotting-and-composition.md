# 注释图与组成图：复用同一顺序和色板

先运行[实例中的 celltype_order 与 celltype_colors](../examples/ibd-organoid.md) 代码块，再运行下面的代码。若在新数据中使用，先重建属于新标签的顺序与色板，不沿用旧 cluster 编号。

## 1. 将颜色写入对象并检查

~~~python
import pandas as pd

assert set(adata_hvg.obs["celltype_v1"].dropna()) == set(celltype_order)
adata_hvg.obs["celltype_v1"] = pd.Categorical(
    adata_hvg.obs["celltype_v1"],
    categories=celltype_order,
    ordered=True,
)
adata_hvg.uns["celltype_v1_colors"] = [
    celltype_colors[ct] for ct in celltype_order
]
~~~

旧对话要求 marker heatmap/dotplot、annotation UMAP、后续堆叠柱状图使用同一标签。dotplot 同时展示表达强度和阳性比例，并用全基因表达对象核对未列入 HVG 的判别 marker；不要用 scaled 层解释绝对表达。UMAP 用来检查标签、marker 和样本空间分布是否相符，不能单独证明谱系或轨迹。

## 2. Annotation UMAP

旧项目使用 OmicVerse 的 embedding 绘图，白底、小点、低饱和、右侧 legend：

~~~python
import matplotlib.pyplot as plt
import omicverse as ov

fig, ax = plt.subplots(figsize=(8.2, 6.3))
ov.pl.embedding(
    adata_hvg,
    basis="X_umap",
    color="celltype_v1",
    palette=[celltype_colors[ct] for ct in celltype_order],
    size=7,
    alpha=0.82,
    frameon=False,
    legend_loc="right margin",
    legend_fontsize=8.5,
    ax=ax,
    show=False,
)
ax.set_title("Cell-type annotation", loc="left", fontsize=15, fontweight="bold")
ax.set_xticks([])
ax.set_yticks([])
plt.tight_layout()
plt.show()
~~~

之后仍需按 sample_id、group、代表 marker 着色查看；“图形好看”不能代替 marker 和样本来源核对。

## 3. 五样本与 Dis/Ctrl 的描述性组成

先创建 condition_plot，检查无 NaN，以避免旧 notebook 曾出现的 KeyError：

~~~python
import numpy as np

adata_hvg.obs["condition_plot"] = (
    adata_hvg.obs["group"].astype(str).map({"dis": "Dis", "ctrl": "Ctrl"})
)
assert adata_hvg.obs["condition_plot"].notna().all()

sample_order = ["IBD", "IN", "IP3", "NOR3", "Normal2"]
condition_order = ["Dis", "Ctrl"]

def composition(column, row_order):
    prop = pd.crosstab(
        adata_hvg.obs[column],
        adata_hvg.obs["celltype_v1"],
        normalize="index",
    )
    prop = prop.reindex(index=row_order, columns=celltype_order, fill_value=0)
    assert prop.notna().all().all()
    assert np.allclose(prop.sum(axis=1), 1)
    return prop

prop_sample = composition("sample_id", sample_order)
prop_condition = composition("condition_plot", condition_order)
display((prop_sample * 100).round(1), (prop_condition * 100).round(1))
~~~

两图均用 100% 堆叠、同一 palette、白色区块分隔和右侧 legend。只给足够大的区块标数字，旧方案约 ≥4%。一个可复用绘图函数：

~~~python
import numpy as np
from matplotlib.ticker import PercentFormatter

def label_color(hex_color):
    rgb = [int(hex_color[i:i+2], 16) for i in (1, 3, 5)]
    luminance = 0.299 * rgb[0] + 0.587 * rgb[1] + 0.114 * rgb[2]
    return "white" if luminance < 150 else "#222222"

def plot_composition(prop, title):
    fig, ax = plt.subplots(figsize=(8, 5))
    x = np.arange(len(prop))
    bottom = np.zeros(len(prop))
    for ct in celltype_order:
        values = prop[ct].to_numpy(dtype=float)
        ax.bar(
            x, values, bottom=bottom, width=0.72,
            color=celltype_colors[ct], edgecolor="white",
            linewidth=0.6, label=ct,
        )
        for i, value in enumerate(values):
            if value >= 0.04:
                ax.text(
                    x[i], bottom[i] + value / 2, f"{value * 100:.1f}%",
                    ha="center", va="center", fontsize=7,
                    color=label_color(celltype_colors[ct]),
                )
        bottom += values
    ax.set_xticks(x, prop.index)
    ax.set_ylim(0, 1)
    ax.yaxis.set_major_formatter(PercentFormatter(1))
    ax.set_ylabel("Cell proportion")
    ax.set_title(title, loc="left")
    ax.spines["top"].set_visible(False)
    ax.spines["right"].set_visible(False)
    ax.legend(title="Cell type", bbox_to_anchor=(1.02, 1),
              loc="upper left", frameon=False, fontsize=8)
    plt.tight_layout()
    return fig, ax

plot_composition(prop_sample, "Cell-type composition by sample")
plot_composition(prop_condition, "Cell-type composition by condition")
plt.show()
~~~

先看样本图，再看 pooled Dis/Ctrl 图。旧项目仅有 3 个 disease 与 2 个 control 样本；正式比较应计算**每个样本**的 cell-type proportion，而不是把每个细胞当作独立重复。Cluster 12/13 的 NOR3 集中性必须在图注中保留。
