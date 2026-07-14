# LLM 水印技术实验项目

本仓库为课程最终提交版本，包含三项实验的代码、数据、结果图表和最终论文。

队员：

- 郑胜文
- 胡辉宇

## 项目内容

本项目在小型开源大语言模型上复现并评估两类文本水印方法，并进一步提出面向表格/时间序列生成数据的结构化残差水印方案。

- 作业 1：复现 KGW 绿名单水印和 Unbiased 概率重加权水印。
- 作业 2：系统评估检测性能、误报率、鲁棒性、文本质量和运行开销。
- 作业 3：设计并验证结构化时间序列残差水印方案。

## 仓库结构

```text
submission/
├── README.md
├── final_report.tex          # 论文 LaTeX 源文件
├── final_report.pdf          # 最终论文 PDF
├── final_report.docx         # 最终论文 Word 版本
├── 要求.jpg                  # 作业要求截图
├── data/
│   ├── prompts.json          # 中文测试 prompts
│   └── human_texts.json      # 人类文本负样本
├── notebooks/
│   ├── assignment1_enhanced.ipynb
│   ├── assignment2_enhanced.ipynb
│   └── assignment3_enhanced.ipynb
└── outputs/
    ├── assignment1_*         # 作业 1 结果表与图
    ├── assignment2_*         # 作业 2 结果表与图
    └── assignment3_*         # 作业 3 结果表与图
```

## 环境依赖

- Python 3.12
- PyTorch 2.8+（CUDA）
- Transformers
- pandas、numpy、matplotlib、seaborn、scikit-learn、scipy、tqdm
- Jupyter / nbconvert
- 模型：Qwen2.5-0.5B-Instruct

安装示例：

```bash
pip install torch transformers pandas numpy matplotlib seaborn scikit-learn scipy tqdm jupyter nbconvert
```

## 运行方式

进入 notebook 目录后按顺序执行：

```bash
cd notebooks

jupyter nbconvert --to notebook --execute assignment1_enhanced.ipynb \
  --output assignment1_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=1500

jupyter nbconvert --to notebook --execute assignment2_enhanced.ipynb \
  --output assignment2_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=2400

jupyter nbconvert --to notebook --execute assignment3_enhanced.ipynb \
  --output assignment3_enhanced.ipynb --output-dir . --ExecutePreprocessor.timeout=600
```

Notebook 会将实验结果保存到 `outputs/` 目录。最终论文引用的图表也来自该目录。

## 最终报告

最终论文文件位于仓库根目录：

- `final_report.pdf`
- `final_report.docx`
- `final_report.tex`

如需重新编译 PDF，可在根目录执行：

```bash
xelatex final_report.tex
xelatex final_report.tex
```
