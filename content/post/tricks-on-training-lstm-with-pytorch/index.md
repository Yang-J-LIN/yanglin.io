+++
title = "Tricks on training LSTM with Pytorch"
subtitle = "Especially for varibale-length long sequences"

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

# Dealing with varibale-length sequence
Variable-length sequence can sometimes be very annoying, especially when we want to apply minibatch to accelerate the training. Fortunately, PyTorch has prepared a bunch of tools to facilitate it. You can find two useful functions in `torch.nn.utils.rnn` -- `pack_sequence` and `pad_packed_sequence()`. The former takes a list as the input, which contains the sequence you want to feed the LSTM, and it returns a PackedSequence (Refer to the docs of `torch.nn.utils.rnn` for details). The latter takes a PackedSequence as the input, and returns a padded tensor.

Here are the prototypes of these two functions:
```python
pack_sequence(sequences, enforce_sorted=True)
```
```python
pad_packed_sequence(sequence, batch_first=False, padding_value=0.0, total_length=None)
```
