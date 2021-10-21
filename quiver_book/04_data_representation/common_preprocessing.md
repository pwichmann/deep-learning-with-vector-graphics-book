# Common SVG preprocessing

Depending on the training dataset, it may be useful to preprocess all input SVGs to avoid unnecessary complexity. SVGs found in the wild may contain all kinds of oddities.

Not all steps may have to be re-implemented. Tools like [SVGO](https://github.com/svg/svgo/) can be configured to perform at least some of these steps.

Ideally, the preprocessed SVGs are exported again so that results can be visually inspected and preprocessing errors can be spotted as early as possible and before a training is initiated.