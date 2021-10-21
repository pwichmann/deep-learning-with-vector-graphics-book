# Wang and Lian (2021) | DeepVecFont: Synthesizing High-quality Vector Fonts via Dual-modality Learning

Wang and Lian specifically address the problem of generating vector glyphs obtained from fonts. The authors build upon the work from Carlier et al. (2020), Ha and Eck (2017), and Lopes et al. (2019). The authors aim to tackle the problem that previous works were not able to synthesize visually pleasing vector glyphs and name two reasons for this issue:
1. Single modality -- they propose to use a dual modality with vector data representation **and** raster image data representation
2. Location shift issue brought by the Mixture Distribution Network -- they propose to employ a differentiable rasterizer (developed in the meantime by Li et al. (2020)) for imposing an additional restriction on the drawing commands predicted by the MDN


This paper was submitted to SIGGRAPH Asia 2021 and published in ACM Transactions on Graphics.


```{admonition} Available resources at a glance
* [arXiv URL to the paper](https://arxiv.org/pdf/2110.06688.pdf)
* [Github repository with code](https://github.com/yizhiwang96/deepvecfont)
```


:::{figure-md} wang_and_lian_2021_cover
<img src="wang_and_lian_2021_cover.png" alt="Wand and Lian 2021 paper" width="600px">

Screenshot of the DeepVecFont [paper](https://arxiv.org/pdf/2110.06688.pdf) by Wang and Lian (2021)
:::