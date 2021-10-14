# From strokes to SVG

This chapter deals with the question how vector graphics can be represented as the numeric tensors required by deep neural networks. It aims to answer this question by telling the story of how the problem was addressed in various academic papers. From the initial representation of just strokes to data representations able to deal with basic SVG images.

## Sequences of strokes (polylines) as input

### Handwriting recorded via a smart whiteboard (2014)

In Graves (2014), handwriting data was used not in a raster image format but in an XML format. The data consisted of a sequence (x, y) pen co-ordinates with an additional flag if pen is lifted off the whiteboard or not. (Note to self: Check data once obtained if this is correct.)
This data format is certainly not as expressive as SVG but it is already a simplified vector graphics format available as numeric tensors. Each handwriting is a sequence of points (a polyline) and each point consists of an x-coordinate, a y-coordinate, and a binary (0 or 1) flag for the pen state.
The data format does not allow for curves; these could only be approximated by multiple straight lines of different slope. The sampling rate determined how close can can approximate a curve.

```XML
<.../>
To be added once we obtained data sample

```

### Sketches drawn with the PC mouse (2017)

#### Raw dataset

Google's "Quick, Draw!" used a similar approach. The drawing is represented as an array of strokes. Each stroke is a polyline, i.e. a sequence of straight lines, and represented by an array of arrays:

```
[ 
  [  // First stroke 
    [x0, x1, x2, x3, ...],
    [y0, y1, y2, y3, ...],
    [t0, t1, t2, t3, ...]
  ],
  [  // Second stroke
    [x0, x1, x2, x3, ...],
    [y0, y1, y2, y3, ...],
    [t0, t1, t2, t3, ...]
  ],
  ... // Additional strokes
]
```

Each sketch in the [raw dataset](https://github.com/googlecreativelab/quickdraw-dataset) is provided as a Newline-delimited JSON ([ndjson](http://ndjson.org/)) file containing the drawing itself as well as some metadata, such as the word the user was supposed to draw. A complete raw format example including the metadata is shown in the json below:

```json
{
  "word":"airplane",
  "countrycode":"CZ",
  "timestamp":"2017-03-25 09:39:37.15337 UTC",
  "recognized":true,
  "key_id":"5302633118040064",
  "drawing":
  [
    [
      [157,151.9720001220703,147.02999877929688,141.1699981689453,135.77699279785156,130.7969970703125,126.39700317382812,120.4990005493164,115.2979965209961,109.875,105.95800018310547,103.5,102.5,105.36299896240234,110.13400268554688,116.0739974975586,121,126,131.66900634765625,137.85000610351562,141.98500061035156,147,154.4530029296875,159.16900634765625,164,170.58799743652344,176.84500122070312,183.82200622558594,188.10699462890625,195.0449981689453,202.04800415039062,208.9219970703125,212.947998046875,219.98899841308594,226.83399963378906,233.5030059814453,238.17799377441406,242.9080047607422,250.26300048828125,257.16900634765625,262.0249938964844,267.40899658203125,267.5],
      [153,152.4969940185547,154.9849853515625,157.9429931640625,160.40798950195312,162.85101318359375,164.802001953125,168.25100708007812,171.70199584960938,176.625,182.08401489257812,186.47500610351562,191.25,195.86300659179688,198.13400268554688,199.5,200,200,199.5,198.21701049804688,197.5,196,194.3489990234375,192.94400024414062,192,190.47100830078125,188.10299682617188,185.55899047851562,184.15701293945312,181.114013671875,178.302001953125,175.6929931640625,173.92098999023438,171.00601196289062,167.7919921875,164.66400146484375,162.59298706054688,160.83700561523438,156.82501220703125,152.38800048828125,149.47500610351562,146.54600524902344,146.5],
      [0,134,179,212,246,279,312,362,413,478,548,612,695,812,894,976,1044,1092,1158,1202,1238,1261,1313,1345,1376,1412,1447,1476,1492,1544,1567,1591,1608,1642,1675,1708,1725,1745,1774,1809,1841,1895,1930]
    ],
    [
      [125,131,136.5240020751953,143.0469970703125,148.9499969482422,155.2830047607422,161.6929931640625,168.5540008544922,175.27099609375,181.83999633789062,188.72500610351562,195.0850067138672,202.9770050048828,209.35499572753906,216.31300354003906,222.1269989013672,227.01400756835938,231.4199981689453,237.302001953125,243.19000244140625,249,254.9530029296875,260,265,270,274.9670104980469,279,284.3840026855469,288.63299560546875,294.1499938964844,299,304,309.38299560546875,314,314.5,307.8349914550781,303.2919921875,297.6679992675781,292.843994140625,287.2950134277344,279.6839904785156,274.5199890136719,270.50799560546875,264.62298583984375,260.5,260.5],
      [163,160,158.49200439453125,156.48800659179688,154.52499389648438,152.07200622558594,149.6020050048828,146.6490020751953,143.36399841308594,140.44000244140625,137.01699829101562,134.30499267578125,130.6820068359375,127.26300048828125,124.4219970703125,121.43600463867188,118.99099731445312,117.53999328613281,115.0989990234375,113.15499877929688,111,108.77400207519531,107,105,103,101.03300476074219,100,98.5,97.36700439453125,96.42500305175781,96,96,96.5,99,103.50100708007812,111.97000122070312,116.88499450683594,121.16499328613281,126.156005859375,130.47000122070312,137.23699951171875,141.97999572753906,145.99200439453125,150.93800354003906,154.5,154.5],
      [2289,2428,2457,2489,2527,2558,2587,2621,2654,2688,2721,2756,2790,2822,2853,2887,2923,2952,3003,3053,3102,3152,3202,3253,3301,3380,3493,3610,3650,3702,3753,3800,3849,3916,3966,4017,4033,4049,4066,4082,4116,4148,4182,4246,4303,4328]
    ],
				
      // ... omitted ...

    ]
}
```

In the json, it may appear that arrays  for the same stroke have a different number of elements but this is just due to values with a different number of decimal places.


The information "pen up" or "pen down" (here: left mouse button pressed or not) must have already been used to distinguish the strokes.

The data representation above can also be mentally reshaped, such that a drawing can also be understood as a sequence of triples. Each triple then consists of the x-coordinate, the y-coordinate and the time in milliseconds $t$ that passed since drawing the first point (the time information allows for an animation of the drawing). You can see that the first time element of the first stroke indeed has the value 0, as we would expect.


For a generative model or classifier, the timing information in the raw data is probably less useful. And the x- and y-coordinates, sometimes using 14 decimal places, are unnecessarily precise.


#### Preprocessed simplified dataset

A simplified version of the drawing data discards the timing information and performs a series of preprocessing steps:

1. The drawing is **aligned to the top-left corner**, to have a minimum values of 0.
2. The drawing is **uniformly scaled to have a maximum value of 255**.
3. All strokes are **resampled** with a 1 pixel spacing. (This gets rid of the 14 decimal places...)
4. All strokes are simplified using the **[Ramer–Douglas–Peucker algorithm](https://en.wikipedia.org/wiki/Ramer%E2%80%93Douglas%E2%80%93Peucker_algorithm)** with an epsilon value of 2.0. (This reduces the number of points while trying to maintain the overall shape of the polyline.)

The simplification also reduces the file size to about 1/10th.

A simplied example is shown in the json below:

```json
{
  "word":"airplane",
  "countrycode":"CZ",
  "timestamp":"2017-03-25 09:39:37.15337 UTC",
  "recognized":true,
  "key_id":"5302633118040064",
  "drawing":[
    [
      [74,68,38,26,16,12,15,39,90,110,173,201],
      [104,104,118,126,137,148,153,158,147,140,113,97]
    ],
    [
      [37,87,155,210,237,249,254,254,242,193],
      [116,97,65,44,39,39,42,48,62,106]
    ],
    [
      [161,155,155,166,199],
      [58,35,16,16,34]
    ],
    [
      [227,246,246,239,221,203],
      [82,104,117,122,123,113]
    ],	
    [
      [67,13,3,0,8,22,71,94,125],
      [95,35,17,0,0,5,29,46,78]
    ],
    [
      [95,103,122,147,169,173,169,126],
      [135,165,199,229,244,237,221,136]
    ],
    [
      [33,39],
      [114,120]
    ]
  ]
}

```

The first key-value pairs represent metadata, such as the word prompt. The drawing is now represented by an array of strokes. Each stroke is represented by an array of x-coordinates and an array of y-coordinates of the same length.

The exact image resulting from json description above is shown below:


:::{figure-md} airplane_drawing
<img src="airplane_drawing.png" alt="Airplane drawing" width="200px">

Resulting airplane drawing; from [Google's Quickdraw Viewer](https://quickdraw.withgoogle.com/data/airplane)
:::

In this case, the artist used 7 strokes which is hard to derive from the image alone. 3 strokes were used for the aircraft body, one for each wing and one for each tail fin. These 7 strokes can be found again in the json representation of the strokes. It's the number of elements in the top-level array.


## SVG images as input


### Simple SVGs obtained from TTF fonts (2018)

In her Master's thesis, Kimberli Zhong (2018) builds upon the work by David Ha but extends the data representation. She allows simplified SVG images to be the input to her model. The SVG images are obtained from TTF fonts which only allow for a restricted range of SVG commands.

Nevertheless, the obtained SVGs have to be further preprocessed and simplified.







