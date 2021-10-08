# This book

This book aims to introduce the domain of applying Deep Learning to [vector graphics](https://en.wikipedia.org/wiki/Vector_graphics), such as [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG), and to identify and summarise relevant prior work.

In this sense, this book also serves as an hommage to those amazingly talented and kind scientists and/or artists who paved the way, such as David Ha ([Twitter](https://twitter.com/hardmaru), [Website](https://otoro.net/ml/)) or Raphael Gontijo Lopes ([Twitter](https://twitter.com/iraphas13), [Website](https://raphagl.com/)).

## Raster images vs. vector graphics

Raster images consist of a grid of pixels. The application of Deep Learning to raster images has been well-researched and is relatively straightforward.

Vector graphics, on the other hand, are not based on a grid of pixels but based on mathematical descriptions of points, lines etc. in an x,y (Cartesian) coordinate system. One major advantage of vector graphics is that they are infinitely scalable. Another advantage is that their filesize is small and independent of the image's size and resolution. This is why, for instance, logos and icons are often designed as vector graphics.

## Why is Deep Learning with Vector Graphics special?

Deep Learning requires the input data to be represented as a collection of numeric values (also called a *tensor*). 

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

</svg>

```

Once rendered by the browser, this looks like this:

:::{figure-md} about_example_svg
<img src="example.svg" alt="Example SVG paper" width="200px">

Example SVG as rendered by the browser
:::


So how do we deal with vector graphics for problems like classification or for creating new vector graphics using generative models?

  * *We could just render the SVG to a raster image file, such as a PNG file.* But then our deep neural network will never learn anything about vector graphics and their specific definition. Unless trained otherwise, it will never be able to output a vector graphic.

  * *We could just treat the SVG as text.* If we do this, we can apply Recurrent Neural Networks, a class of neural networks that is able to learn sequences of inputs. Given a beginning of an SVG, we could be able to predict the next missing part. And then given that, we could again (auto-regressively) predict the next missing part -- and so on. Such a model would surely learn something about the syntax of SVGs. But will it develop an understanding of the resulting visuals?

  * *We could design an encoding logic for the SVG definition.* This means, the composition of the SVG and the parameters of all SVG elements get encoded in such a way that they can be represented as a tensor of numeric values. This requires a complex logic. And are we sure that a model trained on this data will learn something about the resulting visuals?

  * *We could feed in both the SVG and the PNG.* Then we could make sure that the model has all the information. But how does the model know how to adjust the SVG to better match the desired visual?

  * *We could include a differentiable rasterizer.* If there was such a thing (there is), then there may be a way to determine how an SVG needs to be adjusted to better match the desired visual.
