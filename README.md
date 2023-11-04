# 前言

# 一、准备工作
树莓派4（~~略有磨损~~ ）
显示器（Mico HDIM）
PS：这里我们强烈建议您准备显示器，在某些突发情况（局域网突然不稳定）会导致VNC断联，假死，无响应等
## PC端软件
**putty**
![SSH](https://img-blog.csdnimg.cn/20201127204810741.png)
**VNC Viewer**（建议使用远程桌面软件方便后期编译查看情况）
![VNC](https://img-blog.csdnimg.cn/20201127205301223.png)

FileZila（方便与树莓派传输文件）
![FileZila](https://img-blog.csdnimg.cn/20201127205140578.png)

# 二、搭建树莓派环境

## 拓展TF内存卡
<font color=#999AAA >***Warning：搭建OpenCV环境，树莓派至少16GTF卡，需要拓展TF的存储空间至整个TF卡空间***

查看当前树莓派空间分配
```powershell
df -h #查看当前树莓派空间分配
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130184625406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
继续输入

```powershell
sudo raspi-config
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130184905693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130184937606.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
**确定**—**finish**保存回车ENTER（树莓派会重新启动）
```powershell
sudo reboot #也可以手动重启
```
Tips：如果出现以下情况：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130185733335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
<font color=#999AAA >此当前不支持您的分区布局工具，您可能正在使用NOOBS，在这种情况下，您的根文件系统已经扩展。
### Tips：换源（python软件源）
之前的文章虽然已经 换源（清华）但是在我们搭建环境过程中为了能更稳定 ~~不白给~~ 我们再次换源，但这次只换Python软件源即可

```powershell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple #清华源
pip install pip -U
sudo reboot #重启树莓派
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130175811577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
单独换源结束，现在进入正题：
<font color=#999AAA >在此我将安装Python3的OpenCV3环境

## 修改树莓默认Python版本
打开我们的终端输入

```powershell
python
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130211913214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
我发现默认版本是**Python2.7.16**（输入**Ctrl + Z**退出python环境）
继续输入

```powershell
python3
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130212122499.png)
**python3版本为3.7.3****，所以修改默认版本（python）为python3.7
*输入*`sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100`
*Keep going*`sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130212352822.png)
在此输入`python`发现修改**默认版本**成功
## 扩展交互空间
<font color=#999AAA >安装OpenCV3编译部分是最头痛的，Up主自己在编译中很多次都会卡在40%左右假死挂起然后报错，部分报错原因可能是由于交互空间不足导致的，在之前树莓派3B上尝试了很多次都假死挂起（内存太小），在此我们先增大Swap交互空间
在终端中输入

```powershell
free -m #查看当前内存
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130192212683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
继续输入
```powershell
sudo nano /etc/dphys-swapfile
```
将**CONF_SWAPSIZE =** 改为**1024**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130193048986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
**Ctrl + O**再**Enter**再**Ctrl + X**保存退出
继续输入

```powershell
sudo /etc/init.d/dphys-swapfile restart
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130193621155.png)
OK增大Swap交互空间成功
<font color=#999AAA >本次使用的树莓派4（2GB）方便起见我改数值为1024+512，大家根据自身使用配置情况可以进一步修改
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020113019404286.png)
# “进击”的工具包们
你需要安装下载以下的工具包们:libjpeg-dev libatlas-base-dev libjpeg-dev libtiff5-dev libpng12-dev libqtgui4 libqt4-test libjasper-dev
不着急，一个个来
## OpenCV编译工具
```powershell
sudo apt install build-essential
sudo apt install cmake
sudo apt install git
sudo apt install pkg-config
```
## OpenCV图像包

```powershell
sudo apt-get install libtiff5-dev #tif格式图像工具包
sudo apt-get install libjasper-dev #JPEG-2000图像工具包
sudo apt-get install libjpeg8-dev #jpeg格式图像工具包
sudo apt-get install libpng12-dev #png图像工具包
```
## OpenCV视频I/O包

```powershell
sudo apt-get install libavcodec-dev
sudo apt-get install libv4l-dev
sudo apt-get install libavformat-dev
sudo apt-get install libswscale-dev
```
## OpenCV-gtk2.0

```powershell
sudo apt-get install libgtk2.0-dev #安装gtk2.0
```
## 优化函数包

```powershell
sudo apt-get install libatlas-base-dev gfortran #安装优化函数包
```
**以上依赖环境包安装好以后就开始准备配置Cmake参数**
<font color=#999AAA >本次安装OpenCV3.4.0版，是从GitHub上Git clone的方式下载
# 编译Cmake部分（受苦的开始）

**新建目录从 Github 克隆 OpenCV 和 OpenCV contrib 库**

```powershell
mkdir ~/opencv_build #在opencv里新建build文件夹
cd ~/opencv_build
```
新建build的文件夹存放cmake编译时产生的临时数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204214640993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MDY3OTE2Mw==,size_16,color_FFFFFF,t_70)
之后**git clone**

```powershell
wget -O opencv-4.0.0.zip https://github.com/Itseez/opencv/archive/4.0.0.zip
#下载opencv 4.0.0包
```
<font color=#999AAA >这里下载很慢很慢，毕竟是国外网站，而且下载一般报错很正常（~~换源没用~~ ）耐心等待http回应
![请添加图片描述](https://img-blog.csdnimg.cn/8615221311604effa02944b82edceaa4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
个人建议会点魔法的尽量使用魔法
![请添加图片描述](https://img-blog.csdnimg.cn/73d86970211d4dd0be776bbc5e8e55ef.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
最后安装下载完是这样子
![请添加图片描述](https://img-blog.csdnimg.cn/2de9f1a7d5264b7d8496c4bf1f03654e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)

接着需要下载opencv-contrib包文件
```bash
wget -O opencv_contrib-4.0.0.zip https://github.com/Itseez/opencv_contrib/archive/4.0.0.zip #下载opencv包
```
![请添加图片描述](https://img-blog.csdnimg.cn/13b5b64d8b0e48a0b39287063ae0d035.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
最后下载完成.........
这里网络不好的小伙伴我提供一个完整的资源下载（包含opencv4.0.0、opencv_contrib-4.0.0）[OpenCV4.0.0安装环境](https://download.csdn.net/download/weixin_50679163/21798493)
![请添加图片描述](https://img-blog.csdnimg.cn/cf714eeb47ec426ab4f7dd96ffd4f4dc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
```bash
unzip opencv-4.0.0.zip
unzip opencv_contrib-4.0.0.zip
#解压文件
```
![请添加图片描述](https://img-blog.csdnimg.cn/46e0b652c8fc4924b2ebd20be7e698e1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
## 设置编译参数
在文件解压完毕后，保证文件路径没有问题：向我这样的结构：**/opencv4.0.0/build/**以下的操作均需要在**build**的这个文件夹进行：
接下来准备设置编译参数，编译的语句安全起见要加 **sudo**

```bash
sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/opencv/opencv_contrib-4.0.0/modules \
    	#根据自己contrib中modules文件夹的地址进行配置
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D INSTALL_CXX_EXAMPLES=ON \
	-D OPENCV_GENERATE_PKGCONFIG=ON \
    -D BUILD_EXAMPLES=ON ..
```
这里是编译的代码的详细
```bash
CMAKE_BUILD_TYPE #编译方式
CMAKE_INSTALL_PREFIX #安装目录
OPENCV_EXTRA_MODULES_PATH #加载额外模块
INSTALL_PYTHON_EXAMPLES #安装官方python例程
BUILD_EXAMPLES #编译例程
OPENCV_GENERATE_PKGCONFIG : #配置pkg-config , OpenCV 4 默认下生成”opencv4.pc“
-D INSTALL_C_EXAMPLES=ON	#如果需要C语言的编程案例在末尾加上
```
开始编译：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c47e0e7d343244e08bd089cb6def7ca0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
设置编译参数完成看到结尾像这样，没有报错且都为**done**的时候，编译部分结束
![在这里插入图片描述](https://img-blog.csdnimg.cn/4f9a62d28e084b9da070a3afcf21360e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
到这里，在成功编译的情况下建议把编译的文件夹进行一个备份：（**/build**）

```bash
cd ..
cp -r build ./buildres  #在当前目录备份build为buildres文件夹
```
### 可能遇到的第一个坑
**但是吧......在这个步骤中，可能会出现一个错误：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/e7fbb5cf82d14e63abe582beef628b88.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
这种情况是文件编译出错，我自己第一次编译也是一直失败
可能出现的原因：
1.下载的opencv以及opencv-contrib文件不完整，尤其是git clone下的文件，可能文件丢失
**建议重新下载**
2.在编译的时候，没有添加“**sudo**”，在Linux中，sudo是高权限的指令
**尝试添加sudo，并且删除原编译的build文件夹中的所有文件后重新编译**
## 预备，编译
![请添加图片描述](https://img-blog.csdnimg.cn/876ff2c2a27241da93c0e4b1ace5a930.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
![请添加图片描述](https://img-blog.csdnimg.cn/284655ec9c434840b6eee6d9856a8c64.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
一次编译到这里是真的NB！！！（反正我安装了差不多4次的opencv，没有一次能一次顺利的编译完成）
![请添加图片描述](https://img-blog.csdnimg.cn/8d81a5f3d9f943049f518201b5a6d171.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
正常来说，脸白一点，一次完整的编译成功！！！雀食欧皇
（一次编译成功的重要原因是你的opencv和opencv-contrib文件很完整，也就是你连接GitHub的网速好，或者下载的哪个文件完整度较高）
不然.....基本就会在编译中，**会报错缺失文件**
## 那就来盘点一下各种错误重要
### 空间不足
换32GB吧，16GB可能少点的话编译雀食很危险，32GB最保险
**重要提示：**内存卡一定要看看是不是已经扩充到整个磁盘：
![请添加图片描述](https://img-blog.csdnimg.cn/2c4c8d4ed6bd40dabac9fe736677e18c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3b69e8cb8d4940d5acb5bfee99bd8fec.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
这是一张32GB的卡，在第一次编译因为没有扩充到整个磁盘，导致空间不足
扩充磁盘的方法：文章开头有提到
![在这里插入图片描述](https://img-blog.csdnimg.cn/4a2ebe8c90974d19bfd3f0c63ef9a917.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_18,color_FFFFFF,t_70,g_se,x_16)
### 缺失文件类型的错误
这个报错是很常见的了，原因前文也提到了，就是opencv和opencv-contrib两个文件不完整
具体表现在：
缺失以下的文件：
![请添加图片描述](https://img-blog.csdnimg.cn/4ea037cf84fc4fe69d08e0ccfca5c545.png)
那就缺什么下载什么，这里提供以上文件的完整包——[下载](https://download.csdn.net/download/weixin_50679163/21883430)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c3fc88a0d673471db21ba09e554d2abd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
和上图差不多的报错，将缺失的文件放到
**opencv_contrib/modules/xfeatures2d/src/**（记得是**opencv-contrib**这个文件夹里）

### 修改库文件
这是我心态崩的一次安装opencv了
**在编译到99%报错了**直接原地爆炸
![请添加图片描述](https://img-blog.csdnimg.cn/da1c487e55e24d06af7498777a328b13.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
注意看这里：
![在这里插入图片描述](https://img-blog.csdnimg.cn/bc671c86fc064dc89f7344f8a9e2a2ce.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
意思是我们缺失这个文件“**test_detectors_regression.impl.hpp**"
解决这个问题首先要看看我们是否有这个文件
在终端中输入

```bash
find -name test_detectors_regression.impl.hpp
```
或者去这个路径

```bash
/Users/bigboss/Documents/CodeHouse/OpenCV4.0.0/OpenCV4.0.0/opencv-4.0.0/modules/features2d/test/test_descriptors_regression.impl.hpp
```
看看这个文件是否存在
若有这个文件则打开opencv/CmakeLists.txt文件中，插入

```cpp
include_directories("modules")
```
至于插入的位置，位置在构建前就行，举例
![在这里插入图片描述](https://img-blog.csdnimg.cn/2d67438311a14966a7b2038e917e1b01.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
## 最后的编译
执行

```bash
sudo make -j4 2>&1 | tee make.log
```
![请添加图片描述](https://img-blog.csdnimg.cn/89761dbd44b74b2d8125ef974ccce02e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
![请添加图片描述](https://img-blog.csdnimg.cn/d867a6bba06545f69fae5eb18e7f267c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
通过！！！
接着执行

```bash
sudo make install
```
![请添加图片描述](https://img-blog.csdnimg.cn/3344f14aaae54175b46ae353449d7a36.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
**finally 终于成功安装了！！！！**

# 配置OpenCV到Python环境变量
在整个安装完OpenCV环境下，打开终端进行测试
输入 ： `Python` 进入Python的运行环境
输入`import cv2` 如果没有错误的话说明可以正常运行opencv的环境了，提示找不到库时候，说明需要将opencv的环境配置到Python中

退出Python环境，在终端输入

```bash
cd /usr/local/python/cv2
```
进入到Python cv2的文件下，继续输入`ls` 查看当前的文件夹下的文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/b834fafb9de54fbbbf13dd981777fa5c.png)
有一个文件夹是Python-3.7 ，进入这个文件夹 **/usr/local/python/cv2/python-3.7**（根据自己的文件版本进行调整）
![在这里插入图片描述](https://img-blog.csdnimg.cn/80404b9c1a7b4a969111f841ff52fde8.png)
这个文件夹里面有一个文件 **cv2.cpython-37m-arm-linux-gnueabihf.so**将这个文件复制到这个路径下**/usr/local/lib/python3.7/dist-packages**

```bash
sudo cp cv2.cpython-37m-arm-linux-gnueabihf.so /usr/local/lib/python3.7/dist-packages
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e2df7c1080546cb9929eacbcdb2695e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
## 更新环境变量

```bash
sudo ldconfig
```
打开bash.bashrc配置文件

```bash
sudo nano /etc/bash.bashrc
```
进去后，在末尾加入：

```bash
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig       
export PKG_CONFIG_PATH
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/466bb0264a894d4bbbb3ad62e0eb7a2c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
重启树莓派`sudo reboot now` 结束！！！
最后检测一下
![在这里插入图片描述](https://img-blog.csdnimg.cn/3c24c56531c44e87b92f63cae1288992.png)
测试代码

```python
import cv2
import numpy as np

img = cv2.imread（“你测试的图片路径”）
cv2.namedWindows（“TestImage”）
cv2.imshow("TestImage",img)
cv2.waitKey(0)
cv2.destoryAllWindow()

```
测试成功！！！
![在这里插入图片描述](https://img-blog.csdnimg.cn/51a8c95ebbc6420f9d231f43842077c2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQklHQk9TU3lpZmk=,size_20,color_FFFFFF,t_70,g_se,x_16)
