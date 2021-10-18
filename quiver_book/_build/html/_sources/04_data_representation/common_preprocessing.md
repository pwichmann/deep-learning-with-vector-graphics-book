# Common SVG preprocessing


## (1) "De-minifying" path data

Due to its use for Web pages where fast loading can be paramount, the SVG format is obsessed with minimizing file size. To save a few characters, SVG path data is often shortened which makes it hard to parse the path parameters. This is done using common SVG optimizing tools, such as [SVGO](https://github.com/svg/svgo). 

Unfortunately, this optimization makes it harder for a human to understand the already fairly abstract path data. And many Deep Learning projects aiming to ingest SVG files as training data are not sophisticated enough to deal with minified SVGs out of the box.

Usually, SVG path data is comma- and whitespace-separated. See the SVG example below -- only the value of the `d` attribute is relevant:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
  <path fill="none" stroke="red"
    d="M 10,30
       A 20,20 0,0,1 50,30
       A 20,20 0,0,1 90,30
       Q 90,60 50,90
       Q 10,60 10,30 z" />
</svg>

```

By optimizing above SVG code, one can reduce the size of this already small file from 266 bytes to 211 bytes and save about 21%. Larger SVGs can be reduced by 70% and more! The result of the optimization (done by [svgminify.com](https://www.svgminify.com/); another tools is [SVGOMG](https://jakearchibald.github.io/svgomg/)) is shown below:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
<path d="m10 30a20 20 0 0 1 40 0 20 20 0 0 1 40 0q0 30-40 60-40-30-40-60z" fill="none" stroke="red"/>
</svg>
```

Now, if you look closely at the `d` attribute which defines the path data, you will notice some irritating changes, e.g.:
* Parameters are no longer clearly separated by commas and whitespace!
* Some command letters disappeared!

What could be easily parsed before by splitting on commas and whitespaces is now no longer possible.

Now, why does this not result in any errors?
The reason is that omitting separating commas and whitespace is only permitted if the result remains unambiguous.

Let's analyze the path data from the start:

* Because a command letter `m` must be followed by its first parameter, the whitespace between `m` and `10` can be omitted.
* We cannot remove the whitespace between the two parameters `10` and `30`. This would create ambiguity. A browser would not be able to distinguish between the two equally valid solutions: $\Delta x = 10$ and $\Delta y = 30$ vs. $\Delta x = 103$ and $\Delta y = 0$. Thus, we need to keep the separating whitespace.
* There is no whitespace required between `30` and the first arc command `a`. Any letter must be a new command and preceding numbers can only be parameters from a previous command.
* Analogously, there is no need to keep a whitespace between `a` and the first parameter `20`. It is unambiguous that `a20` needs to be parsed as `a` and the first parameter `20`.
* The absolute positioning was changed into a relative positioning. So the `a` command's parameters got changed from `20,20 0,0,1 50,30` to `20 20 0 0 1 40 0` because we have already moved to point `(10, 30)`. Relative to that point, we only need to move the current point `(+40, +0)` to end up in `(50, 30)`.
* Because `a` commands generally have exactly 7 commands, the subsequent `a` command letter can be omitted, too. It is unambigous that after `a20 20 0 0 1 40 0` a new command must start.


A further common minification step is to directly concatenate continuous parameters with the binary flags of the `a` command. Since binary flags always consist of only a single digit (0 or 1), it can be derived how many digits the concatenated continuous parameter must have.

Many Deep Learning models (e.g. DeepSVG) are not able to deal with these minified SVGs out of the box and require some preprocessing before the SVG parameters can be correctly parsed. That may result in minified SVGs to be ignored in the training data. Luckily, there are sophisticated tools that can both minify and reverse the minification.


### SVGO (node.js)

The SVGO library for node.js is an excellent tool for preprocessing SVGs, such that they can be more easily parsed into numeric tensors. This way, this functionality does not have to be reimplemented in Python or any of the other languages used for Deep Learning you may be using.

To "de-minify" SVGs, the following yml settings can be used in SVGO

```yml
 - convertPathData:
      noSpaceAfterFlags: false  # this is crucial; otherwise flags can be merged with numbers and it gets hard to parse
      collapseRepeated: false  # this is crucial; otherwise successive arcs (a / A commands) get merged into one long arc command
```

## (2) Harmonizing positioning (e.g. all absolute or all relative)

E.g. in DeepSVG all commands using relative positioning (indicated by lower case letter commands) are converted to absolute commands (indicated by upper case letter commands). Just switching the case of the command letter is not enough, the parameters also need to be changed from relative (e.g. $\Delta x$ and $\Delta y$) to absolute values (e.g. $x$ and $y$).

## (3) Decomposition of basic SVG shapes to paths

There are 6 [basic SVG shapes](https://developer.mozilla.org/en-US/docs/Web/SVG/Element#basic_shapes):

1. `<circle>`
1. `<ellipse>`
1. `<line>`
1. `<polygon>`
1. `<polyline>`
1. `<rect>`

These can be decomposed and converted into paths without any visually perceivable loss.


```{admonition} Open question
:class: important
Is it possible to keep these basic SVG shapes as part of the feature vector? Would this help with ensuring that circles remain circles and are not just approximated by squiggly lines in the SVG output?
```

| Unsupported basic SVG shape | Explanation | Replacement |
|---|---|---|
| [`<circle>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/circle) | A circle defined by the center point (x,y) and a radius. | Can be transformed to a path using two or four Elliptical Arc commands, which themselves can be converted to Cubic Bézier curves. |
| [`<ellipse>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/ellipse) | An ellipse defined by 4 parameters, the ellipses position (x,y) and the radius on the x axis and the radius on the y axis | Can be transformed to a path using four Elliptical Arc commands, which themselves can be converted to Cubic Bézier curves. |
| [`<line>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/line) | A straight line defined by the start point (x1, y1) and the end point (x2, y2) | Can be converted into a path using the LineTo command |
| [`<polygon>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/polygon) | A _closed_ shape consisting of a set of connected straight line segments. A polygon is defined by a sequence of points; the last point is connected to the first point. | Can be converted into a path using the LineTo command. |
| [`<polyline>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/polyline) | A shape consisting of a sequence of connected traight line segments. As opposed to the `<polygon>`, a `<polyline>` does not have to be closed and the last point is not automatically connected to the first one. | Can be converted into a path using the LineTo command. |
| [`<rect>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/rect)| A rectangle defined by position (x, y), their width and height (and optionally the radius for rounded corners) | Can be converted into a path using the LineTo command. **Please note that DeepSVG may overlook the option of rounded corners at this point.** |


### (3.1) Decomposition of circles

The decomposition of circles into four or just two elliptical arc curves is possible without any loss in visual quality.

#### 4 quarter-circles represented by elliptical arcs

As stated in the [SVG standards for a circle](https://www.w3.org/TR/SVG/shapes.html#CircleElement), ...

> mathematically, a ‘circle’ element is mapped to an equivalent ‘path’ element that consists of four elliptical arc segments, each covering a quarter of the circle. The path begins at the "3 o'clock" point on the radius and proceeds in a clock-wise direction (before any transformations). The rx and ry parameters to the arc commands are both equal to the used value of the r property, after conversion to local user units, while the x-axis-rotation, the large-arc-flag, and the sweep-flag are all set to zero. The coordinates are computed as follows, where cx, cy, and r are the used values of the equivalent properties, converted to user units:
> 1. A move-to command to the point cx+r,cy;
> 2. arc to cx,cy+r;
> 3. arc to cx-r,cy;
> 4. arc to cx,cy-r;
> 5. arc with a [segment-completing close path operation](https://www.w3.org/TR/SVG/paths.html#TermSegment-CompletingClosePath) [not supported yet?].


DeepSVG implements a decomposition using 4 elliptical arcs. The example from the paper is a circle defined as follows:

```SVG
<circle 
  cx="1" 
  cy="1"
  r="1" 
/>
```

So, this is a circle around the center point $(1, 1)$ with a radius of 1 (and, thus, a width of 2).
This circle gets translated a MoveTo command and four elliptical arc curves followed by a closing command.

```SVG
<path 
  d="
    M1,0 
    A1,1 0 0 1 2,1
    A1,1 0 0 1 1,2
    A1,1 0 0 1 0,1
    A1,1 0 0 1 1,0 
    z
  " 
/>
``` 

The first arc starts at the top point of the circle (remember that $y=0$ is the top in the SVG coordinate system) and draws a quarter circle in a clockwise direction to the absolute point $(2, 1)$.


:::{figure-md} deepsvg_circle_decomposition_4_arcs
<img src="deepsvg_circle_decomposition_4_arcs.png" alt="deepsvg_circle_decomposition_4_arcs" width="200px">

Circle decomposition in DeepSVG using four elliptical arc curves; center figure from Appendix of the [DeepSVG paper](https://arxiv.org/abs/2007.11301)
:::


#### 2 half-circles represented by elliptical arcs

The SVG decomposition of circles to paths (and vice versa) has also been covered by this [article by the smashingmagazine.com](https://www.smashingmagazine.com/2019/03/svg-circle-decomposition-paths/).


```SVG
<svg
   xmlns="http://www.w3.org/2000/svg" 
   width="100"
   height="100"
   version="1.1">
    
    <circle cx="50" cy="50" r="25"/>
    
</svg>

```

The option for circle decomposition proposed in the article is to use a MoveTo command followed by two half-circle elliptical arc segments, each describing a half-circle. 

```SVG
<path
  d="
    M 25, 50
    a 25,25 0 1,1 50,0
    a 25,25 0 1,1 -50,0
  "
/>
```

Let's assume, we want to replace had a center point $C$ at (50, 50) and a radius $r$ of 25.
Since we no longer define a circle but a a path, we need to define a new starting point for the overall shape on the circles outline. One possible way is to start at the leftmost point of the circle. To get there, we can use the same y-coordinate as the center point. The x-coordinate is the center point's x-coordinate minus the radius.

So, as a first command, we move the pen to the absolute position $(x, y) = (C_x - r, C_y) = (50 - 25, 50) = (25, 50)$. So the command is: `M 25, 50`.

Then we can start to draw the first elliptical arc curve using a relative positioning: 
The parameters of the first arc `25,25 0 1,1 50,0` mean the following:

* `25`: The relative X radius of the arc;
* `25`: The relative Y radius of the arc;
* `0`: angle in degrees (rotation of the ellipse relative to the x axis) 
* `1`: large-arc-flag, a binary variable indicating which arc shall be drawn: the large (1) or the small one (0)
* `1`: sweep-flag, a binary variable indicating which arc shall be drawn: a clockwise turning arc (1) or the counter-clockwise turning one (0)
* `50`: The ending X coordinate (relative) of the arc
* `0`: The ending Y coordinate (relative) of the arc

The radius of the arc along either dimension needs to be the same as the radius of the circle we replace. These are the first two parameters.

The last two parameters define the end point of our arc. Our current position (25, 50), the leftmost point of the circle. We want draw a half-circle, which means we want to end at the rightmost point on the other side of the cirle. That point is on the same vertical position but the circle's width (2 x the radius), i.e. 50, further to the right, i.e. $(25 + 50, 50 + 0) = (75, 50)$.

The ellipse is not rotated relative to the x axis. So, the third parameter is set to 0 (degrees).

The information so far is not enough to know which of the possible arcs need to be drawn. The two remaining binary flags provide that specification. The first flag has no practical effect. The second flag, however, defines that the arc shall be drawn in clockwise direction from our current point. Which means that the first arc describes the upper half-circle.

A closing z command may also be required but was not used in the article -- likely since it does not impact the visual representation.


:::{figure-md} deepsvg_circle_decomposition_upper_arc
<img src="deepsvg_circle_decomposition_upper_arc.png" alt="deepsvg_circle_decomposition_upper_arc" width="400px">

Only the upper half-circle defined in SVG using an elliptical arc command; code shown on top; resulting half-circle at the bottom; visualized using [codepen.io](https://codepen.io/)
:::


Adding a second elliptical arc to describe the lower half-circle completes the circle. Note that only the end point needs to be adjusted. We want to end up 50 pixels to the left again and at the same vertical position.

:::{figure-md} deepsvg_circle_decomposition_full_circle
<img src="deepsvg_circle_decomposition_full_circle.png" alt="deepsvg_circle_decomposition_full_circle" width="400px">

Full circle described by two half-circles formed by elliptical arc curves; visualized using [codepen.io](https://codepen.io/)
:::


### (3.2) Decomposition of ellipses


### (3.3) Decomposition of lines

Lines, defined by a start point and an end point, can be trivially decomposed to a MoveTo command followed by a single LineTo command.

The following line defines a straight line from $(x_1, y_1)=(0, 0)$ to $(x_2, y_2)=(1, 1)$.

```SVG
<line 
  x1="0"
  x2="1"
  y1="0"
  y2="1"
/>
```

To decompose the basic shape, we can move to the starting position and then use the end point as parameter for the LineTo command:

```SVG
<path 
  d=
    "
      M 0,0 
      L 1,1
    " 
/>
```

### (3.4) Decomposition of polygons

Polygons are just closed polylines and can also trivially be decomposed using a MoveTo command followed by a sequence of LineTo commands.

The following polygon is defined by three points $(0, 0)$ (top left), $(1, 0)$ (top right), and $(1, 1)$ (bottom right). The shape gets closed by definition.

```SVG
<polygon 
  points=
    "
      0, 0 
      1, 0 
      1, 1
    "
/>
```

The polygon can get replaced by the following path:

```SVG
<path 
  d=
    "
      M 0,0 
      L 1,0 
      L 1,1 
      z
    " 
/>
```

We first move to the starting position $(0, 0)$, then we create lines to the second point and the third point and close the path using the `z` command.

### (3.5) Decomposition of polyline

Polylines are just sequences of straight lines and are not automatically closed.
Thus, they can be replaced the same way as polygons -- just without the closing command `z`.


```SVG
<polyline 
  points=
    "
      0, 0 
      1, 0 
      1, 1
    "
/>
```

This is the replacement path data:

```SVG
<path 
  d=
    "
      M 0,0 
      L 1,0 
      L 1,1
    " 
/>
```

### (3.6) Decomposition of rectangles

Rectangles can also trivially be converted into a sequence of straight lines by using a MoveTo command, three or four LineTo commands and a closing command `z`.

```SVG
<rect 
  x="0"
  y="0"
  width="1"
  height="1"
/>
```

This is the replacement path data:

```SVG
<path 
  d=
    "
      M 0,0 
      L 1,0
      L 1,1
      L 0,1
      L 0,0
      z
    "
/>
```

Is is also possible to just use three LineTo commands since the closing command `z` would form a straight line anyways and needs to connect the last and the first point:

```SVG
<path 
  d=
    "
      M 0,0 
      L 1,0
      L 1,1
      L 0,1
      z
    "
/>
```

## (4) Reducing the number of different path data commands

There are 10 different [path data commands](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#path_commands) for relative positioning and 10 for absolute positioning. Not all of these 10 different path data commands are actually required -- some can be represented using the other more basic commands.

### Why do we want to reduce the number of different path data commands?

If commands can be replaced by others, then replacing them reduces the complexity and should help the neural network with learning.


### Replacement of path data commands

In the following, we assume that we have harmonized all commands and only use the absolute positioning.

It appears that the minimal set of path data commands is the following subset:

* [MoveTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#moveto_path_commands) `M`
* [LineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands) `L`
* [Cubic Bézier Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#cubic_b%C3%A9zier_curve) `C`
* [ClosePath](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#closepath) `Z`

The following commands can be replaced or at least approximated by the four commands listed above:


| Unsupported commands | Explanation | Replacement |
|---|---|---|
| **[VerticalLineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands) `V`** | This is just a simple way of defining a vertical line. LineTo is the general commands for all types of straight lines and can also represent vertical lines. | LineTo L |
| **[HorizontalLineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands) `H`** | This is just a simple way of defining a horizontal line. LineTo is the general commands for all types of straight lines and can also represent horizontal lines. | LineTo L |
| **[SmoothCubicBezier](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#cubic_b%C3%A9zier_curve) `S`** | This is just a simple way of defining a smooth cubic bézier curve. The cubic bézier C is the general commands for all types of cubic bézier curves and can also represent smooth cubic béziers. | Cubic Bézier Curve C |
| **[Quadratic Bézier Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#quadratic_b%C3%A9zier_curve) `Q`** | Higher-order cubic bézier curves can also represent quadratic bézier curves | Cubic Bézier Curve C |
| **[Smooth Quadratic Bezier](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#quadratic_b%C3%A9zier_curve) `T`** | This is just a simple way of defining a smooth quadratic bézier curve. The cubic bézier C can also represent all quadratic, including smooth quadratic béziers. | Cubic  Bézier Curve C |
| **[Elliptical Arc Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#elliptical_arc_curve) `A`** | Elliptical arc curves are curves defined as a portion of an ellipse. | Converted to Cubic Bézier curves which can approximate the elliptical arc. |


#### Replacement of VerticalLineTo V (perfect substitute)

#### Replacement of HorizontalLineTo H (perfect substitute)

#### Replacement of SmoothCubicBezier S 

#### Replacement of Quadratic Bézier Curve Q

#### Replacement of Smooth Quadratic Bezier T

#### Replacement of Elliptical Arc Curve A (approximation only!)

Converting an elliptical arc curve (`A` / `a`) is non-trivial. Luckily, a smart person of the name Luc Maisonobe, who -- if correctly identified -- appears to be a flight dynamics expert at CS Group and a free software developer (The Apache Software Foundation), published a technical paper titled "Drawing an elliptical arc using polylines, quadratic or cubic Bézier curves" on spaceroots.org in 2003 (domain unfortunately no longer active). Copies of this paper can still be found on the Web.



[Stackoverflow comment](https://stackoverflow.com/a/5589051): The error is most dependent on the difference of the start and end angles. I've had good success by limiting the angle difference to 60°. That is, I make a separate cubic segment for every 60° (or fraction thereof) and chain them together.



The conversion of elliptical arc curves (a / A commands) can also be performed as part of the preprocessing with SVGO.
The corresponding configuration in the `svgo_config.yml` is:

```yml
  - convertShapeToPath:
      convertArcs: true
```


## (5) Path simplification

To simplify the neural network's task of representation learning, it can make sense to simplify paths. 

Generally, the aim is to reduce the number of points that form a shape. But, in some implementations and for some input examples, the number of points can increase if the resolution of a curve would otherwise be too low.

###### Simplification approach

If the SVG input only consisted of smooth curves, the simplification step could simply be to reparametrize points on that curve so that they are placed equidistantly from one another. However, since SVG shapes contain sharp angles and, thus, corners, these points should not be changed.
Consequently, the simplification is done in two steps:

1. Split paths at points that form a sharp angle.
    * This is measured by the angle between the incoming and the outcoming tangent
    * If the angle is smaller than some threshold $\eta = 150°$, then the angle is considered sharp
2. Apply a simplification algorithm to the resulting shapes
    * For line segments: Ramer-Douglas-Peucker algorithm (from "Algorithms for the Reduction of the Number of Points Required to Represent a Digitized Line or its Caricature" (1973))
    * For segments of cubic Bézier curves: Philip J. Schneider algorithm (from "An Algorithm for Automatically Fitting Digitized Curves" (1990))
3. The resulting lines and Bézier curves are divided in multiple subsegments if their length is larger than some distance $\Delta = 5$. 


## (6) SVG Normalization


## (7) Embedding

