# Arguing for E-2-E approach

A book about Deep Learning with Vector Graphics also needs to make the point why an end-to-end approach appears to be a desirable objective. End-to-end in this context shall refer to a generative Deep Learning model that is fed vector graphics and directly outputs vector graphics.

Here are a few bold claims (that still require evidence):

1. Training on SVG could be much faster since vector graphics store information much more efficiently than raster images.
2. If obtaining SVG is our goal, then the approach of using generative models for raster images and to then convert their output to vector graphics is futile. Current generative models are not conditioned to produce output that is easily converted to vector graphics. And such a conditioning would not be trivial.
3. Learning about the composition of an SVG is only possible if this information is fed into the model. A rasterised image has lost that information.
4. Many logos and icons make use of perfect symmetries and perfect 90° angles. This information could be maintained if both input and output are vector graphics.
5. Current tools for converting a raster image to a vector graphic ("tracing" or "vectorization") are still doing a poor job. Low resolution images can lead to curves and circles getting corners. Identical elements of different sizes may get traced differently.