# Normalization

These steps should be performed before any simplification steps. Simplification could be sensitive to the scale of the SVG input and the scale needs to be normalized first.

## Position of paths within the viewbox

The location of the paths can be harmonized, such that the paths are moved as closely towards the SVG origin of $(0, 0)$ as possible.
(Check again: DeepSVG does not do this? I think Ha did this...)
Or would it be better to center them?

## Same scale (viewbox)

Input SVGs could have very different scales, i.e. sizes of their viewbox attribute.
To normalize the size across all input samples, a standard viewbox (e.g. 256 x 256) is defined and all path data parameters are scaled accordingly.

For a size of $(256, 256)$, we want all SVGs to look as follows:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<svg viewBox="0 0 256 256" xmlns="http://www.w3.org/2000/svg">

  <!-- the actual SVG goes here -->

</svg>
```

Maybe, this has to be done BEFORE the simplification. Otherwise, the simplification will be different since resolution of SVGs was different when simplification was applied.

## Harmonizing starting position and command orientation

Any shapes **starting position** (essentially, the parameters of the first `M` command of any shape) is chosen to be the topmost leftmost point. From this point on, the commands are oriented in a **clockwise direction** around the shape.

**Why does this matter?**


## Applying transforms and then removing them

Transforms are functions that can be applied to a shape and alter it. Examples of these transforms are:
* `translate()` which changes the shapes position along x- and/or y-axis
* `rotate()` which rotates the shape by a specified number of degrees
* `scale()` which changes the size of the shape by a factor or mirrors the object along an axis (the latter is the case for parameters $(-1, 1)$ or $(1, -1)$)
* `skewX()` or `skewY()` which skews the shape along an axis
* `matrix()` which takes 6 parameters and can represent any of the transforms above

If we only parse the shape's path data, we will miss that the shape may actually look very different since a transform need to be applied. To make sure that information is not overlooked, the transforms can be directly applied to the path data and then removed.
This may have to be done after the shape has already been converted to a path.

E.g.:
* https://stackoverflow.com/questions/5149301/baking-transforms-into-svg-path-element-commands
* https://stackoverflow.com/questions/13329125/removing-transforms-in-svg-files
