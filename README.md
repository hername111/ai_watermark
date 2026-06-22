# LLM 水印技术实验

本项目的目标是在小型开源 LLM 上复现并评估两种文本水印方法（KGW 绿名单水印和 Unbiased 分布保持水印），
并提出一种面向表格/时间序列基础模型的多位结构化残差水印方案。

## 项目结构

```text
submission/
├── README.md
├── data/
│   ├── prompts.json              # 210 条中文测试 prompt（12 个领域）
│   └── human_texts.json          # 190 条中文人类文本（用于误报率评估）
├── notebooks/
│   ├── assignment1_enhanced.ipynb # 作业 1：复现 KGW 与 Unbiased 水印
│   ├── assignment2_enhanced.ipynb # 作业 2：系统评估（检测/质量/鲁棒性/开销）
│   └── assignment3_enhanced.ipynb # 作业 3：时间序列/表格多位水印方案
└── outputs/
    ├── assignment1_full_charts.png
    ├── assignment1_category_analysis.png
    ├── assignment1_full_results.csv
    ├── assignment1_full_summary.csv
    ├── assignment2_full_charts.png
    ├── assignment2_full_generation_metrics.csv
    ├── assignment2_full_runtime.csv
    ├── assignment2_full_human_z.csv
    ├── assignment2_full_detection_metrics.csv
    ├── assignment2_full_roc.csv
    ├── assignment2_full_attack_results.csv
    ├── assignment3_full_charts.png
    ├── assignment3_base_series.csv
    ├── assignment3_full_watermarked_series.csv
    ├── assignment3_full_bit_detection.csv
    ├── assignment3_full_quality_metrics.csv
    └── assignment3_full_attack_results.csv
```

## 作业说明

### 作业 1：复现 KGW 与 Unbiased 两种 LLM 水印方法

**目标**：在 Qwen2.5-0.5B-Instruct 模型上复现 KGW 绿名单水印和 Unbiased 分布保持水印，
并对 100 条中文 prompt 进行批量生成实验。

**评估维度**：
- 检测有效性：Z-score（Z > 2 视为可检测）
- 文本质量：模型困惑度（Perplexity, PPL）
- 计算开销：生成延迟

**方法**：
- KGW（Kirchenbauer et al., ICML 2023）：绿名单 token logit +δ 偏置
- Unbiased（受 Thickstun, TMLR 2023 启发）：概率重加权，α 因子放大绿名单概率

**主要发现**：
- KGW 检测信号最强（mean Z=5.2），Cohen's d=4.9 vs baseline
- Unbiased 质量损失更小，与 baseline PPL 无显著差异
- 两种方法对 prompt 类别不敏感，跨领域稳定性好

### 作业 2：系统评估

**目标**：在多维度上系统比较 KGW 与 Unbiased 的性能。

**评估维度**：
- 检测性能：准确率、精确率、召回率、F1、ROC/AUC
- 误报率：100 条中文人类文本作为负类样本
- 参数敏感性：KGW δ∈{1.0~3.0}，Unbiased α∈{1.5~3.5}
- 鲁棒性：删除攻击（0-50%）和同义词替换攻击（0-30%）
- 计算开销：11 种配置的生成延迟

**主要发现**：
- KGW δ≥2.0 和 Unbiased α≥2.5 在 Z=2 阈值下检出率 >90%
- 人类文本误报率在 Z=2 时约 1-4%，Z=3 时降至 0-1%
- KGW AUC 最高达 1.000，Unbiased AUC 最高达 0.994
- KGW 鲁棒性整体优于 Unbiased（logit 偏置比概率重加权更稳固）
- Spearman 相关证实参数强度与 Z-score 的单调递增关系（p < 1e-64）

### 作业 3：结构化残差水印方案（加分项）

**目标**：将文本水印思想迁移到表格和时间序列基础模型。

**方案特点**：
- 嵌入域：数值残差（替代 token logit）
- 12-bit payload（可编码 4096 种身份标识）
- 扰动强度仅为数据标准差的 3%（隐蔽性极佳，KS 检验 p=0.99）
- 数据规模：48 entities × 150 时间步 = 7200 行

**攻击鲁棒性**：
- 加性噪声（α≤0.18）：100% bit 恢复率
- 随机删行（删除率≤60%）：100% bit 恢复率
- 量化（舍入到整数）：100% bit 恢复率
- 平滑（窗口>1）：75% bit 恢复率（该方案的主要弱点）

## 参考文献

| 论文 | 会议/期刊 | 代码 |
|------|----------|------|
| Kirchenbauer et al. "A Watermark for Large Language Models" | ICML 2023 | https://github.com/jwkirchenbauer/lm-watermarking |
| Thickstun, J. "Robust Distortion-free Watermarks for Language Models" | TMLR 2023 | https://github.com/jthickstun/watermark |
| Pan et al. "MarkLLM: An Open-Source Toolkit for LLM Watermarking" | EMNLP 2024 | https://github.com/THU-BPM/MarkLLM |
| LLM Watermarking Tutorial | ACL 2024 | https://leililab.github.io/llm_watermark_tutorial/ |

## 运行环境

- Python 3.12
- PyTorch 2.8+ (CUDA)
- NVIDIA RTX 4090D (24GB VRAM)
- 模型：Qwen2.5-0.5B-Instruct

### 依赖安装

```bash
pip install torch transformers pandas numpy matplotlib seaborn scikit-learn scipy tqdm jupyter nbconvert
```

### 模型下载

```bash
# 使用 HuggingFace 镜像
HF_ENDPOINT=https://hf-mirror.com python3 -c "
from huggingface_hub import snapshot_download
snapshot_download('Qwen/Qwen2.5-0.5B-Instruct', local_dir='/root/autodl-tmp/Qwen2.5-0.5B-Instruct')
"
```

### 运行 Notebook

```bash
cd submission/notebooks

# 作业 1（约 10 分钟）
jupyter nbconvert --to notebook --execute assignment1_enhanced.ipynb \
  --output assignment1_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=1500

# 作业 2（约 30 分钟，含生成 + 攻击评估）
jupyter nbconvert --to notebook --execute assignment2_enhanced.ipynb \
  --output assignment2_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=2400

# 作业 3（约 30 秒，纯数值计算）
jupyter nbconvert --to notebook --execute assignment3_enhanced.ipynb \
  --output assignment3_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=600
```

在 Jupyter Lab/Notebook 中交互运行时图表会直接内联显示，同时自动保存到 `../outputs/` 目录。
