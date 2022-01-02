# First steps

The first steps include:

* Filtering out SVGs
* Moving information from various places into the affected SVG elements
* Harmonizing color information
* Dealing with color gradients

## Filtering out unsuitable SVGs

We may want to filter out SVGs that appear unsuitable for training based on some pre-defined thresholds or heuristics. This step may be necessary especially when SVGs have been obtained from the Web and a variation within the data will be high (as opposed to SVGs that have been obtained from fonts, for example).

### Examples of rules for filtering out unsuitable SVGs

* SVGs too many elements
* SVGs containing any `<script>` tags
* SVGs containing animations (or we just use the initial frame)
* SVGs that are not well-formatted XML
* SVGs using less common (but still standard) elements or attributes that we, for the sake of simplicity, may not want to support. These may be:
  * elements
    * `<clipPath>` (incl. related attribute `clip-rule`)
    * `<text>`
    * `<mask>`
    * `<pattern>`
    * ...
  * attributes
    * `stroke-linecap`
    * `stroke-linejoin`
    * ...


## Removing elements that are not displayed (display="none")

Some SVGs are used as sprites by defining many icons. All but one icon are set to `display="none"` and do not get rendered.
Obviously, invisible parts of the SVG do not need to get encoded into a feature vector.

## Moving information from various places into the affected SVG elements

This can be:
* style information (color, stroke, fill, ...)
* transforms
* `<defs>` which are definitions, e.g. a pattern for a stroke or a fill, that are defined somewhere else in the SVG


### `<style>` elements

`<style>` can contain import information relating to SVG elements. When parsing an SVG element, this "outsourced" style information may be overlooked and get lost.  

Thus, to facilitate parsing, styles from `<style>` elements need to be moved into the SVG element style attributes.

### CSS information

CSS information which can even be placed in separate \*.css files needs to be pulled into the affected SVG elements themselves.
E.g. `class= ...`

### Groups

Groups with attributes can cause issues when parsing SVG elements. It may be useful to move the group's attributes to all member elements (moveGroupAttrsToElems).

## Harmonizing color information

There are various ways to define colors in SVGs (as well as CSS and HTML):

* Via RGB values:
  * `rgb(..., ..., ...)` with three integer values between 0 and 255
  * 6-digit form: `#rrggbb`, i.e. as 6 alphanumerical characters describing the 3 RGB values as 3 hexadecimals (from 00 to FF); the preceding `#` incidates a hexadecimal
  * 3-digit form: `#rgb`, i.e. as 3 alphanumerical characters describing the 3 RGB values as 3 hexadecimals; this works since each characters is interpreted as twice the character: e.g. `#09C` is interpreted as `#0099CC`; this reduces the number of possible colours to 4,096
* Via RGBA values, i.e. RGB and an alpha channel defining opacity
  * `rgba(..., ..., ..., ...)` where the last parameter defines opacity (float between 0.0 and 1.0; 1.0 = fully opaque; 0.0 = fully transparent)
* Via one of the 140 HTML standard string codes, e.g.
  * `"red"`
  * `"olive"`
  * `"dark cyan"`
* Via the HSL and HSLA color space (less common)

To facilitate parsing the color information, the color representation should be harmonized, e.g. as `rgba(..., ..., ..., ...)`.
Missing colour information shall be interpreted as black.

## Dealing with color gradients

SVGs containing color linear or radial gradients may or may not need to be treated -- depending on the chosen data representation.
For the sake of simplicity, we may want to replace a gradient by a single color. This could avoid the issue of reconstructed or generated images looking as if they had been painted using watercolors.

## Width, height, and viewbox

SVGs can contain shapes outside the viewbox. These do not get rendered and can be ignored or actively removed.
Size can be relative (100%) or absolute etc. etc. etc.

`width="500px" height="500px" viewBox="0 0 500 500"`

## Removing Adobe Illustrator <pgf> data

If an SVG is exported from Adobe Illustrator and the "Preserve Illustrator Editing Capabilities" option is checked, then the whole Illustrator document gets saved inside the SVG as a <pgf> tag. This bloats the SVG and can be ignored or removed.