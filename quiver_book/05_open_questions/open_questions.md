# Open questions

The following are the big questions the author is currently unable to answer.
If you would have any ideas to contribute, please use the [issues function of Github for this project](https://github.com/pwichmann/deep-learning-with-vector-graphics-book/issues).

The focus is on deep generative models for SVG images.

## Open question #1: Is information in 'raster space' required or not?

One major open question for the author is if a generative model can work well just using SVG information -- or if the model requires information from the 'raster space', such as via a differentiable renderer (diffvg). For us humans, it really matters how the image looks like rendered onto a display in the end.

### Background

Some existing generative models for SVG are end-to-end and exclusively work in the 'SVG space'. These models encode SVGs into a vector and solely train on and work with the vector image data representation. Examples are DeepSVG by Carlier et al. (2020) and Aoki and Aizawa (2022).

Other models include information from the 'raster space', e.g. Wang and Lian (2021). These models either feed in a raster image data representation in addition to the vector image data representation or they use a differentiable rasterizer to obtain a loss in the raster image space.

### Observations

The current impression is that models that only work in the 'SVG space' perform less well. An exception appears to be the recent work by Aoki and Aizawa (2022) which appears to outperform DeepSVG and DeepVecFont.

### Related sub-questions

1. Is information from the 'raster space' required for a generative model to work well and produce high-quality SVGs that are indistinguishable from human-designed ones?
1. If so, what are the reasons?
1. How can a model deal with the multitude of options how to represent the identical raster image as an SVG?
1. Without the complex knowledge how an SVG is actually rendered to a pixel graphic, how can a generative model possibly have sufficient information how a concept should be designed in SVG space? To us humans, it matters how the resulting image looks like. It matters less, how identically looking magnifying glass icons are specifically designed, e.g. where anchor points are placed.
1. Is it better to split the problem into two parts? A generative model trained on and working in raster space -- and a vectorization model? Or is it better, as it commonly is in Deep Learning, to use an end-to-end model?
1. Is a model like DALL-E that works with grids of pixels not much better positioned to create interpolations -- compared to a model that works with SVG and thus nested, separate elements?