# Google's "Quick, Draw!" (2017)

```{admonition} Available resources at a glance
* ["Quick, Draw!" website](https://quickdraw.withgoogle.com/)
* [Dataset viewer](https://quickdraw.withgoogle.com/data/)
* [Dataset viewer - category cats](https://quickdraw.withgoogle.com/data/cat)
* [Dataset Github repo](https://github.com/googlecreativelab/quickdraw-dataset)
```

:::{figure-md} quickdraw-home-fig
<img src="google_quickdraw_home.png" alt="Homepage of Google Quickdraw" width="500px">

Screenshot of the homepage of Google's ["Quick, Draw!"](https://quickdraw.withgoogle.com/)
:::

[Google's Quick, Draw!](https://quickdraw.withgoogle.com/) (also written as 'quickdraw') is an AI experiment published by Google in early 2017.

At the surface, "Quick, Draw!" can be considered a game where a participant has to quickly draw a concept in under 20 seconds. While the user is drawing, a machine learning model is attempting to guess which concept the user is attempting to draw. By adding drawings, participants help Google obtain a massive training dataset of sketches (features) and the target concept (label).


:::{figure-md} quickdraw-task-fig
<img src="google_quickdraw_task.png" alt="A task of Google Quickdraw" width="500px">

Screenshot of a typical drawing task from Google's ["Quick, Draw!"](https://quickdraw.withgoogle.com/)
:::

The sketch data also contains the order in which the user created the individual strokes of a drawing. This allows Quick, Draw! to animate the collected sketches and visualise in which order sketches were created.

Over 15 million players contributed around 50 million drawings across 345 categories.

## Dataset

:::{figure-md} quickdraw-dataset-fig
<img src="google_quickdraw_dataset.jpg" alt="Preview of the Google Quickdraw dataset" width="900px">

Screenshot of a sample of Google's ["Quick, Draw!"](https://quickdraw.withgoogle.com/) drawings obtained from [here](https://github.com/googlecreativelab/quickdraw-dataset/blob/master/preview.jpg).
:::

The Quick Draw Dataset is a collection of 50 million drawings across 345 categories, contributed by players of the game Quick, Draw!.
The drawings were captured as timestamped vectors, tagged with metadata including what the player was asked to draw and in which country the player was located.

The format is not SVG but a custom, simple to understand format.

:::{figure-md} quickdraw-cat-fig
<img src="google_quickdraw_cats.png" alt="Sample of cat drawings from the Google Quickdraw dataset" width="900px">

Screenshot of a sample of catdrawings from Google's ["Quick, Draw!"](https://quickdraw.withgoogle.com/) drawings obtained from [here](https://quickdraw.withgoogle.com/data/cat).
:::


### View dataset

The dataset of recognised drawings can be browsed on [quickdraw.withgoogle.com/data](https://quickdraw.withgoogle.com/data).


### Download dataset

The dataset of scribbles is [available online as a Github repo](https://github.com/googlecreativelab/quickdraw-dataset).


### Limitations


#### Dataset

Participants of the game only have 20 seconds to draw a concept. While this forces users to focus on the main visual characteristics of a concept, it also leaves little time to produce high-quality drawings.

Furthermore, users of the game typically use the computer mouse to produce the drawings which also leads to rather rough sketches.