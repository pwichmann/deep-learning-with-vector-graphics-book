# Challenges of Vector Graphics

This section aims to summarise why vector graphics are challenging to work with in the context of Deep Learning. The focus will be on generative models and SVG, as the common data format for vector graphics.


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

What does this graph structure mean for the data representation and the suitable of specific models?

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


### 5. Current vectorizers (to convert PNG to SVG) perform extremely poorly

Currently available vectorizing ("tracing") tools perform extremely poorly. Purely the fact that an image has a low resolution can lead to situations where the pixels are traced like steps. Furthermore, there is no contextual awareness: identical objects of different sizes may get represented differently in the resulting SVG.



## Further challenges

* SVGs have an x,y coordinate system of a certain size; in addition only a certain part of it may be defined to be shown to the user (viewport)


