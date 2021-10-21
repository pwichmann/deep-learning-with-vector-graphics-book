# Reducing the set of different path commands

There are 10 different [path data commands](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#path_commands) for relative positioning and 10 for absolute positioning. Not all of these 10 different path data commands are actually required -- some can be represented using the other more basic commands.

## Why do we want to reduce the number of different path data commands?

If commands can be replaced by others, then replacing them reduces the complexity and should help the neural network with learning.
Some commands can indeed be replaced without any information loss (perfect substitutes). Another one, the elliptical arc curve, can be closely approximated by other commands (even though there is no perfect substitute).


## Replacement of path data commands

In the following, we assume that we have harmonized all commands and only use the absolute positioning.

It appears that the minimal set of path data commands is the following subset:

* `M` [MoveTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#moveto_path_commands) 
* `L` [LineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands) 
* `C` [Cubic Bézier Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#cubic_b%C3%A9zier_curve) 
* `Z` [ClosePath](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#closepath)

One could argue (and Zhong (2018) does), that all three commands lines, quadratic bézier curves, and cubic bézier curves could be represented by just cubic bézier curves. Zhong, thus, uses cubic Bézier curves exclusively.

The following commands can be replaced or at least approximated by the four commands listed above:


| Commands that can be replaced or approximated | Letter | Explanation | Replacement |
|---|---|---|---|
| **[VerticalLineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands)** | `V` | This is just a simple way of defining a vertical line. LineTo is the general commands for all types of straight lines and can also represent vertical lines. In case of absolute positioning, the parameter $y$ does not specify the length of the line but the y position of the desired end point, whereas the x position is kept the same as the current point. | LineTo L |
| **[HorizontalLineTo](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#lineto_path_commands)** | `H` | This is just a simple way of defining a horizontal line. LineTo is the general commands for all types of straight lines and can also represent horizontal lines. In case of absolute positioning, the parameter $x$ does not specify the length of the line but the x position of the desired end point, whereas the y position is kept the same as the current point.| LineTo L |
| **[SmoothCubicBezier](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#cubic_b%C3%A9zier_curve)** | `S` | This is just a simple way of defining a smooth cubic bézier curve. The cubic bézier C is the general commands for all types of cubic bézier curves and can also represent smooth cubic béziers. | Cubic Bézier Curve C |
| **[Quadratic Bézier Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#quadratic_b%C3%A9zier_curve)** | `Q` | Higher-order cubic bézier curves can also represent quadratic bézier curves | Cubic Bézier Curve C |
| **[Smooth Quadratic Bezier](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#quadratic_b%C3%A9zier_curve)** | `T` | This is just a simple way of defining a smooth quadratic bézier curve. The cubic bézier C can also represent all quadratic, including smooth quadratic béziers. | Cubic  Bézier Curve C |
| **[Elliptical Arc Curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#elliptical_arc_curve)** | `A` | Elliptical arc curves are curves defined as a portion of an ellipse. | Converted to cubic Bézier curves which can (only) approximate the elliptical arc. |

### Overview of used commands in various papers

* Zhong (2018) does not use the LineTo command but also represents this command using a cubic bézier curve.
* Lopes et al. (2019) use the same minimal set (`{ 'M', 'L', 'C' }`) but do not appear to use the `z` command.
* Carlier et al. (2020) use the above minimal set and include the `z` command.

### Replacement options for each command


#### Replacement of VerticalLineTo V (perfect substitute: L)

#### Replacement of HorizontalLineTo H (perfect substitute: L)

#### Replacement of Smooth Cubic Bezier S (perfect substitute: C)

#### Replacement of Quadratic Bézier Curve Q (perfect substitute: C)

Any quadratic spline can be expressed as a cubic (where the cubic term is zero). The end points of the cubic will be the same as the quadratic's. (https://stackoverflow.com/questions/3162645/convert-a-quadratic-bezier-to-a-cubic-one / https://fontforge.org/docs/techref/bezier.html#converting-truetype-to-postscript)

Position for the control points of a cubic Bézier that behave exactly like a quadratic Bézier:

$C1 = 2/3*C + 1/3*P1$

$C2 = 2/3*C + 1/3*P2$

#### Replacement of Smooth Quadratic Bezier T (perfect substitute: C)
 
Any quadratic spline can be expressed as a cubic (where the cubic term is zero). The end points of the cubic will be the same as the quadratic's. (https://stackoverflow.com/questions/3162645/convert-a-quadratic-bezier-to-a-cubic-one / https://fontforge.org/docs/techref/bezier.html#converting-truetype-to-postscript)

#### Replacement of Elliptical Arc Curve A (approximation only!)

An elliptical arc curve (`A` / `a`) cannot be perfectly transformed into a cubic Bézier. It can be approximated by cubic Bézier curves though -- the required mathematics are not trivial.

Luckily, a smart person of the name Luc Maisonobe, who -- if correctly identified -- appears to be a flight dynamics expert at CS Group and a free software developer (The Apache Software Foundation), published a technical paper titled "Drawing an elliptical arc using polylines, quadratic or cubic Bézier curves" on spaceroots.org in 2003 (domain unfortunately no longer active). Copies of this paper can still be found on the Web.


##### Parametrization


The parametrization of elliptical arc curves in the SVG standard is called "endpoint parametrization". The [SVG standards describe one of the advantages](https://www.w3.org/TR/2018/CR-SVG2-20180807/implnote.html#ArcSyntax) of this parametrization as follows: it permits a consistent path syntax in which all path commands end in the coordinates of the new "current point".

The parameters used for this "endpoint parametrization" are:

* $(x_1, y_1)$ -- absolute coordinates of the **current point** on the path (obtained from the last two parameters of the previous path command)
* $r_x$ and $r_y$ -- radii of the ellipse (also known as its semi-major and semi-minor axes)
* $\phi$ -- angle from the x-axis of the current coordinate system to the x-axis of the ellipse
* $f_A$ -- large arc flag, and is 0 if an arc spanning less than or equal to 180 degrees is chosen, or 1 if an arc spanning greater than 180 degrees is chosen
* $f_S$ -- sweep flag, and is 0 if the line joining center to arc sweeps through decreasing angles, or 1 if it sweeps through increasing angles
* $(x_2, y_2)$ -- absolute coordinates of the final point of the arc
 
The parametrization can be converted from "endpoint parametrization" to "center parametrization" (and back). "Center" refers to the center of the ellipse and the mental model of this parametrization is one of a circle that has been stretched and rotated.

* $(c_x, c_y)$ -- coordinates of the center of the ellipse
* $r_x$ and $r_y$ -- radii of the ellipse (also known as its semi-major and semi-minor axes) - these are identical in both parametrization options
* $\phi$ -- angle from the x-axis of the current coordinate system to the x-axis of the ellipse - this is identical in both parametrization options
* $\theta$ -- angle around the arc that the point on the ellipse $(x, y)$ lies at
* $\theta_1$ -- start angle of the elliptical arc prior to the stretch and rotate operations
* $\theta_2$ -- end angle of the elliptical arc prior to the stretch and rotate operations
* $\Delta \theta$ -- difference between these two angles

Given the following variables from the "endpoint parametrization":

* $(x_1, y_1)$
* $r_x$ and $r_y$
* $\phi$
* $f_A$
* $f_S$
* $(x_2, y_2)$

the task is to find:

* $(c_x, c_y)$
* $\theta_1$
* $\Delta \theta$

The [formula for a conversion to center parametrization](https://www.w3.org/TR/2018/CR-SVG2-20180807/implnote.html#ArcConversionEndpointToCenter) can be found in the SVG Standard.


Todo:
* https://pomax.github.io/BezierInfo-2/#circles_cubic
* https://mortoray.com/2017/02/16/rendering-an-svg-elliptical-arc-as-bezier-curves/
* https://www.w3.org/TR/2018/CR-SVG2-20180807/implnote.html#ArcImplementationNotes


[Stackoverflow comment](https://stackoverflow.com/a/5589051): The error is most dependent on the difference of the start and end angles. I've had good success by limiting the angle difference to 60°. That is, I make a separate cubic segment for every 60° (or fraction thereof) and chain them together.

Further material can be found here:
* https://pomax.github.io/BezierInfo-2/#circles_cubic
* https://mortoray.com/2017/02/16/rendering-an-svg-elliptical-arc-as-bezier-curves/


##### Description of the conversion by Zhong (2018)

1. Extract the arc parameters: start coordinates, end coordinates, ellipse major and minor radii, rotation angle from x and y axes, large arc flag, and sweep flag.
2. Transform ellipse into unit circle by rotating, scaling, and translating, and save those transformation factors.
3. Find the arc angle from the transformed start point to end point on the unit circle.
4. If needed, split the arc into segments, each covering an angle less than 90°.
5. Approximate each segment angle’s arc on a unit circle such that the distance from the circle center to the arc along the angle bisector of the arc is equal to the radius, defining cubic Bézier control points.
6. Invert the transformation above to convert arcs along the unit circle back to the elliptical arc, transforming the generated control points accordingly.
7. Use these transformed control points to parameterize the output cubic Bézier curve.


##### Implementation in DeepSVG

In DeepSVG, the implementation can be found in `deepsvg/svglib/svg_command.py`:

```Python
class SVGCommandArc(SVGCommand):
    def __init__(self, start_pos: Point, radius: Radius, x_axis_rotation: Angle, large_arc_flag: Flag, sweep_flag: Flag, end_pos: Point):
        super().__init__(SVGCmdEnum.ELLIPTIC_ARC, [radius, x_axis_rotation, large_arc_flag, sweep_flag, end_pos], start_pos, end_pos)

        self.radius = radius
        self.x_axis_rotation = x_axis_rotation
        self.large_arc_flag = large_arc_flag
        self.sweep_flag = sweep_flag

    # ... omitted

    def _get_center_parametrization(self):
        r = self.radius
        p1, p2 = self.start_pos, self.end_pos

        h, m = 0.5 * (p1 - p2), 0.5 * (p1 + p2)
        p1_trans = h.rotate(-self.x_axis_rotation)

        sign = -1 if self.large_arc_flag.flag == self.sweep_flag.flag else 1
        x2, y2, rx2, ry2 = p1_trans.x**2, p1_trans.y**2, r.x**2, r.y**2
        sqrt = math.sqrt(max((rx2*ry2 - rx2*y2 - ry2*x2) / (rx2*y2 + ry2*x2), 0.))
        c_trans = sign * sqrt * Point(r.x * p1_trans.y / r.y, -r.y * p1_trans.x / r.x)

        c = c_trans.rotate(self.x_axis_rotation) + m

        d, ns = (p1_trans - c_trans) / r, -(p1_trans + c_trans) / r

        theta_1 = Point(1, 0).angle(d, signed=True)

        delta_theta = d.angle(ns, signed=True)
        delta_theta.deg %= 360
        if self.sweep_flag.flag == 0 and delta_theta.deg > 0:
            delta_theta = delta_theta - Angle(360)
        if self.sweep_flag == 1 and delta_theta.deg < 0:
            delta_theta = delta_theta + Angle(360)

        return c, theta_1, delta_theta

    # ... omitted

    def _get_derivative(self, t: float_type):
        r = self.radius
        return Point(-r.x * np.sin(t), r.y * np.cos(t)).rotate(self.x_axis_rotation)

    def to_beziers(self):
        
        beziers = []

        c, theta_1, delta_theta = self._get_center_parametrization()
        nb_curves = max(int(abs(delta_theta.deg) // 45), 1)
        etas = [theta_1 + i * delta_theta / nb_curves for i in range(nb_curves+1)]
        for eta_1, eta_2 in zip(etas[:-1], etas[1:]):
            e1, e2 = eta_1.rad, eta_2.rad
            alpha = np.sin(e2 - e1) * (math.sqrt(4 + 3 * np.tan(0.5 * (e2 - e1))**2) - 1) / 3
            p1, p2 = self._get_point(c, e1), self._get_point(c, e2)
            q1 = p1 + alpha * self._get_derivative(e1)
            q2 = p2 - alpha * self._get_derivative(e2)
            beziers.append(SVGCommandBezier(p1, q1, q2, p2))
```

## Additional "start of SVG" and "end of SVG commands"

Zhong (2018) uses three dimensions in each drawing command to denote the pen state:
* pen down ($p_d$)
* pen up ($p_u$) -- why do we need this if $p_d$ is binary(?)
* end drawing ($p_e$)

Lopes et al. (2019) use an `<EOS>`  command to denote the end of an SVG.

Carlier et al. (DeepSVG) also add two commands that denote the start and end of the SVG:

* `<SOS>` -- start of the SVG token
* `<EOS>` -- end of the SVG token; this command is also used for padding to fill up the sequence of commands up to the permitted maximum number of commands.
All arguments of these two additional commands are always equal to `-1`.