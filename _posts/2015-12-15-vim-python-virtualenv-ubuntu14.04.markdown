---
layout: post
title: Vim with Python 2.7.11 in a virtualenv in Ubuntu 14.04
category: programming
comments: True
---

In the last [post][opencv2] we saw how to setup OpenCV 2.4.11 with Python 2.7.11 in a virtualenv. The next step of course is to setup an editor for Python and I use Vim as my main editor for Python. Now since we installed all scientific packages (numpy, OpenCV etc.) in a virtualenv, we need to compile Vim from source with the version of Python in the virtualenv. Using `sudo apt-get install vim` to install vim causes errors when using the autocomplete package '[jedi-vim][jedivim]'. The steps are as follows:

{% highlight bash %}
# install vim dependencies
sudo apt-get remove vim vim-runtime gvim
sudo apt-get remove vim-tiny vim-common vim-gui-common
sudo apt-get install libncurses5-dev libgnome2-dev libgnomeui-dev libgtk2.0-dev \
libatk1.0-dev libbonoboui2-dev libcairo2-dev libx11-dev libxpm-dev libxt-dev \
python-dev ruby-dev git
# compile vim from source
export LD_LIBRARY_PATH=$VIRTUAL_ENV/lib/
cd ~
git clone https://github.com/vim/vim.git
cd vim
./configure --with-features=huge \
            --enable-multibyte \
            --enable-rubyinterp \
            --enable-pythoninterp \
            --with-python-config-dir=$VIRTUAL_ENV/lib/python2.7/config \
            --enable-perlinterp \
            --enable-luainterp \
            --enable-gui=gtk2 --enable-cscope --prefix=$VIRTUAL_ENV	
make VIMRUNTIMEDIR=$VIRTUAL_ENV/share/vim/vim74
make install
{% endhighlight %}

This installs vim into the bin directory of your virtualenv. So the command 'vim' is available only after you activate your virtualenv.
Next step is to configure vim with plugins to make it awesome. If you would like a quick start with decent plugins clone my [dotfiles][dotfiles] into '.dotfiles' directory in your home folder. Then execute the following commands to setup the plugins

{% highlight bash %}
ln -s ~/.dotfiles/vim/.vimrc ~/.vimrc
ln -s ~/.dotfiles/vim/.vim ~/.vim
cd ~/.vim
git submodule init
git submodule update
cd ~/.dotfiles/vim/.vim/bundle/jedi-vim
git submodule update --init
{% endhighlight %}

Now open a .py file in vim and do `import cv2` and then `cv2.` to trigger jedi-vim's autocomplete. Use `Ctrl+P` to quickly search and open for files in the directory. Use `\e` to open and close the NerdTree.

---

[opencv2]: /posts/opencv-python-virtualenv-ubuntu14.04/
[jedivim]: https://github.com/davidhalter/jedi-vim/issues/485
[dotfiles]: https://github.com/ramnath-k/dotfiles
