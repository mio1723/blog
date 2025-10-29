---
title: RAE
pubDate: 2025-10-29
lastModDate: 2025-10-29
toc: true
share: true
search: true
draft: false
---

### 概述
RAE 的看点, 就是能否直接在像 DINO 这样的 VFM (Video Foundation model) 上训出一个 Diffusion 来. 之前我们都是在 VAE 的 latent space 上训 Diffusion, 而 RAE 验证了在 VFM 的 feature space 上训 Diffusion 的可行性. 虽然最终的 FID 指标看起来还行, 但是重建的效果不太理想. 这类结构仍然需要进一步探索.

### Method
- Stage 1: 冻结 Encoder 训练一个 ViT Decoder. 用 LPIPS + L1 + GAN loss
	- noise augmented decoding: 就是给 clean latent 加上一个噪声再 decode. 更利于 Decoder 处理 Stage 2 DiT 生成的比较 smooth 的 latent.
- Stage 2: 冻结 Encoder Decoder, 在 latent space 上训 DiT. 但是 DiT 有如下调整
	- 将 DiT width 调整至 $\geq$ Token width
	- 调整 noise scheduling
具体细节请参考[原文章](https://www.alphaxiv.org/abs/2510.11690)

### 一些观察
- 文章提到 RAE 的 DiT 训练过程相比于 REPA, SiT 大大加速: 应该是因为 DINO 已经学到了一个结构良好的 feature space, DiT 在这个空间上训练自然就更容易.
- SD-VAE baseline 时间较早. 是否应该和近期的/训练集更 Diverse 的 VAE variant 比较?
- 没有给出 PSNR, SSIM 等等


### 一些观点
1. 目前比较强的理解模型都丢高频信息. DINO 等等 encoder 可能仍然不够强大.
2. 取 Patch 的行为破坏了 low-level 能力. 如果能在 raw pixel 上做会更好, 但是太贵了而且目前似乎没有较为统一的范式.
3. gFID 很小只能说明, 模型本身学到了 ImageNet 1k 模式的一个超集, 它比 in1k 更懂它自己.  其次 FID 指标本身已经失效, 因为 Inception v3 本身就是在 ImageNet 上训的, 提取的是 ImageNet 上的特征. 那么计算 FID 的时候, 就是在考察模型有没有理解 in 上的图片特征. 这对于当今强大的 foundation model 是十分荒谬的, 因为这就像让一个学生给老师打分一样, 分数肯定会很高, 但是有多少实际参考价值就难说了.

### 我们关心什么
- FID, PSNR 等等指标固然重要, 这方便我们迅速了解一个 model 的能力. 但是我们需要更好的评估方式, 更 human-aligned, semantic sensitive 的指标体系. 如何设计出能够衡量 foundation 模型时代的 "表征质量" 新 metric 也是一个关键的问题.
- 更进一步, 我们其实不太关心最新的模型能否在一个 tiny dataset 上获得更好的指标, 而是更关心这个 model: 
	- 能否在 open domain 上 (训练集之外) 保持稳定?
	- 能否与 foundation model (DINOv2, CLIP) 对齐?
	- 能否在 Gen.(generation) 和 Und.(understanding) 之间共享同一个 latent 空间?
	- 能否在质量参差的 web-scale 数据上泛化?
	- 能否真正 scale up?
	- 是否最终能达到 Gen. 和 Und. 1 + 1 > 2 的效果?

### 问题
- ? 高分辨率训练会自然提升 FID
- ? DINOv3 不能很好适配 RAE. 可能是因为其 feature 太 sparse, 不太符合 generative model 对 feature space 的假设: *smooth, continuous*. 但是通过 PCA 得到的 DINO v3 特征图实际上非常细腻.
- ? VAE latent dim 越大 生成越难学

### 计划
- 同期另外三篇文章: SVG, VFM-VAE, AlignTok. 阅读并对比后再更新 (会 🕊 吗)
- DINO 系列文章
- High-Resolution Image Generation 领域
- UMM 领域

### Citation
本文**大量**参考了知乎以及小红书上的相关讨论. 十分建议有兴趣的进一步了解 \:3

:link[如何评价谢赛宁团队发表的新作 RAE]{id=https://www.zhihu.com/question/1961750679057031835 class=rounded}  
:link[RAE 搜索]{id="https://www.xiaohongshu.com/search_result?keyword=RAE" img="https://api.iconify.design/simple-icons:xiaohongshu.svg?color=%23ff2442" class=rounded}
