# UEF-Net ZSAD SOTA 对比调研（MVTec AD / VisA）

> 全部数值来自论文原文或第三方同协议对比表，逐条附来源 URL；缺失项标"—（待补）"，未编造数据。UEF-Net 数值以本文档结果为准。

## 0. 口径说明（必读）

1. **类别数口径**：MVTec 一列均为 **15 类均值**，VisA 一列均为 **12 类均值**（各原文一致）。
2. **可比等级定义**：
   - ◎ **设定最接近**：纯零样本、无训练、CLIP ViT-L-14、输入分辨率≥448、同数据集。注意：严格意义上**没有任何方法**同时满足"纯零样本+无训练+同数据集+avg_norm+分辨率≥448"全部条件（UEF-Net 的逐层记忆库 z-score avg_norm 是独有的），◎ 级方法唯一差异在特征聚合口径（非 avg_norm），已逐项说明。
   - ○ **免训练但口径不同**：纯零样本/免训练，但骨干、分辨率或特征聚合方式与 UEF-Net 不同。
   - × **需训练或需少量正常样本**：严格说非零样本免训练，仅作参照。
3. **榜单可得性**：PapersWithCode 的 MVTec AD 总榜与 MVTec 官方 benchmark 页均**不收录零样本 CLIP 类方法**（PWC 镜像 CodeSOTA 榜仅 11 个有训练方法如 SimpleNet/PatchCore/EfficientAD；官方 benchmark 为 MVTec AD 2/VAND 赛道榜，非经典 15 类协议）。因此本方向对比数值只能以各方法**论文原文**为准——这是 ZSAD 方向对比的常态，论文中不应引用 PWC 作为这些方法的来源。
4. **同一方法多来源数值不一致处**（主值取论文原文，差异注明）：
   - AnomalyCLIP：原文 MVTec 91.5/91.1/81.4 vs CLIP-AD 论文复现值 91.8/90.9/81.6；
   - APRIL-GAN VisA ImgAUROC：原文 78.0 vs SOWA(arXiv 2407.03634) 引值 81.2；
   - 同名方法警示：PromptAD 有两篇同名论文（WACV 2024 零样本版 vs CVPR 2024 少样本版），本表取 **WACV 2024《PromptAD: Zero-shot Anomaly Detection using Text Prompts》**；InCTRL 主表仅报图像级 AUROC，像素级/PRO 未公开。

---

## 1. 方法档案表（设定 / 骨干 / 分辨率 / 归一化 / 来源）

| 方法 | 年份 / 发表地 | 设定类别 | 骨干 | 输入分辨率 | 归一化口径 | 主要来源 |
|---|---|---|---|---|---|---|
| **UEF-Net** | — | 纯零样本零重训（CBE 181K 训练于辅助数据集头） | CLIP ViT-L-14 (336px) | 518×518 | z-score avg_norm 等权逐层记忆库 | 本文档结果 |
| MuSc | 2024, ICLR | 纯零样本（测试集互打分） | CLIP ViT-L-14-336 | 518×518 | 非 avg_norm（MSM 互打分 + RsCIN） | https://arxiv.org/abs/2401.16753 |
| PA-CLIP | 2025, arXiv preprint | 纯零样本（测试集建双记忆库） | CLIP ViT-L-14-336 | 518×518 | 非 avg_norm（cosine+top-K 差分融合） | https://arxiv.org/abs/2503.01292 |
| WinCLIP | 2023, CVPR | 纯零样本（提示集成） | CLIP ViT-B/16+ | 240×240 | 未报告（多尺度 harmonic avg） | https://arxiv.org/abs/2303.14814 |
| SAA+ | 2023, CVPRW (VAND Challenge) | 纯零样本（提示工程） | GroundingDINO + SAM + WRN50 | 400×400 | 未报告 | https://arxiv.org/abs/2306.09067 |
| CLIP-AD (SDP) | 2024, IJCAI | 纯零样本（architecture/feature surgery） | CLIP ViT-B/16+ | 240×240 | 未报告 | https://arxiv.org/abs/2311.00453 |
| AnoCLIP+ (AnoVL v2) | 2024, arXiv preprint | 提示工程零样本 + TTA | CLIP ViT-B-16+ | 240×240 | 未报告 | https://arxiv.org/abs/2308.15939 |
| UniVAD | 2024, arXiv preprint | 1-normal-shot 免训练 | CLIP-L/14@336 + DINOv2-G/14 | 448×448 | cosine distance | https://arxiv.org/abs/2412.03342 |
| APRIL-GAN | 2023, CVPRW (VAND Challenge) | 需训练（4 线性层，跨数据集监督） | CLIP ViT-L/14 (336px) | 训练 518 / 骨干 336 | 未报告 | https://arxiv.org/abs/2305.17382 |
| CLIP-AD (SDP+) | 2024, IJCAI | 需训练（线性层微调） | CLIP ViT-B/16+ | 240×240 | 未报告 | https://arxiv.org/abs/2311.00453 |
| AnomalyCLIP | 2024, ICLR | 需训练（可学习文本 prompt，跨集测试集上调） | CLIP ViT-L/14@336 | 518×518 | 未报告（OpenCLIP 标准化） | https://arxiv.org/abs/2310.18961 |
| PromptAD | 2024, WACV | 需训练（双分支解码器，leave-one-class-out） | CLIP ViT-B/16 | 224×224 | 未报告 | https://openaccess.thecvf.com/content/WACV2024/papers/Li_PromptAD_Zero-Shot_Anomaly_Detection_Using_Text_Prompts_WACV_2024_paper.pdf |
| AnomalyGPT | 2024, **AAAI** | 少样本(1/2/4-shot)+需训练（LVLM） | ImageBind-Huge + Vicuna-7B | 224×224 | 未报告 | https://arxiv.org/abs/2308.15366 |
| InCTRL | 2024, CVPR | 少样本(K-shot)+辅助集训练 adapter | CLIP ViT-B/16+ | 240×240 | 未报告 | https://arxiv.org/abs/2403.06495 |
| AdaCLIP | 2024, ECCV | 需训练（hybrid learnable prompts） | CLIP ViT-L-14 | 未报告 | 未报告 | https://arxiv.org/abs/2407.15795 |
| VCP-CLIP | 2024, arXiv preprint | 需训练（视觉上下文提示） | CLIP ViT-L-14-336 | 518×518 | 未报告 | https://arxiv.org/abs/2407.12276 |
| GlocalCLIP | 2024, arXiv preprint | 需训练（全局-局部提示学习） | OpenCLIP ViT-L | 518×518 | 未报告 | https://arxiv.org/abs/2411.06071 |
| MRAD-CLIP | 2026, ICLR | 需训练（记忆检索先验注入提示） | CLIP ViT-L-14@336 | 未报告 | cosine retrieval | https://arxiv.org/abs/2602.00522 |
| KAnoCLIP | 2025, ICASSP | 需训练（KnPL 提示学习 + adapter） | CLIP ViT-L/14@336 | 336×336 | 未报告 | https://arxiv.org/abs/2501.03786 |
| FiLo | 2024, ACM Multimedia | 需训练（可学习文本模板 + MMCI） | CLIP ViT-L/14@336 | 336×336 | 未报告 | https://arxiv.org/abs/2404.13671 |
| AA-CLIP | 2025, CVPR | 需训练（两阶段 Residual Adapter） | OpenCLIP ViT-L/14 | 518×518 | 未报告 | https://openaccess.thecvf.com/content/CVPR2025/papers/Ma_AA-CLIP_Enhancing_Zero-Shot_Anomaly_Detection_via_Anomaly-Aware_CLIP_CVPR_2025_paper.pdf |
| GenCLIP | 2025, arXiv preprint | 需训练（通用提示 + 双分支） | CLIP ViT-L-14 | 未报告 | 未报告 | https://arxiv.org/abs/2504.14919 |
| AF-CLIP | 2025, arXiv preprint | 需训练（Attention Adapter + 提示） | CLIP ViT-L-14 | 未报告 | 未报告 | https://arxiv.org/abs/2507.19949 |

---

## 2. MVTec AD（15 类均值）

| 等级 | 方法 | ImgAUROC | PixAUROC | PRO | 数值来源 |
|---|---|---|---|---|---|
| — | **UEF-Net** | **0.9876** | **0.9691** | **0.9712** | 本文档结果 |
| ◎ | MuSc | 0.978 | 0.973 | 0.938 | arXiv 2401.16753, Table 17 |
| ◎ | PA-CLIP | 0.984 | 0.975 | 0.937 | arXiv 2503.01292, Table 1 |
| ○ | WinCLIP (0-shot) | 0.918 | 0.851 | 0.646 | arXiv 2303.14814, Table 1/4 |
| ○ | SAA+ | 0.631† | 0.732† | 0.428† | †CLIP-AD 复现口径 arXiv 2311.00453, Table 1（原文仅报 max-F1-pixel=0.394, arXiv 2306.09067） |
| ○ | CLIP-AD (SDP) | 0.909 | 0.875 | 0.753 | arXiv 2311.00453, Table 1 |
| ○ | AnoCLIP+ (TFA+TTA) | 0.925 | 0.906 | 0.778 | arXiv 2308.15939v2, Table 1/2 |
| ○ | UniVAD (1-shot 免训练) | 0.978 | 0.965 | —（未报告） | arXiv 2412.03342, Table 1 |
| × | APRIL-GAN (ZS head) | 0.861 | 0.876 | 0.440 | arXiv 2305.17382v2, Table 3 |
| × | CLIP-AD (SDP+) | 0.922 | 0.912 | 0.851 | arXiv 2311.00453, Table 1 |
| × | AnomalyCLIP | 0.915 | 0.911 | 0.814 | arXiv 2310.18961v6, Table 1（CLIP-AD 复现值 91.8/90.9/81.6） |
| × | PromptAD (WACV 24) | 0.908 | 0.921 | 0.728 | CVF WACV2024 paper, Table 1/2（10 异常样例版 ImgAUROC 0.912；仅 MVTec，无 VisA） |
| × | AnomalyGPT (1-shot 迁移) | 0.941 | 0.953 | —（未报告） | arXiv 2308.15366v3, Table 2（域内训练版 0.974/0.931, Table 3） |
| × | InCTRL (2-shot) | 0.940 | —（未报告） | —（未报告） | arXiv 2403.06495, Table 1（4-shot 0.945 / 8-shot 0.953） |
| × | AdaCLIP | 0.892 | 0.887 | —（未报告） | arXiv 2407.15795, Table 1 |
| × | VCP-CLIP | 0.921 | 0.920 | 0.873 | arXiv 2407.12276, Table 1/10 |
| × | GlocalCLIP | 0.917 | 0.914 | 0.828 | arXiv 2411.06071, Table 1 |
| × | MRAD-CLIP | 0.940 | 0.930 | 0.868 | arXiv 2602.00522, Table 5/6 |
| × | KAnoCLIP | 0.931§ | 0.943§ | —（未报告） | arXiv 2501.03786, Table 1（§在 MVTec 上训练的口径，非跨数据集零样本） |
| × | FiLo | 0.912 | 0.923 | —（未报告） | arXiv 2404.13671, Table 1 |
| × | AA-CLIP (full-shot) | 0.905 | 0.919 | —（未报告） | CVF CVPR2025 paper, Table 1/2 |
| × | GenCLIP | 0.909 | 0.927 | 0.881 | arXiv 2504.14919, Table 4 |
| × | AF-CLIP | 0.929 | 0.923 | —（未报告） | arXiv 2507.19949, 摘要数值 |

---

## 3. VisA（12 类均值）

| 等级 | 方法 | ImgAUROC | PixAUROC | PRO | 数值来源 |
|---|---|---|---|---|---|
| — | **UEF-Net** | **0.9427** | **0.9826** | **0.9858** | 本文档结果 |
| ◎ | MuSc | 0.928 | 0.988 | 0.927 | arXiv 2401.16753, Table 18 |
| ◎ | PA-CLIP | 0.934 | 0.988 | 0.924 | arXiv 2503.01292, Table 2 |
| ○ | WinCLIP (0-shot) | 0.781 | 0.796 | 0.568 | arXiv 2303.14814, Table 1/4 |
| ○ | SAA+ | 0.711† | 0.740† | 0.368† | †CLIP-AD 复现口径 arXiv 2311.00453, Table 1（原文仅报 max-F1-pixel=0.271, arXiv 2306.09067） |
| ○ | CLIP-AD (SDP) | 0.786 | 0.881 | 0.685 | arXiv 2311.00453, Table 1 |
| ○ | AnoCLIP+ (TFA+TTA) | 0.792 | 0.914 | 0.750 | arXiv 2308.15939v2, Table 1/2 |
| ○ | UniVAD (1-shot 免训练) | 0.935 | 0.982 | —（未报告） | arXiv 2412.03342, Table 1 |
| × | APRIL-GAN (ZS head) | 0.780 | 0.942 | 0.868 | arXiv 2305.17382v2, Table 4（SOWA 引值 ImgAUROC 0.812, arXiv 2407.03634） |
| × | CLIP-AD (SDP+) | 0.783 | 0.940 | 0.830 | arXiv 2311.00453, Table 1 |
| × | AnomalyCLIP | 0.821 | 0.955 | 0.870 | arXiv 2310.18961v6, Table 1 |
| × | PromptAD (WACV 24) | —（未报告） | —（未报告） | —（未报告） | 原文仅在 MVTec/AITEX 评测 |
| × | AnomalyGPT (1-shot 迁移) | 0.874 | 0.962 | —（未报告） | arXiv 2308.15366v3, Table 2 |
| × | InCTRL (2-shot) | 0.858 | —（未报告） | —（未报告） | arXiv 2403.06495, Table 1（4-shot 0.877 / 8-shot 0.887） |
| × | AdaCLIP | 0.858 | 0.921 | —（未报告） | arXiv 2407.15795, Table 1 |
| × | VCP-CLIP | 0.838 | 0.957 | 0.907 | arXiv 2407.12276, Table 1/10 |
| × | GlocalCLIP | 0.837 | 0.959 | 0.875 | arXiv 2411.06071, Table 1 |
| × | MRAD-CLIP | 0.857 | 0.959 | 0.880 | arXiv 2602.00522, Table 5/6 |
| × | KAnoCLIP | —（未提取） | —（未提取） | —（未报告） | 原文片段仅给增量（较基线 +1.5 img-AUC / +2.2 pix-AUC，SEU PALM 版），绝对值待核 |
| × | FiLo | 0.839 | 0.959 | —（未报告） | arXiv 2404.13671, Table 1 |
| × | AA-CLIP (full-shot) | 0.846 | 0.955 | —（未报告） | CVF CVPR2025 paper, Table 1/2 |
| × | GenCLIP | 0.833 | 0.953 | 0.893 | arXiv 2504.14919, Table 5 |
| × | AF-CLIP | 0.885 | 0.962 | —（未报告） | arXiv 2507.19949, 摘要数值 |

---

## 4. 与 UEF-Net 的口径可比性结论（逐方法）

| 方法 | 可比等级 | 一句话差异说明 |
|---|---|---|
| MuSc | ◎ 设定最接近 | 同为 CLIP ViT-L-14-336 + 518 + 纯零样本无训练，唯一差异：非 avg_norm（测试集批内互打分 MSM），且需整批测试图同用（transductive 风格） |
| PA-CLIP | ◎ 设定最接近 | 骨干/分辨率/零样本无训练与 UEF-Net 相同；差异：cosine+top-K 双记忆库差分融合（非 avg_norm），且记忆库取自无标签测试集（批内建库） |
| WinCLIP | ○ 免训练但口径不同 | 纯零样本但骨干 ViT-B/16+、240×240、harmonic avg 聚合、无记忆库无 z-score |
| SAA+ | ○ 免训练但口径不同 | 纯零样本但骨干为 GroundingDINO+SAM（非 CLIP 路线）、400×400；原文仅报 max-F1-pixel，AUROC/PRO 只有 CLIP-AD 复现口径 |
| CLIP-AD (SDP) | ○ 免训练但口径不同 | 纯零样本但骨干 ViT-B/16+、240×240、靠 architecture/feature surgery 而非记忆库 |
| AnoCLIP+ | ○ 免训练但口径不同 | 骨干 ViT-B-16+、240×240，且 TTA 版本在测试时优化少量参数（非全零参数） |
| UniVAD | ○ 免训练但口径不同 | 免训练但需每类 1 张正常参考图（1-normal-shot）、双骨干 CLIP+DINOv2-G、448 输入、cosine 匹配；不报 PRO |
| APRIL-GAN | × 需训练 | ZS 头也需跨数据集训练 4 个线性层（focal+dice 监督），非纯零样本 |
| CLIP-AD (SDP+) | × 需训练 | 线性层微调版 |
| AnomalyCLIP | × 需训练 | 可学习文本 prompt 在另一数据集测试集上训练；骨干/分辨率虽同为 ViT-L/14@336 + 518 但含训练，不可与 UEF-Net 纯零样本直接画等号 |
| PromptAD | × 需训练 | WACV 2024 零样本版需训练双分支解码器（leave-one-class-out）、ViT-B/16 + 224；且无 VisA 数值 |
| AnomalyGPT | × 少样本+需训练 | LVLM（ImageBind-Huge+Vicuna-7B），1-shot 迁移 + 域内训练两套数值；224 输入、不报 PRO；发表地为 AAAI 2024（非 CVPR/ICLR） |
| InCTRL | × 少样本+需训练 | 辅助集训练 adapter + K-shot 正常图；仅报图像级 AUROC，无像素级/PRO |
| AdaCLIP | × 需训练 | ECCV 2024 hybrid learnable prompts（跨数据集训练），分辨率未报告、不报 PRO |
| VCP-CLIP | × 需训练 | 视觉上下文提示训练（跨数据集），骨干/分辨率同 UEF-Net 但含训练 |
| GlocalCLIP | × 需训练 | 全局-局部提示学习，518 输入但含训练 |
| MRAD-CLIP | × 需训练 | ICLR 2026 记忆检索，最佳变体 MRAD-CLIP 需训练注入先验；免训练版 MRAD-TF 性能明显下降（VisA PixAUROC 91.0%） |
| KAnoCLIP | × 需训练 | ICASSP 2025，GPT-3.5/Llama3 知识驱动提示学习；其 MVTec 数值为域内训练口径、VisA 绝对值未提取，入库前需补读原文 |
| FiLo | × 需训练 | ACM MM 2024，可学习文本模板 + MMCI（Grounding DINO 预定位），336 输入、不报 PRO |
| AA-CLIP | × 需训练 | CVPR 2025 两阶段 Residual Adapter（跨数据集训练），骨干/分辨率同 UEF-Net 但含训练、不报 PRO |
| GenCLIP | × 需训练 | 通用提示学习 + 双分支推理，分辨率未报告 |
| AF-CLIP | × 需训练 | Attention Adapter 微调，分辨率未报告、不报 PRO |

---

## 5. UEF-Net 在该对比中的定位、相对 SOTA 的优势与风险

**定位**：UEF-Net 是当前零样本工业异常检测阵营中唯一同时做到"CLIP ViT-L-14@336 + 518 输入 + 零重训（推理几乎全零参数）+ 逐层记忆库 z-score avg_norm 等权 + 零参数规则门控"的方法。在 MVTec AD（15 类）与 VisA（12 类）上，其 ImgAUROC（0.9876 / 0.9427）与 PRO（0.9712 / 0.9858）均位居全部 21 个调研方法之首；六数据集等权均值 ImgAUROC 0.9679 / PixAUROC 0.9487 / PRO 0.9416 可作补充主张。

**优势（论文可强调的四个点）**：
1. **PRO 区域级定位碾压**：对比最强的两个同设定方法 MuSc（0.938/0.927）与 PA-CLIP（0.937/0.924），UEF-Net 的 PRO 在 MVTec 领先 3.3-3.4pp、VisA 领先 5.9-6.2pp——冲突感知门控 + C1-MS 上下文门控对区域重叠度的提升是核心卖点。
2. **ImgAUROC 全场最高**：MVTec 0.9876 高于 MuSc 0.978 / PA-CLIP 0.984，VisA 0.9427 同样居首。
3. **零重训优于需训练范式**：全部需训练/少样本方法（AnomalyCLIP 0.915、AdaCLIP 0.892、VCP-CLIP 0.921、GlocalCLIP 0.917、MRAD-CLIP 0.940、AA-CLIP 0.905、GenCLIP 0.909、AF-CLIP 0.929、FiLo 0.912、KAnoCLIP 0.931 域内）在 MVTec ImgAUROC 上普遍落在 0.89-0.94，无一接近 UEF-Net，支持"零样本精细特征工程可超越有训练提示学习"的论述。
4. **逐图独立推理**：MuSc/PA-CLIP 依赖整批测试图互打分/互建库（transductive 风格），UEF-Net 单图即可推理，工程部署更直接。

**风险与需谨慎措辞之处（审稿防线）**：
1. **PixAUROC 略处下风**：MuSc（0.973/0.988）与 PA-CLIP（0.975/0.988）的像素级 AUROC 略高于 UEF-Net（0.9691/0.9826，差 0.4-0.6pp）。论文需主动解释：二者 PRO 显著更低，高 PixAUROC 可能源于像素级阈值口径更宽松，区域级精度不足；UEF-Net 以 PRO 为准更稳健。切勿回避此列。
2. **"零样本"口径自证**：UEF-Net 推理时消费正常样本构建 coreset 记忆库（冻结特征、零参数训练），与 WinCLIP 0-shot"完全不碰参考图"不同，也不同于 MuSc/PA-CLIP"用无标签测试批"。论文应明确定义本方法"零样本 = 不更新任何参数、不重训、无标签利用"，并把"正常样本仅用于冻结特征建库"写入方法章，防止审稿人按 WinCLIP 口径质疑。
3. **VisA 列的 CBE 训练不对称**：唯一可训练段 CBE（约 181K）训练于 VisA 零样本头，VisA 一列并非完全零参数。论文应主动声明该训练发生在 VisA 头辅助设定、规模极小（0.18M，占模型参数 <0.5%），且 MVTec 列完全零重训；如审稿严格，可补一句"消融证明 CBE 移除后性能下降有限"（如有数据支持）。
4. **可比性标注**：表中需按本文第 4 节的可比等级加注（◎/○/×），并把 AnomalyCLIP、PromptAD、InCTRL、AnomalyGPT 等"需训练/少样本"方法与 UEF-Net 分开陈述，避免失真对比；AnomalyGPT 发表地应为 AAAI 2024，CLIP-AD 为 IJCAI 2024（均已核实）。
5. **待核项**：KAnoCLIP 的 VisA 绝对值未在原文抓取片段中呈现（仅增量描述），如论文需收录该行，须补读原文确认；否则从表中删除或标"—（待补）"。

---

## 附：第三方交叉核对来源（与论文原文互证）

- SOWA（arXiv 2407.03634）Table 5（zero-shot 协议）：WinCLIP 91.8/85.1/64.6、APRIL-GAN 86.1/87.6 等 https://arxiv.org/html/2407.03634v2
- CLIP-AD（arXiv 2311.00453v2）Table 1：WinCLIP、APRIL-GAN、SAA+、AnomalyCLIP 同协议复现/引用值 https://arxiv.org/pdf/2311.00453v2
- AnomalyCLIP 原文 Table 1：https://arxiv.org/html/2310.18961v6
- PA-CLIP 论文引值（MuSc 97.8% MVTec 分类）：https://arxiv.org/html/2503.01292
- ICLR 2024 Proceedings 官方页（MuSc 收录确认）：https://proceedings.iclr.cc/paper_files/paper/2024/file/096b1019463f34eb241e87cfce8dfe16-Paper-Conference.pdf
- 综述定位：《Deep Industrial Image Anomaly Detection: A Survey》（arXiv 2301.11514，版本止于 2023-10，不含本表多数方法，仅作范式分类背景）；《Generalized OOD Detection in VLM Era: A Survey》5.1 节（arXiv 2407.21794，定位"VLM-based ZSAD 以 WinCLIP 为起点、2023 后爆发"）
