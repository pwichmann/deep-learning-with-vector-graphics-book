# Li et al. (2020) | Differentiable Vector Graphics Rasterization for Editing and Learning (diffvg)

Li et al. introduce a differentiable renderer for vector graphics, such as SVG.
This renderer bridges the raster and vector domains through backpropagation and allows for losses in the "raster space" to be propagated back into the "SVG space".


```{admonition} Available resources at a glance
* [Paper PDF via MIT](https://people.csail.mit.edu/tzumao/diffvg/diffvg.pdf)
* [Project website](https://people.csail.mit.edu/tzumao/diffvg/)
* [Supplementary project website (showing more results)](https://people.csail.mit.edu/tzumao/diffvg/supplementary_webpage/)
* [Video](https://www.youtube.com/watch?v=coV29MzZsGc)
* [Github repository and code](https://github.com/BachiLi/diffvg)
```


:::{figure-md} diffvg_cover
<img src="diffvg_cover.png" alt="diffvg paper" width="700px">

Screenshot of the [diffvg paper](https://people.csail.mit.edu/tzumao/diffvg/diffvg.pdf)
:::


## Visual results


:::{figure-md} diffvg_results_stylized
<img src="diffvg_stylized_results.png" alt="diffvg paper - stylized results" width="500px">

Screenshot of the visual results taken from the [diffvg paper](https://people.csail.mit.edu/tzumao/diffvg/diffvg.pdf)
:::

## Covered elements and attributes

The differentiable renderer is able to deal with the following elements and attributes.

### Elements
The diffvg primitives are based on the SVG (Scalable Vector Graphics) standard. diffvg supports SVG paths (with linear, quadratic or cubic
segments), ellipses, circles, and rectangles, but any curve with a parametric form is compatible with our method. The curves can be
either open or closed.


* polynomial and rational polynomial curves, including polygons, quadratic and cubic Bézier
curves, circles and ellipses

The current implementation converts arcs to cubic curves.

### Attributes
* stroke color
* fill color

Colors can be solid, linear gradient, or radial gradient, and contain RGB and alpha channels.
The current implementation assumes round caps for the strokes.