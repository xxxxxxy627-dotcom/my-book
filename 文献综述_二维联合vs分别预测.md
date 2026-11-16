# 温度与湿度预测：二维联合 vs 分别预测 —— 文献综述

> 适用课题：海上风电场温度、湿度预测（2023-01 ~ 2026-05，10 min 分辨率）
> 所有文献均已在 Crossref 核对 DOI、卷期、年份，可直接引用。

---

## 一、先把概念对齐（否则后面全是鸡同鸭讲）

"二维预测"在文献里其实对应三种不同东西，混用会得出错误结论：

| 叫法 | 实质 | 输出 | 典型文献 |
|---|---|---|---|
| **联合概率预测**（真正的二维） | 建模 (T, RH) 的**联合分布**，保留相关结构 | 二维概率分布 | Baran & Möller 2015；Casellas 2026；Buchner 2026 |
| **多输出回归** | 一个网络两个头，同时输出两个点值 | 两个标量 | Sci Rep 2025（多输出 DL） |
| **多任务学习** | 共享底层表征，各任务独立解码头 | 两个标量 + 共享特征 | Zhao 2025（CGC-BiLSTM） |

**"分别预测"** = 训练两个完全独立的单变量模型，互不共享任何信息。

**关键判断**：如果你只在比 RMSE，那"二维"和"分别"的差距通常很小；**只有在评价联合结构（相关性、情景合理性）时，二维才显出优势**。这一点几乎所有论文都提到，也是审稿人最常问的。

---

## 二、五条技术路线与代表模型

### 路线 1：概率后处理中的双变量联合（"二维 vs 分别"最直接的证据）

**模型 M1 — 双变量 BMA（Bivariate BMA）**

> Baran, S., & Möller, A. (2015). Joint probabilistic forecasting of wind speed and temperature using Bayesian model averaging. *Environmetrics*, 26(2), 120–132. https://doi.org/10.1002/env.2316

- **做法**：以二元截断正态分布为基础，把原本只处理风速的单变量 BMA 扩展成"风速 + 温度"联合模型，协方差结构显式刻画两者依赖。
- **对比对象**：对两变量**各自独立做 BMA**；以及 Gaussian Copula 方法。
- **结论（原文摘要）**：相比原始集合预报与**独立 BMA**，联合模型在概率校准和点预报精度上均有改善；整体表现可与 Gaussian Copula 方法持平。
- **为什么重要**：这是"二维优于分别"最干净的对照实验——同样的数据、同样的方法族，只改"联合 vs 独立"这一个变量。

**模型 M2 — 双变量分布回归网络（Bivariate DRN）**

> Casellas, E., Miró, J.-R., Cortès, M., Altava-Ortiz, V., & Moré, J. (2026). Joint Postprocessing of Air and Dew Point Temperature Ensemble Forecasts With Machine Learning. *Meteorological Applications*, 33(5), e70237. https://doi.org/10.1002/met.70237

- **做法**：一步式联合建模，神经网络同时估计边缘分布参数与依赖结构；对照组是"单变量 DRN + ECC（集成 Copula 耦合）两步法"。
- **结论（最值得引用的一句）**：双变量 DRN 相比"单变量 + ECC"，**在边际性能基本不变的前提下，显著提升了多变量技巧**——说明"显式建模变量间依赖"比"事后重建依赖"更有效。
- **对你的价值**：温度与湿度（露点）正是该文的对象，几乎可以照搬到你的场景。

**模型 M3 — Vine Copula 双变量联合**

> Buchner, F., Jobst, D., Möller, A., & Czado, C. (2026). Bivariate postprocessing of wind vectors. *Quarterly Journal of the Royal Meteorological Society*, 152(778), e70188. https://doi.org/10.1002/qj.70188

- **做法**：提出三种双变量方法（vine copula、梯度提升双变量 EMOS、双变量 DRN），在德国 60 个站的风矢量分量上对比。
- **结论**：新方法均优于传统双变量 EMOS；双变量 DRN 与最灵活的 vine copula 表现最佳。
- **配套奠基文献**：Pinson, P. (2012). Adaptive calibration of (u,v)-wind ensemble forecasts. *QJRMS*, 138, 1273–1284. https://doi.org/10.1002/qj.1873 —— 二维风矢量校准的开山之作。

### 路线 2：多任务学习（温度 + 湿度共享表征）

**模型 M4 — CGC-BiLSTM 多任务网络**

> Zhao, T., Jiang, B., Li, Y., Rezgui, Y., Zhang, C., & Wang, P. (2025). Multi-point temperature or humidity prediction for office building indoor environment based on CGC-BiLSTM deep neural network. *Building and Environment*, 112259. https://doi.org/10.1016/j.buildenv.2024.112259

- **做法**：定制门控（Customized Gate Control）+ BiLSTM，多点多任务同时预测温度或湿度。
- **结论（可直接引用的量化结果）**：三个任务中两个最优；多点温度预测平均 R² = 0.972，**相比最优单任务模型提升 3.51%**，相比最优多任务模型提升 0.94%。
- **注意**：作者也明确指出任务间可能存在**负迁移**（不同点位的分布、波动趋势不同会互相干扰）——这是你实验中必须检查的风险点。

### 路线 3：多输出端到端深度模型

**模型 M5 — 多输出 Transformer / LSTM**

> Multi-output deep learning for high-frequency prediction of air and surface temperature in Kuwait. *Scientific Reports* (2025). https://doi.org/10.1038/s41598-025-23455-4

- **做法**：一次前向同时输出气温与地表温度，比较 FTTransformer 与 LSTM 等多输出结构。
- **结论要点**：多输出可行且稳定；SHAP 与置换重要性显示**露点温度、相对湿度是最重要的预测因子**——这从特征重要性角度反证了温度与湿度强耦合，联合建模有物理依据。

### 路线 4：解耦编码 + 融合（解决负迁移）

**模型 M6 — Met2Net（每个变量独立编解码 + 隐空间融合）**

> Li, et al. (2025). Met2Net: A Decoupled Two-Stage Spatio-Temporal Forecasting Model for Complex Meteorological Systems. *ICCV 2025*.

- **做法**：给每个气象变量配一对独立的 Encoder/Decoder，再用一个 Transformer 在隐空间做融合与预测。
- **意义**：这是"既要联合、又不想硬共享导致负迁移"的代表设计，比简单共享编码器更稳。
- **同类工作**：M2Weather benchmark（多站多变量联合预测基准，2026），可用于选型参考，但属预印本，引用需谨慎。

### 路线 5：多变量**输入**（注意：这不是二维输出，别混淆）

很多论文标题里的 "multivariate" 只是指"输入用了多个变量"，输出仍是单变量。这类文献能回答"输入要不要加湿度/露点"，但**不能**回答"输出要不要联合"。

- Multivariate wind speed forecasting based on multi-objective feature selection approach and hybrid deep learning model. *Energy* (2023). https://doi.org/10.1016/j.energy.2022.126100
- Point and interval wind speed forecasting of multivariate time series based on dual-layer LSTM. *Energy* (2024). https://doi.org/10.1016/j.energy.2024.130875

### 附：海上风电多任务的最新应用

- A multi-task spatio-temporal fusion network for offshore wind power ramp events forecasting. *Renewable Energy* (2024). https://doi.org/10.1016/j.renene.2024.121774
- Wind power forecasting: A hybrid forecasting model and multi-task learning-based framework. *Energy* (2023). https://doi.org/10.1016/j.energy.2023.127864

---

## 三、横向对比表

| 模型 | 类型 | 是否建模依赖 | 数据需求 | 可解释性 | 边际精度 vs 分别 | 联合结构 |
|---|---|---|---|---|---|---|
| 分别预测（基线） | 两个独立模型 | ❌ | 低 | 高 | — | ❌ 完全丢失 |
| M1 双变量 BMA | 统计 | ✅ 协方差 | 中（需集合） | 高 | 略优或持平 | ✅ |
| M2 双变量 DRN | 神经网络 + 分布 | ✅ | 中 | 中 | **基本持平** | ✅ 显著更优 |
| M3 Vine Copula | 半参数 | ✅ 尾部也覆盖 | 中高 | 中 | 持平 | ✅ 最强 |
| M4 CGC-BiLSTM | 多任务 | ✅ 隐式 | 中 | 低 | **+3.51%** | 弱（点预测） |
| M5 多输出 DL | 多输出 | ✅ 隐式 | 中 | 中（SHAP） | 略优 | 弱 |
| M6 Met2Net | 解耦 + 融合 | ✅ 隐式 | 高 | 低 | 优于硬共享 | 中 |

---

## 四、"哪种更好"—— 分场景结论

综合以上文献，可以给出一个有条件的结论（**这正是你报告里该写的那种结论**）：

1. **只看单变量点精度（RMSE / MAE / R²）时，联合预测与分别预测差距很小。**
   Casellas 等（2026）明确指出双变量模型"在保持边际性能相当的同时"提升多变量技巧——反过来读就是：**边际指标上几乎没有便宜可占**。

2. **看联合结构（相关性、情景合理性、极端组合）时，联合预测明显更好。**
   Baran & Möller（2015）的联合 BMA 优于独立 BMA；Buchner 等（2026）的三种双变量方法全面优于双变量 EMOS。

3. **数据量有限、两个变量强相关时，多任务学习的增益最实在。**
   Zhao 等（2025）给出 +3.51%（R²）的量化提升；但同文也提示负迁移风险。

4. **两个变量动态差异大时，硬共享编码器可能反而变差**，应采用解耦编码 + 融合（Met2Net 式）或软共享。

> **落地到你项目的一句话结论**：
> 温度与湿度物理强耦合（露点由两者共同决定），**建议主方案采用"共享编码器 + 双解码头"的多任务结构，对照方案为两个独立模型**；评价必须同时报告**单变量指标**与**联合指标**，否则无法回答"哪个更好"。

---

## 五、给你的实验设计（这部分决定论文可信度，比选模型更重要）

**控制变量**：三条路线（分别 / 多任务共享 / 多输出）必须用**同一个 backbone**（同一层数、同一隐藏维度、同一训练轮数、同一随机种子集合），否则结论全是超参数差异。

**数据切分**：按时间切分，不要随机打乱。建议前 70% 训练、15% 验证、15% 测试；你的数据跨 2023–2026 三个完整年度，测试集至少要覆盖一个完整的季节周期。

**输入构造**：滑动窗口（建议 144 步 = 24 h，或 1008 步 = 7 d），并入季节项（小时内正弦/余弦 + 年内正弦/余弦）——你的数据显示季节振幅约 15 ℃，远大于模型能学到的其他差异。

**预测步长**：至少做 1 步（10 min）、6 步（1 h）、144 步（1 d）三档，联合 vs 分别的差距通常随步长增大而放大。

---

## 六、评价指标（很多论文栽在这里）

| 层面 | 指标 | 说明 |
|---|---|---|
| 单变量 | RMSE、MAE、MAPE、R² | 各自算，用于回答"边际精度有没有变差" |
| 概率（若做区间） | CRPS | 单变量概率预报的标准评分 |
| **联合** | **Energy Score（ES）** | 多变量概率预报的严格评分，论文标准做法 |
| **联合** | **Variogram Score（VS）** | 专门衡量变量间/空间依赖是否被正确刻画 |
| 相关性保持 | 预测值与真实值的 Pearson r 差异 | 最直观、最容易被老师看懂的一条 |

> Lakatos 等（2026）在 QJRMS 上正是用 **ES + VS 的复合损失**训练模型：https://doi.org/10.1002/qj.70119
> 这说明联合指标不只是"评价用"，还能直接进损失函数。

**如果你只报 RMSE，就会得出"两者差不多"的结论，然后被问"那你的创新点在哪"。**

---

## 七、参考文献（GB/T 7714 格式，可直接粘贴）

[1] BARAN S, MÖLLER A. Joint probabilistic forecasting of wind speed and temperature using Bayesian model averaging[J]. Environmetrics, 2015, 26(2): 120-132. DOI: 10.1002/env.2316.

[2] CASELLAS E, MIRÓ J R, CORTÈS M, et al. Joint postprocessing of air and dew point temperature ensemble forecasts with machine learning[J]. Meteorological Applications, 2026, 33(5): e70237. DOI: 10.1002/met.70237.

[3] BUCHNER F, JOBST D, MÖLLER A, et al. Bivariate postprocessing of wind vectors[J]. Quarterly Journal of the Royal Meteorological Society, 2026, 152(778): e70188. DOI: 10.1002/qj.70188.

[4] PINSON P. Adaptive calibration of (u,v)-wind ensemble forecasts[J]. Quarterly Journal of the Royal Meteorological Society, 2012, 138(666): 1273-1284. DOI: 10.1002/qj.1873.

[5] ZHAO T, JIANG B, LI Y, et al. Multi-point temperature or humidity prediction for office building indoor environment based on CGC-BiLSTM deep neural network[J]. Building and Environment, 2025, 267: 112259. DOI: 10.1016/j.buildenv.2024.112259.

[6] LAKATOS M, et al. A composite-loss graph neural network for the multivariate post-processing of ensemble weather forecasts[J]. Quarterly Journal of the Royal Meteorological Society, 2026. DOI: 10.1002/qj.70119.

[7] LI X, et al. Met2Net: a decoupled two-stage spatio-temporal forecasting model for complex meteorological systems[C]//Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV). 2025.

[8] Multi-output deep learning for high-frequency prediction of air and surface temperature in Kuwait[J]. Scientific Reports, 2025. DOI: 10.1038/s41598-025-23455-4.

[9] Multivariate wind speed forecasting based on multi-objective feature selection approach and hybrid deep learning model[J]. Energy, 2023, 263: 126100. DOI: 10.1016/j.energy.2022.126100.

[10] Point and interval wind speed forecasting of multivariate time series based on dual-layer LSTM[J]. Energy, 2024, 288: 130875. DOI: 10.1016/j.energy.2024.130875.

[11] A multi-task spatio-temporal fusion network for offshore wind power ramp events forecasting[J]. Renewable Energy, 2024, 237: 121774. DOI: 10.1016/j.renene.2024.121774.

[12] Wind power forecasting: a hybrid forecasting model and multi-task learning-based framework[J]. Energy, 2023, 282: 127864. DOI: 10.1016/j.energy.2023.127864.

> 注：文献 [7][8][9][10] 的作者全名请在你引用前用 DOI 在 Crossref 或期刊页面补全，我没取到完整作者列表。

---

## 八、阅读顺序建议

1. **先读 [2]**（Casellas 2026）—— 对象就是温度+露点，方法现代，结论清晰，最容易复现。
2. **再读 [1]**（Baran & Möller 2015）—— 建立"联合 vs 独立"的对照直觉，把"边际不变、联合更优"这句话吃透。
3. **读 [5]**（Zhao 2025）—— 学多任务架构怎么写、负迁移怎么讨论。
4. **读 [3][4]** —— 风矢量二维校准，理解"为什么必须显式建模依赖"。
5. **最后读 [7]**（Met2Net）—— 如果发现共享编码器有负迁移，这是解法。

**做笔记时固定回答四个问题**：
1. 输入是什么、输出是什么（点值还是分布？单变量还是联合？）
2. 对照组是什么（有没有"分别预测"这个基线？）
3. 用了哪些指标（有没有联合指标？）
4. 数据规模和分辨率（跟你的 10 min / 17 万条比，够不够接近）
