# Open questions

The following are the big questions the author is currently unable to answer.
If you would have any ideas to contribute, please use the [issues function of Github for this project](https://github.com/pwichmann/deep-learning-with-vector-graphics-book/issues).

The focus is on deep generative models for SVG images.

## Open question: Is information in 'raster space' required or not?

### Background

Some existing generative models for SVG are end-to-end and exclusively work in the 'SVG space'. These models encode SVGs into a vector and solely train on and work with the vector image data representation. Examples are DeepSVG by Carlier et al. (2020) and Aoki and Aizawa (2022).

Other models include information from the 'raster space', e.g. Wang and Lian (2021). These models either feed in a raster image data representation in addition to the vector image data representation or they use a differentiable rasterizer to obtain a loss in the raster image space.

### Observations

The current impression is that models that only work in the 'SVG space' perform less well. An exception appears to be the recent work by Aoki and Aizawa (2022) which appears to outperform DeepSVG and DeepVecFont.

### Question

1. Is information from the 'raster space' required for a generative model to work well and produce high-quality SVGs that are indistinguishable from human-designed ones?
1. If so, what are the reasons?
1. How can a model deal with the multitude of options how to represent the identical raster image as an SVG?
1. Without the complex knowledge how an SVG is actually rendered to a pixel graphic, how can a generative model possibly have sufficient information how a concept should be designed in SVG space? To us humans, it matters how the resulting image looks like. It matters less, how identically looking magnifying glass icons are specifically designed, e.g. where anchor points are placed.

Regarding the last question

