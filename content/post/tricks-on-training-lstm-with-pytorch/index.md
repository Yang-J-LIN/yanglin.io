+++
title = "Tricks on training LSTM with Pytorch"
subtitle = "Especially for variable-length long sequences"

# Add a summary to display on homepage (optional).
summary = "Training LSTM is not a easy thing for beginner in this field. There are a lot of tricks in choosing the most appropriate hyperparameters and structures, which has to be learned from a lot of experience. In this article, I'd love to share some tricks that I summarized from my experience and docs on the Internet."

date = 2019-09-26T04:15:00+08:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["admin"]

# Is this a featured post? (true/false)
featured = false

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["lstm", "tricks", "pytorch"]
categories = ["Blog"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
[image]
  # Caption (optional)
  caption = "The Herbert Wertheim College of Engineering at the University of Florida. Source: https://www.eng.ufl.edu/about/"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""
+++

Training LSTM is not a easy thing for beginner in this field. There are a lot of tricks in choosing the most appropriate hyperparameters and structures, which has to be learned from a lot of experience. In this article, I'd love to share some tricks that I summarized from my experience and docs on the Internet.

## Dealing with variable-length sequence

### Minibatch
Variable-length sequence can sometimes be very annoying, especially when we want to apply minibatch to accelerate the training. Fortunately, PyTorch has prepared a bunch of tools to facilitate it. You can find two useful functions in `torch.nn.utils.rnn` -- `pack_sequence()` and `pad_packed_sequence()`. Here are the prototypes of these two functions:
```python
pack_sequence(sequences, enforce_sorted=True)
```
`pack_sequence()` takes a list as the input, which contains the sequence you want to feed the LSTM, and it returns a PackedSequence (Refer to the docs of `torch.nn.utils.rnn` for details).
```python
pad_packed_sequence(sequence, batch_first=False, padding_value=0.0, total_length=None)
```
`pad_packed_sequence()` takes a PackedSequence as the input, and returns a padded tensor.

Here is an example.
```python
>>> import torch
>>> from torch.nn.utils.rnn import pack_sequence
>>> from torch.nn.utils.rnn import pad_packed_sequence
>>> a = torch.tensor([1, 2, 3])
>>> b = torch.tensor([1, 2, 3, 4])
>>> c = torch.tensor([1, 2, 3, 4, 5])
>>> packed_sequence = pack_sequence([a, b, c], enforce_sorted=False)
>>> print(packed_sequence)
PackedSequence(data=tensor([1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 5]), batch_sizes=tensor([3, 3, 3, 2, 1]), sorted_indices=tensor([2, 1, 0]), unsorted_indices=tensor([2, 1, 0]))
>>> sequence = pad_packed_sequence(packed_sequence)
>>> print(sequence)
(tensor([[1, 1, 1],
        [2, 2, 2],
        [3, 3, 3],
        [0, 4, 4],
        [0, 0, 5]]), tensor([3, 4, 5]))
```
They also work well for high-dimension inputs, such as the sentence represention after embedding.
These functions are really conveinent. You just need to place `pack_sequence()` before your LSTM and `pad_packed_sequence` after your LSTM. Here is part of implementation of my BiLSTM:
```python
embed = [self.embed(i) for i in x]
packed_embed = pack_sequence(embed, enforce_sorted=False)
bilstm_out, _ = self.bilstm(packed_embed)
bilstm_out, _ = pad_packed_sequence(bilstm_out)
```
### Special Notes for Dropout
Dropout cannot take a `PackedSequence` as the inputs, so you are supposed to dropout your inputs before they are packed to be `PackedSequence`. I achieve this goal by this:
```python
# In __init__(self) of your self-defined LSTM class
self.dropout = nn.Dropout(p=0.5)

# In forward(self, x) of your self-defined LSTM class
embed = [self.dropout(self.embed(i)) for i in x]
packed_embed = pack_sequence(embed, enforce_sorted=False)
bilstm_out, _ = self.bilstm(packed_embed)
bilstm_out, _ = pad_packed_sequence(bilstm_out)
```
However, I think this operation will slow down the process, since the operation of `list` is in CPU.

### Get the Output of last nodes
After observing the output in the example of *Minibatch*, we can find that it's not easy to get the final output of LSTM. If you just index by -1, you will get a lot of ZEROs, which are added by the padding. Here is a resolution I find on StackOverflow:
```python
indexes = [len(i) - 1 for i in x]  # x is the input, which is a list contains sentence embeddings in a batch
bilstm_out = bilstm_out[indexes, range(bilstm_out.shape[1]), :]
```
And there is another way to achieve so:
```python
indexes = torch.tensor([len(i) - 1 for i in x]).to(torch.long)
indexes = indexes.unsqueeze(0)
bilstm_out = bilstm_out.gather(0, indexes).squeeze(0)
```

## Initialization

A lot of literature point out that the initialization has huge impact on the performance of LSTM. For the implementation in Pytorch, there are three set of parameters for 1-layer LSTM, which are `weight_ih_l0`, `weight_hh_l0`, `bias_ih_l0` and `bias_hh_l0`. Pytorch initializes them with a Gaussian distribution, but that's usually not the best initialization. Pytorch has implemented a set of initialization methods. They could be found [here](https://pytorch.org/docs/stable/nn.init.html). For LSTM, it is recommended to use `nn.init.orthogonal_()` to initialize weights, to use `nn.init.zeros_()` to initialize all the biases except that of the forget gates, and to use `nn.init.zeros_()` to initialize the bias of forget gates. To initialize the bias of forget gates will help LSTM learn long-term dependency.

According to an [issue on Github](https://github.com/pytorch/pytorch/issues/750), `ih` and `hh` are identical, hence they should be operated in the same way.

The complete codes are as follows:
```python
lstm = nn.LSTM(input_size, hidden_size)

nn.init.orthogonal_(lstm.weight_ih_l0)
nn.init.orthogonal_(lstm.weight_hh_l0)
nn.init.orthogonal_(lstm.weight_ih_l0)
nn.init.orthogonal_(lstm.weight_hh_l0)

nn.init.zeros_(lstm.bias_ih_l0)
nn.init.ones_(lstm.bias_ih_l0[hidden_size:hidden_size*2])
nn.init.zeros_(lstm.bias_hh_l0)
nn.init.ones_(lstm.bias_hh_l0[hidden_size:hidden_size*2])
```
