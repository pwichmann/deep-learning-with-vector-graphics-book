# Harmonizing command positioning logic

SVG path data commands can either use **relative** positioning or **absolute** positioning. Absolute positioning is indicated by an upper case command letter (e.g. `A`); relative positioning is indicated by a lower case command letter (e.g. `a`).
Either option can be converted into the other without any information loss. Harmonizing all SVGs to only use one option is desirable to reduce complexity.

Just switching the case of the command letter is not enough, the parameters also need to be re-computed from relative (e.g. $\Delta x$ and $\Delta y$) to absolute values (e.g. $x$ and $y$).

```{admonition} Open question
:class: important
Is there a reason to prefer one system over the other? Lopes et al. (2019) stress how important **relative** positioning was. Carlier et al. (DeepSVG), however, use **absolute** positioning. In what situations should one prefer which positioning logic?
```

## Converting positioning logic

E.g. in DeepSVG all commands using relative positioning are converted to absolute commands.


## Options for converting positioning logic

This process does not necessarily have to be re-implemented. Many vector graphics drawing tools or SVG optimization tools provide such functionality.

* The [convertPathData](https://github.com/svg/svgo/blob/master/plugins/convertPathData.js) plugin of SVGO can convert commands and parameters from relative to absolute positioning (and vice versa).
* Inkscape also appears to allow for changing the positioning: In settings, under "SVG Output", set "Path string format" to "Absolute" and save as plain SVG. You may have to nudge the object so Inkskape will reset the 'd' attribute for the path. (https://stackoverflow.com/a/30046737)

