# Common preprocessing


## "De-minify" path data

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

The SVGO library for node.js is an excellent tool for preprocessing SVGs, such that they can be more easily parsed into numeric tensors.

To "de-minify" SVGs, the following yml settings can be used in SVGO

```yml
 - convertPathData:
      noSpaceAfterFlags: false  # this is crucial; otherwise flags can be merged with numbers and it gets hard to parse
      collapseRepeated: false  # this is crucial; otherwise successive arcs (a / A commands) get merged into one long arc command
```
