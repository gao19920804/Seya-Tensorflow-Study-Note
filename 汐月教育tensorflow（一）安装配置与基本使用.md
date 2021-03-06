作者：JUDGE_MENT

邮箱：gao19920804@126.com

CSDN博客：http://blog.csdn.net/sinat_23137713

最后编辑时间：2016.12.5  V1.1

声明：

1）该资料结合官方文档及网上大牛的博客进行撰写，如有参考会在最后列出引用列表。  

2）本文仅供学术交流，非商用。如果不小心侵犯了大家的利益，还望海涵，并联系博主删除。  

3）转载请注明出处。

4）本文主要是用来记录本人初学Tensorflow时遇到的问题，特此记录下来，因此并不是所有的方法（如安装方法）都会全面介绍。希望后人看到可以引以为鉴，避免走弯路。同时毕竟水平有限，希望有饱含学识之士看到其中的问题之后，可以悉心指出，本人感激不尽。

***

<br />

<br />

<br />

# 一. 安装和卸载

**TensorFlow有5种安装方法之多,本文只介绍pip安装**

## 1. pip安装tensorflow 

注意pip安装Tensorflow目前只能安装到64位系统之下,不支持32位。Pip是Python下的一个包管理和安装软件（与easy_install齐名）

本文首先尝试在虚拟机中Ubuntu16.4, 64位，CPU模式，Python 2.7之下安装. Python2.7在16.4下已经自带. 

### i. 安装pip

`$ sudo apt-get install python-pip python-dev      # Ubuntu/Linux 64-bit`

### ii. 设置下载地址变量,别的OS下是别的网址,可以自己去git上看.

`$ export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.10.0-cp27-none-linux_x86_64.whl`

### iii. 安装tensorflow

`sudo pip install --upgrade $TF_BINARY_URL`

注意，注意，这里你很可能连不上网,有坑 . 毕竟要从google下载，所以你可能需要科学上网才能继续下载咯。

## 2. pip卸载tensorflow

`$ sudo pip uninstall tensorflow`

## 3. anaconda中使用pip安装tensorflow 

### i. 新建conda环境

**意思就是说不安装到anaconda2/lib/python2.7目录下，自己给这么庞大的程序单独安装到一个文件夹：envs/tensorflow**

注意anaconda2/envs文件夹没有权限，如果直接安装，不会撞到usr/local/anaconda2/envs，而是安装到home/wayne/.conda/envs文件夹，所以我们要先获取文件夹的超级权限。

`sudo chmod 777 -R /usr/local/anaconda2conda create -n tensorflow Python=2.7`

### ii. 激活tensorflow环境，并安装

```shell
source activate tensorflow 

export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.0.0-cp27-none-linux_x86_64.whl

sudo pip install --ignore-installed --upgrade $TF_BINARY_URL`
```

### iii. 退出tensorflow环境，完成安装 （以后每次使用tensorflow之前都需要激活一下tensorflow环境）

    source deactivate tensorflow
注意：不知道为什么我好像新建envs环境之后，经常无法安装tensorflow到envs里，反倒是安装到usr/local/lib之后再copy一份到anaconda比较方便。  

<br />

# 二. 安装位置

## 1. 普通pip安装位置

/usr/local/lib/python2.7/dist-packages/tensorflow
/usr/local/lib/python2.7/site-packages/tensorflow

## 2. anaconda下安装位置

/usr/local/anaconda2/envs/tensorflow/lib/python2.7/site-packages/tensorflow

## 3. 目录结构

![暂时无法显示图片](https://cl.ly/0t3Q0h2C3v3g/P1.jpg)

按照上一节的pip安装方法后，tensorflow整个库包被安装到了目录下；在上图中，左边为安装后位置的文件夹，右边的是从github上下载的源码包。发现配置过程中删除了部分文件（examples下的部分文件不翼而飞了）（原来源代码中还有一个model文件夹，其实和examples都是例子，在1.0.0之后models文件夹也去除了），如果想看某些例子文件，可以去源代码中examples文件夹下去看。

***(Example：用来存放网络教程中讲的源代码。Model：储存目前TF上完善的模型的代码。包括embedding（词向量）、image（图像）、rnn（循环神经网络）这些代码。都是可以直接运行的。这是针对0.8.0版本说的，现在models文件夹已经没有)***

<br />

# 三. 运行段代码，检查是否安装成功

## 1. 启动python

`$ python`

## 2. 输入代码

```python
import tensorflow as tf

hello = tf.constant('Hello, TensorFlow!')

sess = tf.Session()

print(sess.run(hello))

```

## 3. 输出结果

`Hello, TensorFlow!`

<br />

# 四. 配置GPU下安装TF

##1. 安装依赖

安装顺序： NVIDIA DIRIVER - CUDA - CUDNN （我猜）

### 1. NVIDIA DRIVER

NVIDIA DRIVER下载地址：http://www.nvidia.cn/Download/index.aspx?lang=cn

进入命令行：Ctrl-Alt+F1

关闭图形界面：`sudo service lightdm stop`

给run文件权限：`sudo chmod a+x NVIDIA-Linux-x86_64-375.20.run`

安装：

`sudo ./NVIDIA-Linux-x86_64-375.20.run –no-x-check –no-nouveau-check –no-opengl-files`

–no-x-check 安装驱动时关闭X服务

–no-nouveau-check 安装驱动时禁用nouveau

–no-opengl-files 只安装驱动文件，不安装OpenGL文件

结束：` sudo service lightdm start`

`cat /proc/driver/nvidia/version` 查看驱动



#### 遇到问题：

##### ERROR: Unable to load the kernel module ‘nvidia.ko’. 

解决方法： 

http://www.linuxidc.com/Linux/2010-06/26779.htm and http://www.itkeyword.com/doc/4293258555587595321/unable-to-load-the-kernel-module-nvidia-ko

a. 以管理员身份打开blacklist模块  `vim    /etc/modprobe.d/blacklist.conf` ；添加以下部分：

```
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist nvidiafb
blacklist rivatv
```

b. 删除安装的驱动

`sudo apt-get --purge remove nvidia-*`

c. 输入秘密代码

`sudo update-initramfs -u`

d. 重启再次安装



###2. CUDA

CUDA下载地址： https://developer.nvidia.com/cuda-downloads，选择下面Legacy Releases，CUDA Toolkit 8.0 GA2， 注意一定要下载runfile,

安装依赖：

```
sudo apt-get install freeglut3-dev 
sudo apt-get install build-essential 
sudo apt-get install libx11-dev 
sudo apt-get install libxmu-dev 
sudo apt-get install libxi-dev 
sudo apt-get install libglu1-mesa 
sudo apt-get install libglu1-mesa-dev 
```

安装cuDA：

`sudo sh cuda_8.0.44_linux.run —override`

出现Install NVIDIA Accelerated Graphics Driver?  选择n，不要安装驱动

以及在弹出xorg.conf时选择NO

添加环境变量：

`sudo gedit ~/.bashrc` 

```
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda8.0-/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```



如果卸载：按照，http://blog.csdn.net/u012436149/article/details/53163346



###3. cuDNN（是CUDA的一个增强插件）

CUDNN下载地址： https://developer.nvidia.com/rdp/cudnn-download, 选择cuDNN v5.1 for CUDA8.0

进入cuDNN存放的文件夹，输入：

`tar -zxvf cudnn-8.0-linux-x64-v5.1.tgz`

解压安装包以后在当前目录会出现cuda的文件夹，把其中的文件拷贝到系统相应目录:

```Shell
sudo cp -a cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp -a cuda/lib64/libcudnn* /usr/local/cuda/lib64/ 
```

给权限：

```
 sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

##2. 添加环境变量

```shell
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
```

这两个是开启GPU加速的关键，在import tensorflow之前一定要运行.

因此这两个东西可以放到环境变量文件中（开机之后默认运行），对于控制台可以放入./.bashrc文件中，对于pycharm应该要放入pycharm文件夹/bin/pycharm.sh文件中。

因为pycharm执行环境和.bashrc文件独立的

是这样，You might want to put your LD_LIBRARY_PATH setting in .bash_profile then it will be applied when you log in.

###1. 

在.bashrc中添加：

```shell
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
```

在/bin/pycharm.sh中，185行左右，`LD_LIBRARY_PATH="$IDE_BIN_HOME:$LD_LIBRARY_PATH" "$JAVA_BIN" \` 变成：

```shell
LD_LIBRARY_PATH='/usr/local/cuda/lib64' 

LD_LIBRARY_PATH='$LD_LIBRARY_PATH:LD_LIBRARY_PATH/usr/local/cuda/extras/CUPTI/lib64'
```

没找到，然后去软件里添加就行了。

###2. 

`source ~/.bashrc #使更改的环境变量生效`

参考博客：

http://blog.csdn.net/slade_ruan/article/details/53022768

http://blog.csdn.net/wo334499/article/details/52238986

http://blog.csdn.net/u012759136/article/details/53355781