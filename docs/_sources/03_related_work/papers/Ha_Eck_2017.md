# Ha & Eck (2017) | A Neural Representation of Sketch Drawings (sketch-rnn)


```{admonition} Available resources at a glance
* [arXiv URL to the paper](https://arxiv.org/abs/1704.03477)
```

:::{figure-md} sketchrnn_cover
<img src="sketchrnn_cover.png" alt="sketchrnn paper" width="600px">

Screenshot of the [sketchrnn paper](https://arxiv.org/abs/1704.03477)
:::


Ha and Eck used the drawings collected from users of Google's "Quick, Draw!" game to train a Recurrent Neural Network.


## Latent Space Interpolation

:::{figure-md} sketchrnn_latent_space_interpolation_1
<img src="sketchrnn_latent_space_interpolation_1.png" alt="sketchrnn Latent Space Interpolation" width="500px">

Screenshot of Latent Space Interpolation from the [sketchrnn paper](https://arxiv.org/abs/1704.03477)
:::


The trained model allows for a latent space interpolation, as shown in the figure above. However, the interpolation may not result in a visua that is semantically meaningful.


:::{figure-md} sketchrrn_latent_space_interpolation_2.png
<img src="sketchrrn_latent_space_interpolation_2.png" alt="sketchrnn Latent Space Interpolation" width="500px">

Screenshot of Latent Space Interpolation from the [sketchrnn paper](https://arxiv.org/abs/1704.03477)
:::

Similar to the famous NLP example of king/queen and male/female vectors, it was possible to perform basic arithmetics in embedding space, as shown in the figure above.

