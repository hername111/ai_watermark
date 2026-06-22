# 作业要求

LLM水印技术将不易察觉的信号嵌入模型生成的文本或模型权重中，以实现作者身份识别、检测AI生成的内容并保护知识产权。水印方法的关键要求包括有效性（可靠的检测）、隐蔽性（对文本质量的影响最小）以及对释义和其他移除攻击的鲁棒性。

💻 资源说明。建议学生使用小型开源LLM模型（例如Qwen3-0.6B、Qwen2.5-1.5B或Gemma-2-2B）作为基础生成模型来实现和评估水印方法。MarkLLM工具包专门支持使用多种水印算法进行轻量级实验，其内置的评估套件可以在无需大型模型或大量GPU内存的情况下评估文本质量（困惑度）和检测性能。

项目目标。

●(a) 使用 MarkLLM 等现有工具包，复现至少两种 LLM 水印方法（例如 KGW 绿名单水印、无偏水印或 SynthID-Text），并在小型开源模型生成的文本上进行评估。评估应涵盖检测准确率、误报率和文本困惑度。

●(b) 设计实验，分析每种方法在检测准确率、文本质量、抗释义攻击的鲁棒性和计算开销等关键维度上的优缺点。

●(c) （加分项）提出新的改进方案，例如增强抗自适应攻击的鲁棒性、减少文本质量下降、为代码 LLM 设计水印，或开发编码溯源元数据的多位水印方案。考虑时间序列基础模型或表格基础模型的水印方案将是一大优势（自行查阅相关文献，有很多相关论文！）。

# 参考文献
Paper Venue Code
A Watermark for Large Language Models (KGW) ICML 2023 https://github.com/jwkirchenbauer/lm-watermarking
Robust Distortion-free Watermarks for Language Models TMLR 2023 https://github.com/jthickstun/watermark
MarkLLM: An Open-Source Toolkit for LLM Watermarking EMNLP 2024 https://github.com/THU-BPM/MarkLLM
LLM Watermarking Tutorial (ACL 2024) ACL 2024 https://leililab.github.io/llm_watermark_tutorial/


# 最终提交版说明

本目录是当前建议提交的干净版本。旧的 `notebooks`、`notebook_outputs`、`final_submission` 是早期实验产物，可以忽略。

## 提交文件

```text
submission/notebooks/assignment1_complete.ipynb
submission/notebooks/assignment2_complete.ipynb
submission/notebooks/assignment3_complete.ipynb
```

三个 notebook 均已运行过，并保留了输出表格和图表。

## 输出数据和图表

输出文件位于：

```text
submission/outputs
```

## 输入数据

notebook 运行所需输入数据已放在：

```text
submission/data/prompts.json
submission/data/human_texts.json
```

其中 `prompts.json` 用于作业 1 和作业 2 的模型生成实验，`human_texts.json` 用于作业 2 的误报率评估。

主要图表：

```text
assignment1_full_charts.png
assignment2_full_charts.png
assignment3_full_charts.png
```

主要数据：

```text
assignment1_full_results.csv
assignment1_full_summary.csv

assignment2_full_generation_metrics.csv
assignment2_full_detection_metrics.csv
assignment2_full_human_z.csv
assignment2_full_roc.csv
assignment2_full_deletion_attack.csv
assignment2_full_runtime.csv
assignment2_full_summary.csv

assignment3_base_series.csv
assignment3_full_watermarked_series.csv
assignment3_full_bit_detection.csv
assignment3_full_quality_metrics.csv
assignment3_full_attack_results.csv
```

## 模型路径

作业 1 和作业 2 使用本地模型：

```text
D:\LLM_weight\Qwen2.5-0.5B-Instruct
```

`Qwen2.5-1.5B-Instruct` 曾尝试下载，但当前目录中只有 tokenizer 和配置文件，没有完整权重，因此没有作为已运行实验依赖。

## 实验逻辑

作业 1：复现两种文本水印方法。  
比较 baseline、KGW、Unbiased 的 Z-score、PPL 和运行开销。

作业 2：系统评估。  
在不同参数下比较检测准确率、误报率、ROC、阈值敏感性、删除攻击鲁棒性和计算开销。

作业 3：加分方案。  
提出面向表格和时间序列基础模型的多位结构化水印，并用合成时间序列验证检测、质量损失和攻击鲁棒性。

## 重新运行

在项目根目录 `watermark-assignment` 下运行：

```powershell
$env:JUPYTER_RUNTIME_DIR=(Resolve-Path .jupyter_runtime)
$env:JUPYTER_ALLOW_INSECURE_WRITES='1'

jupyter nbconvert --to notebook --execute submission\notebooks\assignment1_complete.ipynb --output assignment1_complete.ipynb --output-dir submission\notebooks --ExecutePreprocessor.timeout=1500

jupyter nbconvert --to notebook --execute submission\notebooks\assignment2_complete.ipynb --output assignment2_complete.ipynb --output-dir submission\notebooks --ExecutePreprocessor.timeout=2400

jupyter nbconvert --to notebook --execute submission\notebooks\assignment3_complete.ipynb --output assignment3_complete.ipynb --output-dir submission\notebooks --ExecutePreprocessor.timeout=600
```
