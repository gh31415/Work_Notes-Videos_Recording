# Work Notes 2023.6.30

1. 在Ubuntu上运行Python项目，首先安装anaconda，直接在官网安装就好了。然后装Pycharm，也是在官网上装对应的社区版，配置conda的虚拟环境。在复现vae的代码时，绘图不能呈现，出现下述问题。

![image-20230630153324974](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230630153324974.png)

**解决方法：**

① 先用`pip list`列出所有的库，然后`uninstall`删掉PYQT相关的库

② 再用`pip install PYQT==5.15.1`去安装，但此时会安装两个库，PYQT和PYQT-sip，再`pip uninstall PYQT`只保留一个QT相关库就可以正常运行画图相关代码了。

#### Ubuntu安装AirSim

* 在Unreal Engine上加入Github开发团队，这样Github会发送邮箱确认，加入开发团队后就能够访问UE的开源代码。

![image-20230701133956811](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701133956811.png)

![image-20230701134109112](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701134109112.png)

* 进入文件夹，`cd UnrealEngine-4.26`，然后运行安装程序`./Setup.sh`

![image-20230701140822584](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701140822584.png)

![image-20230701154532504](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701154532504.png)

安装中。

![image-20230701154805922](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701154805922.png)

安装成功。

* 运行工程配置文件`./GenerateProjectFiles.sh`

![image-20230701162003638](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701162003638.png)

![image-20230701171730367](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701171730367.png)

工程配置脚本运行成功。

* 编译UE4，使用`make`命令

![image-20230701172504514](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701172504514.png)

![image-20230701182208152](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701182208152.png)

编译成功。

* `git clone `对应的airsim Linux版本。`cd AirSim`运行`./setup.sh`

![image-20230701184215898](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230701184215898.png)

![image-20230703020031069](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230703020031069.png)

这个错误信息是因为Ubuntu无法找到"vulkan-utils"这个包。解决方案：

1. 更新包列表：尝试使用以下命令更新您的系统包列表。

```
sudo apt-get update
```

然后再试着安装

2. 手动添加软件源：如果更新包列表后问题仍然存在，可以试着手动添加Vulkan软件源。

先添加 key
```
wget -qO - http://packages.lunarg.com/lunarg-signing-key-pub.asc | sudo apt-key add -
```

然后添加到 apt source list
```
sudo wget -qO /etc/apt/sources.list.d/lunarg-vulkan-1.2.170-bionic.list http://packages.lunarg.com/vulkan/1.2.170/lunarg-vulkan-1.2.170-bionic.list
```

再更新一次包列表
```
sudo apt update
```
但是还是会出现问题：

```l
(base) gh@gh-virtual-machine:~/AirSim$ ./setup.sh
+ set -e
+++ dirname ./setup.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/home/gh/AirSim
+ pushd /home/gh/AirSim
+ downloadHighPolySuv=true
+ MIN_CMAKE_VERSION=3.10.0
++ uname
+ '[' Linux == Darwin ']'
+ DEBUG=false
+ [[ 0 -gt 0 ]]
++ uname
+ '[' Linux == Darwin ']'
+ sudo apt-get update
Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease               
Hit:2 https://packages.lunarg.com/vulkan/1.2.170 bionic InRelease              
Hit:4 http://gb.archive.ubuntu.com/ubuntu jammy InRelease                      
Hit:5 http://gb.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:6 http://gb.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:3 https://packagecloud.io/slacktechnologies/slack/debian jessie InRelease
Reading package lists... Done
W: https://packages.lunarg.com/vulkan/1.2.170/dists/bionic/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
W: https://packagecloud.io/slacktechnologies/slack/debian/dists/jessie/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
+ sudo apt-get -y install --no-install-recommends lsb-release rsync software-properties-common wget libvulkan1 vulkan-utils
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package vulkan-utils is a virtual package provided by:
  vulkan-tools 1.1.82.0+dfsg1-1bionic~lunarg0.1 [Not candidate version]
  vulkan-tools 1.1.82.0+dfsg1-2bionic~lunarg0.1 [Not candidate version]
  vulkan-tools 1.1.85.0-1bionic~lunarg1.1 [Not candidate version]
  vulkan-tools 1.1.92.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.92.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.92.0~rc3-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.97.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.97.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.101.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.101.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.106.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.106.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.108.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.114.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.114.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.114.0~rc3-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.121.0~rc1-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.121.0~rc2-1lunarg18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.126.0~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.126.0~rc2-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.1.130.0~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.0~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.0~rc2-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.1~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.1~rc2-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.1~rc3-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.131.2~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.135.0~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.135.0~rc2-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.141.0~rc1-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.141.0~rc2-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.141.0~rc3-1ubuntu18.04.1.1 [Not candidate version]
  vulkan-tools 1.2.148.0-1ubuntu18.04-3 [Not candidate version]
  vulkan-tools 1.2.154.0-1lunarg18.04-1 [Not candidate version]
  vulkan-tools 1.2.154.0-1lunarg18.04-2 [Not candidate version]
  vulkan-tools 1.2.154.0-1lunarg18.04-3 [Not candidate version]
  vulkan-tools 1.2.154.0-1lunarg18.04-4 [Not candidate version]
  vulkan-tools 1.2.162.0-1lunarg18.04-1 [Not candidate version]
  vulkan-tools 1.2.162.0-1lunarg18.04-2 [Not candidate version]
  vulkan-tools 1.2.162.1~rc1-1lunarg18.04-1 [Not candidate version]
  vulkan-tools 1.2.170.0~rc1-1lunarg18.04-1 [Not candidate version]
  vulkan-tools 1.2.170.0~rc2-1lunarg18.04-1 [Not candidate version]

E: Package 'vulkan-utils' has no installation candidate
```

使用`sudo apt-get install vulkan-tools `还是会出现问题。

1. 更新软件包索引列表。在终端中运行以下命令：

```shell
sudo apt-get update
```

2. 更新软件包索引列表后仍然无法找到 `vulkan-utils`，更改/更新软件源。编辑 `/etc/apt/sources.list` 文件。备份当前 sources.list 文件，然后替换为默认内容。默认的 sources.list 文件应如下所示：

```plaintext
deb http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse
```

使用文本编辑器打开并编辑此文件，如 `sudo nano /etc/apt/sources.list` ，并替换为上述内容，然后保存(ctrl+S)并关闭(ctrl+X)文件。

3. 保存更改后，再次更新您的软件包索引列表：

```shell
sudo apt-get update
```

4. 然后再次尝试安装 `vulkan-utils`：

```shell
sudo apt-get install vulkan-utils
```

