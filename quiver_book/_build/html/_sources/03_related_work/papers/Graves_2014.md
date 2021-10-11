# Alex Graves (2014) | Generating Sequences With Recurrent Neural Networks


The paper shows how LSTM (Long Short-Term Memory) recurrent neural networks (RNNs) can be used to auto-regressively generate complex sequences. The approach is demonstrated for (a) discrete-valued text and (b) real-valued handwriting. The work is then extended to handwriting synthesis by allowing the network to condition its predictions on a text sequence. The generated handwriting is indistinguishable from real handwriting samples. The handwriting dataset consists of a sequence of (x, y) coordinates and a pen state variable. Thus, neither training data nor the generated output are based on raster images.


The handwriting synthesis portion of this paper inspired David Ha's blog posts in December 2015 and the subsequent sketch-rrn work.


```{admonition} Available resources at a glance
* [Paper PDF on ArXiv](https://arxiv.org/pdf/1308.0850.pdf)
```


:::{figure-md} graves_2014_cover
<img src="graves_2014_cover.png" alt="Alex Graves' 2014 paper" width="500px">

Screenshot of [Alex Graves' paper](https://arxiv.org/pdf/1308.0850.pdf)
:::


## Handwriting synthesis

:::{figure-md} graves_2014_handwriting_sample
<img src="graves_2014_handwriting_sample.png" alt="Handwriting synthesis examples from Alex Graves' paper" width="800px">

Screenshot of handwriting synthesis examples -- the first line is real, the other lines are generated samples from the synthesis network; taken from [Alex Graves' paper](https://arxiv.org/pdf/1308.0850.pdf)
:::


### Dataset for handwriting: IAM-OnDB

The dataset used for the handwriting portion is obtained from the IAM Online Handwriting Database (IAM-OnDB). The IAM-OnDB consists of handwritten lines collected from 221 different writers using a "smart whiteboard". The writers were asked to write forms from the Lancaster-Oslo-Bergen text corpus, and the position of their pen was tracked using an infra-red device in the corner of the board.

The original input data consists of the x and y pen co-ordinates and the points in the sequence when the pen is lifted off the whiteboard.

### Preprocessing

Recording errors in the (x, y) data was corrected by interpolating to fill in for missing readings, and removing steps whose length exceeded a certain threshold
