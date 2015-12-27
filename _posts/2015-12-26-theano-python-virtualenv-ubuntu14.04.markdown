---
layout: post
title: Theano with Python 2.7.11 in a virtualenv in Ubuntu 14.04
category: programming
comments: True
---

In a previous [post][opencv2] we saw how to setup OpenCV 2.4.11 with Python 2.7.11 in a virtualenv with GPU support. Now let's set up Theano to do some fast GPU training of neural nets. Actually installing Theano is as easy as executing the following command `pip install theano` after activating the virtualenv which has numpy installed. Now we need to tell Theano to use the GPU. Add the following lines to the file .theanorc in your home folder:

{% highlight cfg %}
[cuda]
root = /usr/local/cuda
[global]
device = gpu
floatX = float32
{% endhighlight %}

The cuda section tells Theano where to find the cuda libraries and the global section tells Theano to use the first gpu (or the only gpu if you have only one) and since GPU speedups are much more significant for float32 computations we set the floatX parameter to equal float32.

That's it. You have Theano installed and ready to use. You can check it by opening the python console and typing `import theano`. It should say it is using your GPU.

####Possible issues

If when running `import theano` you get an error about shared library objects that looks something like below

{% highlight bash %}
/usr/bin/ld: /usr/local/lib/libpython2.7.a(abstract.o): relocation R_X86_64_32S against '_Py_NotImplementedStruct' can not be used when making a shared object; recompile with -fPIC
/usr/local/lib/libpython2.7.a: error adding symbols: Bad value
collect2: error: ld returned 1 exit status
error: command 'gcc' failed with exit status 1
{% endhighlight %}

then it means that your python was not compiled with the `--enable-shared` option. This option creates the libpython*.so file in your python installation directory which is required for Theano.

####CuDNN integration

CuDNN can speedup Theano's neural net training on a GPU. Follow the steps to setup CUDA outlined in a [previous post][opencv2] before executing the steps here. Add the cuda library to your LD_LIBRARY_PATH by adding the following line to your ~/.bashrc

{% highlight bash %}
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
{% endhighlight %}

Next download CuDNN from [NVIDIA developer site][cudnn]. cd to the directory where it is downloaded and untar it. Then copy over the files to `/usr/local/cuda` using the following commands:

{% highlight bash %}
tar -xvzf <cudnn_package_name>.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/
{% endhighlight %}

Now we need to let Theano know where to find it and use it. To do this add the following lines to your ~/.theanorc

{% highlight bash %}
[nvcc]
optimizer_including=cudnn
{% endhighlight %}

Now execute the following command to test if Theano is able to detect cudnn

{% highlight bash %}
python -c 'from theano.sandbox.cuda.dnn import version; print(version())'
{% endhighlight %}

If if does not complain about GPU being unavailable then you are good to go.

---

[opencv2]: /posts/opencv-python-virtualenv-ubuntu14.04/
[cudnn]: https://developer.nvidia.com/cudnn

