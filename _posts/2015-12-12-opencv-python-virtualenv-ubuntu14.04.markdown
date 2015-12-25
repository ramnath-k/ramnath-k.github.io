---
layout: post
title: Installing OpenCV 2.4.11 with Python 2.7.11 in a virtualenv in Ubuntu 14.04
category: computer vision
comments: True
---

I spent a couple of days last week setting up my brand new [Lenovo Y50-70][laptop] laptop. I thought I will save time for others wanting to setup a computer vision environment by sharing the steps. 

First let me detail the quirks needed to dual boot Ubuntu with Windows 10. Disable only the fast-startup option under power settings in Windows 10. No need to disable UEFI or Secureboot. To get the ubuntu amd64 installer to stop hanging (if it hangs for you), edit the linux options in the grub-like installer purple screen and remove `quiet splash` and add `nomodeset`. In addition, skip the wifi addition so that the install completes as quick as possible. Then enable wifi. If you experience very choppy wifi, then disable 802.11n in your router. Yes I know the specs for the laptop say it supports 802.11n but sadly it did not work for me. Finally change the settings in Ubuntu 'Software & Updates' app to point to the 'Main Server'. It turned out that the Indian Mirror which it used based on location did not have the recent updates to the nvidia-drivers which prevented me from enabling nvidia-drivers until I made the switch. Then install the drivers and restricted addons for media using the following commands:

```shell
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get install -y ubuntu-restricted-addons
sudo apt-get install -y ubuntu-restricted-extras
sudo apt-get install -y nvidia-352
```

Next install basic packages for compiling Python and computer vision libraries using the following commands:

```shell
# for compiling python from source
sudo apt-get install -y build-essential zlib1g-dev gcc-multilib g++-multilib \
libffi-dev libffi6 libffi6-dbg python-crypto python-mox3 python-pil python-ply \
libssl-dev zlib1g-dev libbz2-dev libexpat1-dev libbluetooth-dev libgdbm-dev \
dpkg-dev quilt autotools-dev libreadline-dev libtinfo-dev libncursesw5-dev \
tk-dev blt-dev libssl-dev zlib1g-dev libbz2-dev libexpat1-dev libbluetooth-dev \
libsqlite3-dev libgpm2 mime-support netbase net-tools bzip2 libpng12-dev
# for numpy
sudo apt-get install -y libatlas-base-dev liblapack-dev gfortran cython
sudo apt-get install -y python-pip
# for scikit-image and opencv
sudo apt-get install -y libjpeg-dev libtiff5-dev
# opencv dependencies https://help.ubuntu.com/community/OpenCV
sudo apt-get remove ffmpeg x264 libx264-dev
sudo apt-get install -y checkinstall cmake pkg-config yasm libjasper-dev \
libavcodec-dev libavformat-dev libswscale-dev libdc1394-22-dev libxine2-dev \
libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev libv4l-dev libtbb-dev \
libqt4-dev libgtk2.0-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev \
libopencore-amrwb-dev libtheora-dev libvorbis-dev libxvidcore-dev x264 unzip v4l-utils
```

Then download the latest Python 2.7 from [python.org][python2] and cd to download directory and compile it using the following commands:

```shell
tar -xf Python-2.7.11.tar.xz
cd Python-2.7.11
./configure --prefix /usr/local/lib/python2.7.11 --enable-ipv6 --enable-unicode=ucs4 --enable-shared
make
sudo make install
```

Now we can install the setuptools, pip and virtualenv packages for this latest python.

```shell
wget https://bootstrap.pypa.io/ez_setup.py
sudo /usr/local/lib/python2.7.11/bin/python ez_setup.py
sudo /usr/local/lib/python2.7.11/bin/easy_install pip
sudo /usr/local/lib/python2.7.11/bin/pip install virtualenv virtualenvwrapper
echo 'export PATH=$PATH:/usr/local/lib/python2.7.11/bin/' >> ~/.bashrc
echo 'source /usr/local/lib/python2.7.11/bin/virtualenvwrapper.sh' >> ~/.bashrc
source ~/.bashrc
```

Now we are ready to create a virtualenv from the latest version of Python and install packages in it using the following commands:

```shell
mkvirtualenv opencv2
workon opencv2
pip install numpy 
pip install scipy 
pip install matplotlib
# verify that a simple snippet like plt.plot([1,2,3]) produces a plot when executed as a python script
pip install scikit-learn
pip install -U scikit-image
```

Finally it is time to compile Opencv2.4.11 with Python bindings into the virtualenv. Download opencv-2.4.11.zip from [opencv.org][opencv2] and cd to the folder where it is saved.

```shell
unzip opencv-2.4.11.zip
cd opencv-2.4.11
mkdir release
cd release
cmake -D CMAKE_INSTALL_PREFIX=$VIRTUAL_ENV/local/ \
-D PYTHON_EXECUTABLE=$VIRTUAL_ENV/bin/python \
-D PYTHON_LIBRARY=/usr/local/lib/python2.7.11/lib/libpython2.7.so \
-D PYTHON_PACKAGES_PATH=$VIRTUAL_ENV/lib/python2.7/site-packages \
-D BUILD_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON \
_D INSTALL_C_EXAMPLES=ON -D WITH_V4L=ON \
-D CUDA_GENERATION=Auto -D WITH_CUBLAS=1 \
-D ENABLE_FAST_MATH=1 -D CUDA_FAST_MATH=1 \
-D WITH_OPENGL=ON -D WITH_QT=ON -D WITH_TBB=ON ..
make -j7
make install
```

Verify that OpenCV2 is successfully installed by launching `python` from the virtualenv and doing `import cv2` followed by `cv2.__version__`. 

---

[laptop]: http://shopap.lenovo.com/in/en/laptops/lenovo/y-series/y50/
[python2]: https://www.python.org/downloads/
[opencv2]: http://opencv.org/downloads.html
