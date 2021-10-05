# Deep Learning with Vector Graphics

**By Pascal Wichmann**


## About

This book aims to introduce the domain of applying Deep Learning to [vector graphics](https://en.wikipedia.org/wiki/Vector_graphics), such as [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG), and to identify and summarise relevant prior work.

In this sense, this book also serves as an hommage to those amazing scientists and/or artists who paved the way, such as David Ha ([Twitter](https://twitter.com/hardmaru), [Website](https://otoro.net/ml/)).

## Why is Deep Learning with Vector Graphics special?

Raster images consist of a grid of pixels. The application of Deep Learning to raster images has been well-researched and is relatively straightforward.

Vector graphics, on the other hand, are not based on a grid of pixels but based on mathematical descriptions of points, lines etc. in an x,y (Cartesian) coordinate system. One major advantage of vector graphics is that they are infinitely scalable. Another advantage is that their filesize is small and independent of the image's size and resolution. This is why, for instance, logos and icons are often designed as vector graphics.

Deep Learning requires the input data to be represented as a collection of numeric values (also called a **tensor**). 

Representing a raster image as such a tensor is easy: In a simple black-and-white image, a single pixel already holds a single numeric value, e.g. from 0 (black) to 255 (white), with all the shades of grey in between. So, the units of an artificial neural network can (more or less) directly be fed with these values.

Vector graphics are different. One standard format for vector graphics is the Scalable Vector Graphics format - or short: SVG. This format is XML-based and, thus, a text-based description of the image using tags and attributes. So how do we deal with vector graphics?

  * *We could just render the SVG to a raster image file, such as a PNG file.* But then our deep neural network will never learn anything about vector graphics and their specific definition. Unless trained otherwise, it will never be able to output a vector grapic.
  * *We could just treat the SVG as text.* If we do this, we can apply Recurrent Neural Networks, a class of neural networks that is able to learn sequences of inputs. Given a beginning of an SVG, we could be able to predict the next missing part. And then given that, we could again (auto-regressively) predict the next missing part -- and so on. Such a model would surely learn something about the syntax of SVGs. But will it develop an understanding of the resulting visuals?
  * *We could design an encoding logic for the SVG definition.* This means, the composition of the SVG and the parameters of all SVG elements get encoded in such a way that they can be represented as a tensor. This requires a complex logic. And are we sure that a model trained on this data will learn something about the resulting visuals?
  * *We could feed in both the SVG and the PNG.* Then we could make sure that the model has all the information. But how does the model know how to adjust the SVG to better match the desired visual?
  * *We could include a differentiable rasterizer.* If there was such a thing (there is), then there may be a way to determine how an SVG needs to be adjusted to better match the desired visual.


:::{note}

Please note that this book is a living document. It serves as a knowledge repository but also aims to document the author's own questions and embarrassing knowledge gaps.

The author can be contacted via the [issues function of Github for this project](https://github.com/pwichmann/deep-learning-with-vector-graphics-book/issues). Please make yourself heard via the issues function:

  * if relevant prior work or newly published work has been overlooked
  * if you spot any errors in content
  * if you spot any typos or grammatical errors
  * if you know the answer to posed questions
  * if have suggestions for solving problems stated as being open

I am happy to consider suggested improvements and include your name in the 'thank you' notes of this book.

Feel free to use excerpts from this book as long as you cite the source, e.g. as:

**Wichmann, Pascal. (2021). Deep Learning with Vector Graphics. https://pwichmann.github.io/deep-learning-with-vector-graphics-book**

:::


## ChangeLog

Please refer to the [repository itself](https://github.com/pwichmann/deep-learning-with-vector-graphics-book/commits/main) for an detailed overview of changes.

  * 2021-10-05: First commit and publication of the early draft of the book