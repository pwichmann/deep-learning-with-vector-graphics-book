# Strategies for dealing with SVG

So how do we deal with vector graphics when it comes to deep generative models?
The following is a comprehensive overview of possible approaches.

## Avoid SVG altogether and only work with raster images

We could just render the SVG to a raster image file, such as a PNG file. Once input files have been converted, we would only work with raster images.

But then our deep neural network will never learn anything about vector graphics and their specific definition. Unless trained otherwise, it will never be able to output a vector graphic. Existing tools that can convert raster images into vector graphics (so-called tracers or vectorizers) do not perform well, e.g. in the case of color gradients.

## Simply treat the SVG as text

We could just treat the SVG just like any text sequence in Deep Learning. Text sequences get split into tokens, such as individual characters or individual words. And then each token gets converted into a vector. A few years ago, this would have been one-hot vectors. Nowadays, these are generally embeddings.

If we do this, we can apply Recurrent Neural Networks (RNNs) or transformers, types of neural networks that are able to learn sequences of inputs. Given a beginning of an SVG, we could be able to predict the next missing part. And then given that, we could again (auto-regressively) predict the next missing part -- and so on. Such a model would surely learn something about the syntax of SVGs. But will it develop an understanding of the resulting visuals? Is a sequence of text characters really the appropriate data representation?

## Encode the SVG definition into a vector

We could our domain knowledge of the SVG standard to encode the definition of each SVG of the training dataset into a numerical vector.

This is not trivial. An SVG is an XML document of (often deeply) nested tags. And each tag can have a multitude of attributes that impact the way the SVG looks when rendered on the screen. 

Because only a few attributes are used in any given tag, the resulting vector will be sparse, that is it will contain many zero values.

Converting an SVG into a vector requires a complex, hard-coded logic. Changes to the SVG standard would require the logic to be updated.

Another concern is that a model trained on this data may still not "understand" the impact of changes in the SVG definition on the resulting rendered image.

## Represent the SVG as a graph

A further idea is to represent the SVG consisting of nested tags and attributes as a graph. Graph Neural Networks (GNN) are specialised to deal with graph structures and deep generative models for graph generation have been developed ([survey paper on the topic](https://arxiv.org/pdf/2007.06686.pdf)).

## Use a multi-modal model of raster image and SVG image

We could feed in both the SVG and the corresponding PNG as inputs to the model. By using this multi-modality (i.e. two different sensory inputs of the same "object"), we may be able to improve the model's "understanding" how SVG definition and PNG render relate to each other.

But how would the model know how to adjust the SVG to better match the desired visual?

## Include the rasterizer in the model

A rasterizer renders the SVG definition into a pixel-based graphic that can be displayed on a screen. Only recently a rasterizer was developed that is differentiable. That means any loss in the raster space could be propagated back to required changes in the SVG definition.

## Include a text description in the model

Multi-modal models using images and a text description, like DALL-E or imagen, allow users to generate an image by simply providing a text prompt. If we could obtain text descriptions for the SVG images, this could allow users of the trained model to generate SVG images simply by providing a descriptive text prompt.