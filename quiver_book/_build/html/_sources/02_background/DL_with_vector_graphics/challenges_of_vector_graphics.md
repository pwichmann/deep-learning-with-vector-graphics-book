# Challenges of Vector Graphics

This section aims to summarise why vector graphics are challenging to work with in the context of Deep Learning. The focus will be on generative models and SVG, as the common data format for vector graphics.

To the author, the following aspects appear to be the key challenges:

1. Data representation
2. Rasterizing is not a trivial function
3. Ambiguous SVG source of a PNG
4. Linear interpolation in case of step changes
5. Current vectorizers (to convert PNG to SVG) perform poorly

Each key challenge shall be discussed below.


## Key challenges

### 1. Data representation

#### Requirement of numeric tensors (largely solved)

Deep Learning models expect a numeric tensor. However, the common vector graphics format SVG is XML-based and, thus, a text file in the first instance. This is very different from pixel-based raster images which already come as an array of RGB(A) values and, thus, in a tensor format.

So, how do we represent a vector graphic as a numeric tensor?

#### SVG is a complex format

SVGs can have groups of elements and multiple layers. The SVG format not only defines simple paths but also allows for quadratic and cubic bézier curves as well as other pre-defined SVG primitives (such as rectangles, circles etc.).
Shapes can be filled or just an outline. 

The list of [SVG elements (the tags)](https://developer.mozilla.org/en-US/docs/Web/SVG/Element) and the list of [SVG attributes](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute) that a browser needs to deal with is long.

If we cannot represent all of these (likely!), then we need to simplify and focus on the most common ones. SVGs can contain animations and all sorts of other Web technology (scripts, ...) that need to be ignored.

#### Graph structure inherent to SVGs

Since SVG files are XML-based, they also allow for deeply nested tags. This forms a hierarchical structure, a graph of nodes and attributes. In raster images, there is no hierarchy of pixels.

What does this graph structure mean for the data representation and the suitability of specific models?

#### A loss function makes more sense in raster space than in SVG space

Defining a loss function in SVG space does not seem to be the right approach. Instead, we want to achieve a specific visual result.


### 2. Rasterizing is not a trivial function

Translating an SVG to its visual representation is not trivial and not as straightforward as it is for a raster image with (R,G,B) pixel values. SVGs have layers. Elements can be partially occluded. Mathematical descriptions of shapes have to be converted into the RGB pixel values.

It cannot be taken for granted that a Deep Neural Network alone can internalise the "knowledge" of how to convert an SVG into a raster image and how changing the SVG would impact the resulting raster image.
It may be required to use both vector graphics and corresponding raster images as input (dual-modality) and/or to also rely on a differentiable rasterizer.


### 3. Ambiguous SVG source of a PNG

Converting an SVG to a PNG ("rasterizing" or "tracing") is not a bijective function. There are *many* ways to design an SVG that results in the same rastered PNG. Given a PNG, one can only derive possible SVG compositions. But one can not know how the original SVG was designed.

That makes learning just from raster images difficult if one wanted to output SVG in the end.


### 4. Linear interpolation in case of step changes


How could linear interpolation in latent space work when the images look similar but the SVG composition differs? 


By design, a raster image allows for linear interpolation between images. That is, one can always find a way to gradually alter pixel values to gradually morph one image into another.

This may or may not be different for vector graphics. Let's take two visual representations of the same concept, an eye. Both images below look similar. In raster space, one could easily imagine how to morph one eye into the other.

But in the vector image space, one image consists of __two separate paths__, and the other consists of __just one__.


:::{figure-md} eye_1
<img src="eye_1.png" alt="eye" width="400px">

Icon of an eye opened in Inkscape; the eye consists of two separate paths
:::


:::{figure-md} eye_2
<img src="eye_2.png" alt="eye" width="400px">

Icon of an eye opened in Inkscape; the eye consists of just one path
:::

Now, how would linear interpolations between these SVG representations work?
How would a linear interpolation look if one the icons even had the white part of the eye explicity modelled as a shape?
Is this still possible in an end-to-end model or is that the point where we need to design two parts: a generative model in raster space and one deep learning-based vectorizer?

We know from the DeepSVG paper by Carlier (2020) that it is technically possible to generate linear interpolations between two vector graphics with a different number of shapes. But are these interpolations as useful as interpolations in raster space?

:::{figure-md} deepsvg_linear_interpolation
<img src="deepsvg_linear_interpolation.png" alt="DeepSVG linear interpolation" width="700px">

Linear interpolation between two vector graphics as made possible by DeepSVG; image taken from the [DeepSVG paper](https://arxiv.org/abs/2007.11301)
:::



### 5. Current vectorizers (to convert PNG to SVG) perform poorly

Currently available vectorizing ("tracing") tools perform extremely poorly. Purely the fact that an image has a low resolution can lead to situations where the pixels are traced like steps. Furthermore, there is no contextual awareness: identical objects of different sizes may get represented differently in the resulting SVG.

#### Examples

**Example 1:**

The image below shows the "Octocat", Github's mascot.

:::{figure-md} GitHub-Mark
<img src="GitHub-Mark.png" alt="github" width="300px">

Github's mascot Octocat
:::

Note the ellipses on the octocat's tentacle-tail in the figure above.
Vectorizing a low resolution version of that section of the image leads to a poor result, see the figure below:


:::{figure-md} vectorizing_still_poor
<img src="vectorizing_still_poor.png" alt="vectorizing_still_poor" width="900px">

Current vectorizers are still performing poorly. In this case, the used tool was [vectormagic](https://vectormagic.com/) -- still one of the better tools out there and not free.
:::

The low resolution results in the smaller ellipses to have corners in the vector output. It appears that the vectorizer has no understanding that the resolution of the input is low and pixel edges and corners should not be traced too closely. The vectorizer also has no contextual understanding that there is a symmetry in these shapes: all of these are ellipses of different sizes.

**Example 2:**

In another example, see figure below, an image loses its perfect symmetry through vectorization.

:::{figure-md} vectorizing_destroy_symmetry
<img src="vectorizing_destroy_symmetry.png" alt="vectorizing_destroy_symmetry" width="400px">

Example from [imagetracerjs](https://github.com/jankovicsandras/imagetracerjs)
:::


**Example 3:**

Vector graphis, such as SVG, can make use of linear and radial gradients. This can produce visually pleasing results, such as this logo below:

:::{figure-md} edge
<img src="edge.svg" alt="edge" width="300px">

Microsoft Edge logo designed as a vector graphic in SVG format; [source](https://www.npmjs.com/package/@browser-logos/edge)
:::

Since this logo was designed as an SVG, we know it is possible to design such a logo as a vector graphic.
Here is the SVG code for the logo above:


:::{figure-md} edge_logo_svg
<img src="edge_logo_svg.png" alt="edge_logo_svg" width="900px">

SVG code of the Microsoft Edge logo
:::


However, current vectorizers have a hard time converting a rasterized version of the logo back to a vector graphic.
These algorithms have no understanding of what (potentially overlapping) shapes are required and what shapes need to have what type of linear or radial gradients. There are too many parameters at play for pre-Deep Learning algorithms to work well.


:::{figure-md} vectorized_edge_logo
<img src="vectorized_edge_logo.png" alt="vectorized_edge_logo" width="300px">

Screenshot of the vectorized Microsoft Edge logo; vectorized using [vectorizer.io](https://www.vectorizer.io). The vector result is much more complicated than the original SVG and no where near the original in terms of quality.
:::


#### Summary of limitations of current vectorizers

* No context-aware tracing
  * E.g. not prior classification of objects and if they should be round or edgy
  * E.g. the bottom of a heart shape becomes round (--> incorrect points / edges)
* No resolution-aware tracing
  * At low input resolution, tracers start to follow pixel steps
  * E.g. this shape was likely a circle even though it is now a 20x20 pixel blob
* Potentially incorrect trade-off between accuracy and number of points
  * Vectorizers rarely produce elegant results with minimal number of points
* Over-use of points vs. a smart use of bezier curves and their handles
* No or poor detection of colour gradients
* No consideration of symmetry
* Tracers do not understand how humans compose images from multiple shapes (potentially overlapping, potentially half-transparent), e.g. shadows
* SVGs just have too many parameters and an intractable complexity for current vectorizers to correctly guess them all given a raster image

Because current vectorizers are performing poorly (and will hopefully soon be replaced by smarter Deep Learning-based algorithms), there is a need to output vector graphics directly. Instead of attempting to output raster images and only then vectorize them.


## Further challenges

* SVGs have an x,y coordinate system of a certain size; in addition only a certain part of it may be defined to be shown to the user (viewport)


