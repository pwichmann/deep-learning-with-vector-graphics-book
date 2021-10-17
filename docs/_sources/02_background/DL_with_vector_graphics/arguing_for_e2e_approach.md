# Arguing for E-2-E approach

A book about Deep Learning with Vector Graphics also needs to make the point why an end-to-end (E-2-E) approach appears to be a desirable objective. End-to-end in this context shall refer to a generative Deep Learning model that is fed vector graphics and directly outputs vector graphics. 

End-to-end in this context shall not exclude multi-modal approaches where, _in addition_ to the vector graphic, other types of inputs are used, such as text descriptions or the corresponding raster image. Multi-modal may even be a required setting for some problems.

Here are a few bold claims (that may still require evidence):

## 1. More efficient and faster(?)

Training on SVG could be much faster since vector graphics can store information much more efficiently than raster images. This is obviously the case if an image has been designed as a vector graphic from the start. The rastered image file will be much larger and even "empty" pixels need to be described using their RGB(A) values.

## 2. More effective(?)

If obtaining SVG is our goal, then the approach of using generative models for raster images and to then convert their output to vector graphics is futile. Current generative models are not conditioned to produce output that is easily converted to vector graphics, especially when it comes to colour images (as opposed to black and white images). And such a conditioning would not be trivial.

## 3. Rasterising a vector graphics leads to information loss

Learning about the composition of an SVG (e.g. how shapes are represented, the number of layers, ...) is only possible if this information is fed into the model. A rasterised image has lost that information.

## 4. Maintaining symmetries, perfect angles etc.

Many logos and icons make use of perfect symmetries and perfect 90° angles. This information could be maintained if both input and output are vector graphics.

## 5. Current rasterisers are doing a poor job

Current tools for converting a raster image to a vector graphic ("tracing" or "vectorization") are still doing a poor job. Low resolution images can lead to undesirable corners and edges in the vector graphic. E.g. circles may not get recognised as such and then represented by separate paths. Identical elements of different sizes may get traced differently.