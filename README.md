# reactionrnn

reaction is a Python 2/3 module + R package on top of [Keras](https://github.com/fchollet/keras)/[TensorFlow](https://www.tensorflow.org) which can easily predict the proportionate reactions (love, wow, haha, sad, angry) to a given text using a pretrained recurrent neural network:

```python
from reactionrnn import reactionrnn

react = reactionrnn()
react.predict("Happy Mother's Day from the Chicago Cubs!")
```
```
[('love', 0.9765), ('wow', 0.0235), ('haha', 0.0), ('sad', 0.0), ('angry', 0.0)]
```

The pretrained model was trained on hundreds of thousands of Facebook posts. Unlike traditional text models using tools like word2vec/doc2vec/NLTK, reactionrnn handles text at the character level, allowing it to incorporate capitalization, grammar, sarcasm, and emoji.

```
> react.predict("This is scary AF!😱😱")
[('wow', 0.9109), ('sad', 0.0891), ('love', 0.0), ('haha', 0.0), ('angry', 0.0)]
```

```
> react.predict("When the soup is too hot 😂😂😂")
[('haha', 0.8568), ('love', 0.1376), ('wow', 0.0056), ('sad', 0.0), ('angry', 0.0)]
```

```
> react.predict("He was only 41.")
[('sad', 1.0), ('love', 0.0), ('wow', 0.0), ('haha', 0.0), ('angry', 0.0)]
```

```
> react.predict("Everyone loves autoplaying videos!")
[('angry', 0.8667), ('wow', 0.1333), ('love', 0.0), ('haha', 0.0), ('sad', 0.0)]
```

As a bonus, the model can encode text as a 256D vector (incorporating context of grammar/caps/punc/emoji) which can then be fed into other machine learning/deep learning models.

```
> react.encode("DYING. 😄")
[ 0.0411452   0.87985831  0.31406021, ...]
```

Did I mention that reactionnn is also available as an R package?

```
library(reactionrnn)
react <- reactionrnn()
react %>% predict("Happy Mother's Day from the Chicago Cubs!")
```

```
      love        wow       haha        sad      angry 
0.97649449 0.02350551 0.00000000 0.00000000 0.00000000 
```

## Usage

For Python, reactionrnn can be installed [from pypi](https://pypi.python.org/pypi/reactionrnn) via `pip`:

```
sudo pip3 install reactionrnn
```

For R, you can install reactionrnn from CRAN:

```
install.packages('reactionrnn')
```

You can view a demo of common features in [this Jupyter Notebook](/docs/textgenrnn-demo.ipynb). (full documentation coming soon)

`/datasets` contains example datasets using Hacker News/Reddit data for training textgenrnn.

`/weights` contains further-pretrained models on the aforementioned datasets which can be loaded into textgenrnn.

`/outputs` contains examples of text generated from the above pretrained models.

## Neural Network Architecture and Implementation

![](/docs/model_shapes.png)

reactionrnn is based off of the June 2016 blog post I wrote titled [Classifying the Emotions of Facebook Posts Using Reactions Data](http://minimaxir.com/2016/06/interactive-reactions/) which noted that there is nuance to the proportionality of the reactions on a Facebook status. What makes a Facebook post "WOW" but *not* "HAHA"? Is there a semantic difference between a post with 100% WOW and 50% WOW> A year later, Facebook now has enough public data to sufficiently train a neural network to understand these nuances.

reactionrnn takes in an input of up to 140 characters, converts each character to a 100D character embedding vector, and feeds those into a 256-cell gated recurrent unit layer. That output is mappedregressed ntage proportion* (**notch of the five non-Like Reactions; all  simultaneously and outputs predicted values for eachpredicted values will sum to 1.

The  (the output is **not** the probability of the label like a classification problem!)model weights included with the package are trained on the captions on hundreds of thousands of public Facebook statuses on Facebook Pages ([via my Facebook Page Post Scraper](https://github.com/minimaxir/facebook-page-post-scraper)), from a very *diverse* variety of subreddits/Pages (necessary since some Pages will have *very* different reactions to a given text!). The network was also trained in such a way that the `rnn` layer is decontextualized in order to both improve training performance and mitigate authorial and temporal bias.

The `encode` function returns the value produced by the GRU.


## Notes

* Keep in mind that the network is trained on modern (2016-2017) language. As a result, using rhetorical/ironic statements will often yield love/wow responses instead of sad/angry. 

* If a text sequence is >140 characters, reactionrnn will only use the first 140 characters.

* If you do use `encode` on multiple texts, I strongly recommend using [principal component analysis](https://en.wikipedia.org/wiki/Principal_component_analysis) to both reduce the high dimensionality of the text (i.e to 30-50D) and align the given encoded texts in context of each other. (see reactionrnn demos on how to implement PCA)

* A GPU is not required to use reactionrnn.

## Future Plans for textgenrnn

* A web-based implementation using Keras.js (works especially well due to the network's small size)

* A larger pretrained network which can accommodate longer character sequences and a more indepth understanding of language, creating better reaction predictions. This may be released as a commercial product instead, if any venture capitalists are interested.

## Maintainer/Creator

Max Woolf ([@minimaxir](http://minimaxir.com))

*Max's open-source projects are supported by his [Patreon](https://www.patreon.com/minimaxir). If you found this project helpful, any monetary contributions to the Patreon are appreciated and will be put to good creative use.*

## Disclaimer

reactionrnn is not supported by Facnot endorsed ebook.