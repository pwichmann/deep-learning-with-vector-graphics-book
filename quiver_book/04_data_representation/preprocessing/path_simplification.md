# Path simplification

To simplify the neural network's task of representation learning, it can make sense to simplify paths. 

Generally, the aim is to reduce the number of points that form a shape. But, in some implementations and for some input examples, the number of points can increase if the resolution of a curve would otherwise be too low.

## Simplification approach

If the SVG input only consisted of smooth curves, the simplification step could simply be to reparametrize points on that curve so that they are placed equidistantly from one another. However, since SVG shapes contain sharp angles and, thus, corners, these points should not be changed.
Consequently, the simplification is done in two steps:

1. Split paths at points that form a sharp angle.
    * This is measured by the angle between the incoming and the outcoming tangent
    * If the angle is smaller than some threshold $\eta = 150°$, then the angle is considered sharp
2. Apply a simplification algorithm to the resulting shapes
    * For line segments: Ramer-Douglas-Peucker algorithm (from "Algorithms for the Reduction of the Number of Points Required to Represent a Digitized Line or its Caricature" (1973))
    * For segments of cubic Bézier curves: Philip J. Schneider algorithm (from "An Algorithm for Automatically Fitting Digitized Curves" (1990))
3. The resulting lines and Bézier curves are divided in multiple subsegments if their length is larger than some distance $\Delta = 5$. 
