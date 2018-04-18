---
date: 2018-04-07T20:00:00+03:00
lastmod: 2018-04-07T20:00:00+03:00
title: One-Hot layer in Keras's Sequential API
authors: ["fdalvi"]
categories:
  - techtips
tags:
  - Deep learning
  - Neural networks
  - keras
  - python
  - representations
slug: 2018-04-07-keras-sequential-onehot
---

It is quite common to use a [One-Hot](https://en.wikipedia.org/wiki/One-hot) representation for categorical data in machine learning, for example textual instances in Natural Language Processing tasks. In Keras, the [Embedding](https://keras.io/layers/embeddings/) layer automatically takes inputs with the category indices (such as `[5, 3, 1, 5]`) and converts them into dense vectors of some length (e.g. `5` → `[0.2 1.7 3.2 -7.6 ...]`). What actually happens internally is that `5` gets converted to a one-hot vector (like `[0 0 0 0 0 1 0 0 ... 0]` of length equal to the vocabulary size), and is then multiplied by a normal weight matrix (such as a Dense layer), essentially picking the `5th` indexed row from the weight matrix. However, there is no way in Keras to just get a one-hot vector as the output of a layer [\[1\]](https://github.com/keras-team/keras/issues/4838) [\[2\]](https://github.com/keras-team/keras/issues/3680). The default proposed solution is to use a Lambda layer as follows: `Lambda(K.one_hot)`, but this has a few caveats - the biggest one being that the input to `K.one_hot` must be an integer tensor, but by default Keras passes around float tensors.

There is an [excellent gist by Bohumír Zámečník](https://gist.github.com/bzamecnik/a33052ec46ee7efeb217856d98a4fb5f) working around these issues, but it uses the functional API. When preparing some course material where we were exclusively using the Sequential API, the proposed solution did not work well, specifically because we can't use an `Input` layer in the Sequential API to force the `OneHot` layer's input to be an integer tensor.

Let's look at an implementation that will work well with the Sequential API:

{{< highlight Python >}}
from keras.layers import Lambda
# We will use `one_hot` as implemented by one of the backends
from keras import backend as K

def OneHot(input_dim=None, input_length=None):
    # Check if inputs were supplied correctly
    if input_dim is None or input_length is None:
        raise TypeError("input_dim or input_length is not set")

    # Helper method (not inlined for clarity)
    def _one_hot(x, num_classes):
        return K.one_hot(K.cast(x, 'uint8'),
                          num_classes=num_classes)

    # Final layer representation as a Lambda layer
    return Lambda(_one_hot,
                  arguments={'num_classes': input_dim},
                  input_shape=(input_length,))
{{< / highlight >}}

Let's walk through the implementation - the goal here was to stay consistent with the arguments used by the Embedding layer, namely `input_dim` and `input_length`. `input_dim` refers to the eventual length of the one-hot vector (e.g. vocab size), and `input_length` refers to the length of the input sequence (usually padded/truncated to some `MAX_SEQUENCE_LENGTH`).

The first thing we do with the input `x` is to cast it an integer tensor. Next, we pass it to `K.one_hot` along with `num_classes` (the eventual length of the one-hot vector). Finally, we use this `_one_hot` function in a [Lambda](https://keras.io/layers/core/#lambda) layer (that applies arbitrary functions on its inputs). We use the special `arguments` argument to pass in the parameters expected by `_one_hot`.

Let's now look at a concrete example. Let our input be a matrix of two examples, each of length 5:

{{< highlight Python >}}
X = np.array([
    [5, 2, 4, 25, 17], # Instance 1
    [15, 54, 13, 2, 98] # Instance 2
])

print(X.shape) # prints (2, 5)
{{< / highlight >}}

If we were to just use an Embedding layer normally with `VOCAB_SIZE=100`, `EMBEDDING_SIZE=25` and `MAX_SEQUENCE_LENGTH=5`, the output of the Embedding layer would be of shape `[2 x 5 x 25]`, where we have an embedding vector of length `25` for each of the `5` tokens across the `2` examples:

{{< highlight Python >}}
model = Sequential()
model.add(Embedding(input_dim=VOCAB_SIZE,
                    output_dim=EMBEDDING_SIZE,
                    input_length=MAX_SEQUENCE_LENGTH))

model.compile(loss='mse', optimizer='sgd')
print(model.predict(X, batch_size=32).shape)  # prints (2, 5, 25)
{{< / highlight >}}

Now, we can use our drop-in replacement layer `OneHot`, and its output would be of shape `[2 x 5 x 100]`. Here, each token is represented as a 100-long *one-hot* vector:

{{< highlight Python >}}
model = Sequential()
model.add(OneHot(input_dim=VOCAB_SIZE,
                         input_length=MAX_SEQUENCE_LENGTH))

model.compile(loss='mse', optimizer='sgd')
print(model.predict(X, batch_size=32).shape) # prints (2, 5, 100)
{{< / highlight >}}

Note that we don't use the argument `output_dim` from the Embedding layer since we are only interesting in the one-hot representation, not an embedding from the weight matrix.

Finally for the sake of completeness, if you want to replicate the Embedding layer using this `OneHot` layer, it would look something like this:

{{< highlight Python >}}
model = Sequential()
model.add(OneHot(input_dim=VOCAB_SIZE,
                 input_length=MAX_SEQUENCE_LENGTH))
model.add(TimeDistributed(Dense(EMBEDDING_SIZE)))

model.compile(loss='mse', optimizer='sgd')
print(model.predict(X, batch_size=32).shape) # prints (2, 5, 25)
{{< / highlight >}}

We use a [TimeDistributed](https://keras.io/layers/wrappers/#timedistributed) wrapper to apply the same Dense layer for each time step in the input sequence. This Dense layer would hold the weights (or embeddings) for each word in our vocabulary. Note that the output of this model is exactly the same as the model with the Embedding layer!

Some thoughts following this implementation:

* Its a good question to ask why we need a `OneHot` layer anyways (and not just use the Embedding layer). In my case, I wanted to show an RNN model that has an explicit Embedding layer vs one that does not, and hence it was necessary to pass the one-hot vector as is to the recurrent layer. I'm sure there are other scenarios as well, given the discussions around the web regarding this topic.
* Another potential solution is to just convert the input matrix `X` into a one-hot representation before passing it as input (e.g. using `keras.utils.to_categorical`) - but this method quickly fails when you have a lot of data. With 1000 instances of length 50 and a vocabulary of 5000 words, the one-hot representation would need `1000 x 50 x 5000` entries in the one-hot tensor, and if each entry is of 8 bytes, that adds up to 2GB of memory for representing the input alone! Hence, creating the one-hot representations on the fly becomes the only option.