---
layout: post
title: Theano with Python 2.7.11 in a virtualenv in Ubuntu 14.04
category: programming
comments: True
---

In a previous [post][opencv2] we saw how to setup OpenCV 2.4.11 with Python 2.7.11 in a virtualenv with GPU support. Now let's set up Theano to do some fast GPU training of neural nets. Actually installing Theano is as easy as executing the following command `pip install theano` after activating the virtualenv which has numpy installed. Now we need to tell Theano to use the GPU. Add the following lines to the file .theanorc in your home folder:

``shell
[cuda]
root = /usr/local/cuda

[global]
device = gpu
floatX = float32
```

The cuda section tells Theano where to find the cuda libraries and the global section tells Theano to use the first gpu (or the only gpu if you have only one) and since GPU speedups are much more significant for float32 computations we set the floatX parameter to equal float32.

That's it. You have Theano installed and ready to use.

---

[opencv2]: /posts/opencv-python-virtualenv-ubuntu14.04/

