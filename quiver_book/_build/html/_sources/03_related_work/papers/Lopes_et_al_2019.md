# Lopes et al. (2019) | A Learned Representation for Scalable Vector Graphics (svg-vae)

```{admonition} Available resources at a glance
* [arXiv URL to the paper](https://arxiv.org/abs/1904.02632)
* [Implementation as part of Google's Magenta, Github repo](https://github.com/tensorflow/magenta/tree/master/magenta/models/svg_vae)
* [Jupyter Notebook provided by Lopes](https://github.com/iRapha/magenta-demos/blob/master/colab-notebooks/vae_svg_decoding.ipynb)
* [Model checkpoints as tar.gz](https://storage.googleapis.com/magentadata/models/svg_vae/svg_vae.tar.gz)
```

:::{figure-md} svg-vae-home-fig
<img src="svg_vae_cover.png" alt="SVG-VAE paper by Lopes" width="900px">

Screenshot of the SVG-VAE [paper](https://arxiv.org/abs/1904.02632)
:::


## Model architecture

:::{figure-md} svg-vae-model-fig
<img src="svg_vae_model_architecture.png" alt="SVG-VAE model architecture" width="500px">

Screenshot of the SVG-VAE model architecture; taken from [paper](https://arxiv.org/abs/1904.02632)
:::

"Visual similarity between SVGs is learned by a class-conditioned, convolutional variational autoencoder (VAE) on a rendered representation (blue). The class label and learned representation z are provided as input to a model that decodes SVG commands (purple). The SVG decoder consists of stacked LSTMs followed by a Mixture Density Network (MDN)."


## Latent space interpolation

:::{figure-md} svg-vae-interpolations-fig
<img src="svg_vae_linear_interpolations.png" alt="SVG-VAE linear interpolations" width="700px">

Screenshot of linear interpolations; taken from [paper](https://arxiv.org/abs/1904.02632)
:::


The trained model allows for the generation of new characters and fonts by leveraging linear interpolations between two characters or two positions in the latent space.


## Semantically meaningful directions in latent space

:::{figure-md} svg-vae-directions-fig
<img src="svg_vae_meaningful_directions.png" alt="SVG-VAE meaningful directions" width="500px">

Screenshot of meaningful directions in latent space; taken from [paper](https://arxiv.org/abs/1904.02632)
:::

Interestingly, semantically meaningful directions could be identified. Examples, as shown above, are:
* Font weight: from light to bold
* Oblique or italic type
* Condensed


## Example results

:::{figure-md} svg-vae-results-fig
<img src="svg_vae_sample_randomly_generated_fonts.png" alt="SVG-VAE sample of randomly generated fonts" width="700px">

Screenshot of a sample of randomly generated fonts; taken from [paper](https://arxiv.org/abs/1904.02632)
:::



## Remarkable aspects of this work


* One of the first papers to develop an encoding scheme for SVG fonts that allows fonts to be fed into a Deep Learning model
* The paper demonstrates that different styles of fonts can be learned by the model



## Discussion and limitations

The paper is not easy to replicate with a relatively complicated pipeline including:
* Apache Parquet database
* FontForge's SFD format
* Tensorflow 1.0
* Tensor2Tensor library

The quality of the resulting SVG images is fairly limited.



