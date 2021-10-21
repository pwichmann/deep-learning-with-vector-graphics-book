# Finishing touches

## Rounding numeric values

As a last step, we may want to round numeric values, such as parameters of the path data, to a fixed precision. It is likely not necessary to have 14 decimals.

Numeric values can already be rounded to a fixed precision ([cleanupNumericValues](https://github.com/svg/svgo/blob/master/plugins/cleanupNumericValues.js))


## Ensuring correct XML namespace

Since we want to export the preprocessed SVGs for visual inspection, we need to make sure the basic SVG tags have been defined correctly. Otherwise, e.g. with missing XML namespace, they may not get rendered in a browser.

```XML
<?xml version="1.0" encoding="UTF-8"?>
<svg viewBox="0 0 256 256" xmlns="http://www.w3.org/2000/svg">
```
