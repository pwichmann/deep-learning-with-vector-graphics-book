# This book

This book aims to introduce the domain of applying Deep Learning to [vector graphics](https://en.wikipedia.org/wiki/Vector_graphics), such as [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG), and to identify and summarise relevant prior work.

The focus is on content related to the aim of end-to-end generative deep learning models for vector graphics. But other content, such as object localization and classification within vector graphics, may also be covered.

This book is an hommage to those amazingly talented and kind scientists, engineers and artists who paved the way and inspired the author, such as **David Ha** ([Twitter](https://twitter.com/hardmaru), [Website](https://otoro.net/ml/)) or **Raphael Gontijo Lopes** ([Twitter](https://twitter.com/iraphas13), [Website](https://raphagl.com/)).

## Raster images vs. vector graphics

Raster images consist of a grid of pixels. The application of Deep Learning to raster images has been well-researched and is relatively straightforward.

Vector graphics, on the other hand, are not based on a grid of pixels but based on mathematical descriptions of points, lines etc. in an x,y (Cartesian) coordinate system. A common vector graphics format is the Scalable Vector Graphics format (SVG). One major advantage of vector graphics is that they are infinitely scalable. Another advantage is that their file size is small and independent of the image's size and resolution. This is why, for instance, fonts, icons and logos are often designed as vector graphics.

## Why is Deep Learning with Vector Graphics special?

Deep Learning requires the input data to be represented as a collection of numeric values (also called a *tensor*). This book was also half-jokingly dubbed "*Scalable Tensor Graphics*" at some point for that reason -- which sounds like an extension from vectors to multidimensional tensors.

Representing a raster image as such a tensor is easy: In a simple black-and-white image, a single pixel already holds a single numeric value, e.g. from 0 (black) to 255 (white), with all the shades of grey in between. So, the units of an artificial neural network can (more or less) directly be fed with these values.

Vector graphics are different. One standard format for vector graphics is the Scalable Vector Graphics format - or short: SVG. This format is XML-based and, thus, a text-based description of the image using a hierarchical structure of nested tags and their associated attributes. 

Below is an example of simple SVG:

```XML
<svg version="1.1"
     width="200" height="200"
     xmlns="http://www.w3.org/2000/svg">

  <rect width="100%" height="100%" fill="blue" />

  <circle cx="100" cy="100" r="80" fill="yellow" />

  <text x="100" y="125" font-size="60" text-anchor="middle" fill="black">SVG</text>

  <path d="M 30 160 q 50 -50 140 0" stroke="red" stroke-width="8" fill="none" />

</svg>
```

Once rendered by the browser, this looks like this:

:::{figure-md} about_example_svg
<img src="example.svg" alt="Example SVG paper" width="200px">

Example SVG as rendered by the browser
:::

The properties of vector graphics pose a number of challenges when it comes to Deep Learning (see the separate chapter on the topic).


## So, how do we deal with vector graphics?

So how do we deal with vector graphics for problems like classification or for creating new vector graphics using generative models?

### We could avoid SVG altogether and only work with raster images

We could just render the SVG to a raster image file, such as a PNG file. Once input files have been converted, we would only work with raster images.

But then our deep neural network will never learn anything about vector graphics and their specific definition. Unless trained otherwise, it will never be able to output a vector graphic. Existing tools that can convert raster images into vector graphics (so-called tracers or vectorizers) do not perform well, e.g. in the case of color gradients.

### We could just treat the SVG as text

We could just treat the SVG just like any text sequence. If we do this, we can apply Recurrent Neural Networks (RNNs), a class of neural networks that is able to learn sequences of inputs. Given a beginning of an SVG, we could be able to predict the next missing part. And then given that, we could again (auto-regressively) predict the next missing part -- and so on. Such a model would surely learn something about the syntax of SVGs. But will it develop an understanding of the resulting visuals? Is a sequence of text characters really the appropriate data representation?

### We could encode an SVG into a numerical vector

We could convert each SVG of the training dataset into a numerical vector. 

This is not trivial. An SVG is an XML document of (often deeply) nested tags. And each tag can have a multitude of attributes that impact the way the SVG looks when rendered on the screen. 

Because only a few attributes are used in any given tag, the resulting vector will be sparse, that is it will contain many zero values.

Converting an SVG into a vector requires a complex, hard-coded logic. Changes to the SVG standard would require the logic to be updated.

Another concern is that a model trained on this data may still not "understand" the impact of changes in the SVG definition on the resulting rendered image.

### We could use a multi-modal model

We could feed in both the SVG and the corresponding PNG as inputs to the model. By using this multi-modality (i.e. two different sensory inputs of the same "object"), we may be able to improve the model's "understanding" how SVG definition and PNG render relate to each other.

But how would the model know how to adjust the SVG to better match the desired visual?

### We could include the rasterizer in the model

A rasterizer renders the SVG definition into a pixel-based graphic that can be displayed on a screen. Only recently a rasterizer was developed that is differentiable. That means any loss in the raster space could be propagated back to required changes in the SVG definition.

### We could also include a text description in the model

Multi-modal models using images and a text description, like DALL-E or imagen, allow users to generate an image by simply providing a text prompt. If we could obtain text descriptions for the SVG images, this could allow users of the trained model to generate SVG images simply by providing a descriptive text prompt.

