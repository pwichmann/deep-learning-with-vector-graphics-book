# Aoki and Aizawa (2022) | SVG vector font generation for Chinese Characters with Transformer

Aoki and Aizawa propose a novel architecture to generate SVG images, in particular Chinese characters. The architecture is based on Transformers and, unlike DeepVecFont, does *not* use a differentiable renderer. Instead, they generate SVG characters from a style reference and content reference in a style transfer manner.


```{admonition} Available resources at a glance
* [arXiv URL to the paper](https://arxiv.org/pdf/2206.10329.pdf)
* Github repository with code: not available (yet)
* Project page: none (yet)
```

:::{figure-md} deepsvg_cover
<img src="cover_aoki_2022.png" alt="Aoki and Aizawa paper" width="500px">

Screenshot of the [Aoki and Aizawa (2022) paper](https://arxiv.org/pdf/2206.10329.pdf)
:::

## Data representation

Aoki and Aizawa adopt the embedding method from DeepSVG.