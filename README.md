1. 如果安装的中文版，则将Ubuntu主文件夹里的中文文件夹名称改成英文

   首先修改现有主文件夹下各文件夹名称为：

   `desktop` `documents` `downloads` `music` `pictures` `public` `templates` `videos`

   然后编辑配置文件：

   ```
   vim ~/.config/user-dirs.dirs
   ```

   把文件夹指向改掉：

   ```
   XDG_DESKTOP_DIR="$HOME/desktop"
   XDG_DOWNLOAD_DIR="$HOME/downloads"
   XDG_TEMPLATES_DIR="$HOME/templates"
   XDG_PUBLICSHARE_DIR="$HOME/public"
   XDG_DOCUMENTS_DIR="$HOME/documents"
   XDG_MUSIC_DIR="$HOME/music"
   XDG_PICTURES_DIR="$HOME/pictures"
   XDG_VIDEOS_DIR="$HOME/videos"
   ```

2. 修改镜像源

   备份:

   ```
   sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
   ```

   将`/etc/apt/sources.list`修改为清华大学的Ubuntu 18.04的镜像源:

   ```
   # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
   
   # 预发布软件源，不建议启用
   # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
   ```

3. 安装Vim

   ```
   sudo apt install vim
   ```

4. 安装Typora，Markdown编辑器

   ```
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
   sudo add-apt-repository 'deb https://typora.io/linux ./'
   sudo apt update
   sudo apt install typora
   ```

5. 安装NVIDIA（GTX 1060）驱动以及Cuda9.0、Cuda7.5

   1. 先用系统的gcc-7安装驱动，驱动安装步骤和[链接](https://blog.csdn.net/StrugglePeach/article/details/77940490)里的类似，安装后先别安装Cuda。

   2. 给GCC、G++降级，安装`GCC-4.8`和`G++-4.8`：

      ```
      sudo apt install gcc-4.8
      sudo apt install g++-4.8
      ```

      装完后进入到`/usr/bin`目录下，命令可查看GCC和G++优先级：

      ```
      ls -l gcc*
      ls -l g++* 
      ```

      备份并且重新链接：

      ```
      sudo mv gcc gcc.backup
      sudo ln -s gcc-4.8 gcc
      sudo mv g++ g++.backup 
      sudo ln -s g++-4.8 g++
      ```

   3. 安装Cuda9.0和对应版本cuDNN

      逛完下载Cuda9.0，下载Cuda9.0的时候下载`runfile (local)`（.run文件）。安装Cuda9.0的时候，有个是问是否安装NVIDIA驱动的，前面已经安装过了，所以选择`no`，如果要将当前安装的cuda9.0设置为当前环境的版本，则创建软链接（symbolic link）选项可选`yes`，否则选`no`。

      赋予可执行权限：

      ```
      chmod +x cuda_9.0.176_384.81_linux.run
      chmod +x cuda_9.0.176.1_linux.run
      chmod +x cuda_9.0.176.2_linux.run
      chmod +x cuda_9.0.176.3_linux.run
      chmod +x cuda_9.0.176.4_linux.run
      ```

      安装Cuda9.0：

      ```
      sudo ./cuda_9.0.176_384.81_linux.run
      sudo ./cuda_9.0.176.1_linux.run
      sudo ./cuda_9.0.176.2_linux.run
      sudo ./cuda_9.0.176.3_linux.run
      sudo ./cuda_9.0.176.4_linux.run
      ```

      Cuda9.0对应的cuDNN有多个版本，根据需要选择版本，下载cuDNN的时候选择`Library for Linux`文件，下载后解压：

      ```
      tar -xzvf cudnn-9.0-linux-x64-v7.3.1.20.tgz
      ```

      复制到`cuda-9.0`文件夹(`cuda`是安装的cuda的软链接，避免以后安装多个cuda时cudnn混乱，不要复制到`cuda`文件夹）：

      ```
      sudo cp cuda/include/cudnn.h /usr/local/cuda-9.0/include
      sudo cp cuda/lib64/libcudnn* /usr/local/cuda-9.0/lib64
      sudo chmod a+r /usr/local/cuda-9.0/include/cudnn.h /usr/local/cuda-9.0/lib64/libcudnn*
      ```

      设置环境变量：

      ```
      sudo vim ~/.bashrc
      ```

      末尾添加：

      ```
      export PATH=/usr/local/cuda/bin${PATH:+:$PATH}}
      export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
      ```

      使环境变量设置立即生效：

      ```
      source ~/.bashrc
      ```

   4. 安装Cuda8.0和对应版本cuDNN

      正常步骤安装Cuda8.0会失败，原因是缺乏安装工具。故安装前需把`cuda_8.0.61_375.26_linux.run`解压，然后复制`InstallUtils.pm`到`/usr/lib/x86_64-linux-gnu/perl-base/`并且设置环境：

      ```
      sh ./cuda_8.0.61_375.26_linux.run --tar mxvf
      sudo cp InstallUtils.pm /usr/lib/x86_64-linux-gnu/perl-base/
      export $PERL5LIB
      ```

      之后安装步骤同上。注意软链接（symbolic link）选项，cuDNN复制到`cuda-8.0`里。

      查看当前系统Cuda环境：

      ```
      nvcc -V
      ```

      切换系统Cuda环境到`cuda-8.0`：

      ```
      sudo rm -rf /usr/local/cuda
      sudo ln -s /usr/local/cuda-8.0 /usr/local/cuda
      ```

   5. 安装Cuda7.5和对应版本cuDNN

      正常步骤安装Cuda7.5会失败，原因是缺乏安装工具。故安装前需把`cuda_7.5.18_linux.run`解压，然后复制`InstallUtils.pm`到`/usr/lib/x86_64-linux-gnu/perl-base/`并且设置环境：

      ```
      sh ./cuda_7.5.18_linux.run --tar mxvf
      sudo cp InstallUtils.pm /usr/lib/x86_64-linux-gnu/perl-base/
      export $PERL5LIB
      ```

      之后安装步骤同上。注意软链接（symbolic link）选项，cuDNN复制到`cuda-7.5`里。

      查看当前系统Cuda环境：

      ```
      nvcc -V
      ```

      切换系统Cuda环境到`cuda-7.5`：

      ```
      sudo rm -rf /usr/local/cuda
      sudo ln -s /usr/local/cuda-7.5 /usr/local/cuda
      ```

6. 安装Anaconda3

   从[官网](https://docs.anaconda.com/anaconda/install/linux)下载，安装：

   ```
   bash Anaconda3-5.2.0-Linux-x86_64.sh
   ```

   换成清华大学的镜像源：

   ```
   conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
   conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
   conda config --set show_channel_urls yes
   ```

7. 安装Pytorch

   在官网选对应版本Pytorch后会有安装命令，Linux+conda+3.6+9.1的命令是：

   ```
   conda install pytorch torchvision cuda91 -c pytorch
   ```

8. 安装MATLAB R2016b

   按照[链接](https://blog.csdn.net/zyqblog/article/details/78938760)安装。

9. 安装MatConvNet

   从[链接](http://www.vlfeat.org/matconvnet/)下载`matconvnet-1.0-beta25.tar`并解压，按照[链接](https://blog.csdn.net/breeze5428/article/details/42242761)安装。

10. 安装工具gdebi

    ```
    sudo apt install gdebi
    ```

11. 安装搜狗输入法

    1. 安装fcitx

       ```
       sudo apt install fcitx
       ```

    2. 安装搜狗输入法

       从[链接](http://cdn2.ime.sogou.com/dl/index/1524572264/sogoupinyin_2.2.0.0108_amd64.deb)下载搜狗输入法64位，之后用gdebi安装,重启系统才生效。

       ```
       sudo gdebi sogoupinyin_2.2.0.0108_amd64.deb
       ```

12. 安装Chrome

    ```
    sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/
    wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
    sudo apt update
    sudo apt install google-chrome-stable
    ```

13. 安装electron-ssr

    1. 安装Python2

       需要系统可正常执行`shadowsocksr`的Python版，因此需要安装Python2：

       ```
       sudo apt install python-minimal
       ```

    2. 安装electron-ssr

       从[链接](https://github.com/erguotou520/electron-ssr)下载electron-ssr，安装electron-ssr:

       ```
       sudo gdebi electron-ssr_0.2.4_amd64.deb
       ```

       安装后即可打开配置ssr账号。

    3. 配置PAC模式

       安装后在系统`设置`-->`网络`-->`网络代理` ，设置为`手动`：

       | HTTP代理**    |                               | **0**    |
       | ------------- | ----------------------------- | -------- |
       | **HTTPS代理** |                               | **0**    |
       | **FTP代理**   |                               | **0**    |
       | **Socks主机** | **127.0.0.1**                 | **1080** |
       | **忽略主机**  | **localhost,127.0.0.0/8,::1** |          |

       还是全局翻墙。下载[SwitchOmega](https://github.com/FelisCatus/SwitchyOmega/releases)。Chrome扩展程序页面打开开发者模式，重启Chrome，把`SwitchyOmega_Chromium.crx`文件拖到浏览器扩展程序页面安装，安装后关闭开发者模式。之后安装[链接](https://www.switchyomega.com/settings/)配置SwitchyOmega。

    4. 终端和Git翻墙

       在终端查看IP地址和位置信息：

       ```
       curl ip.gs
       
       ```

       终端设置代理：

       安装`polipo`：

       ```
       sudo apt install polipo
       
       ```

       修改`polipo`配置文件：

       ```
       sudo vim /etc/polipo/config
       
       ```

       末尾添加：

       ```
       socksParentProxy = "localhost:1080"
       socksProxyType = socks5
       logLevel=4
       
       ```

       关闭和启动polipo：

       ```
       sudo service polipo stop
       sudo service polipo start
       
       ```

       添加到配置文件：

       ```
       vim ~/.bashrc
       
       ```

       末尾添加：

       ```
       export http_proxy=http://localhost:8123
       export https_proxy=https://localhost:8123
       
       ```

       使配置生效：

       ```
       source ~/.bashrc
       
       ```

       Git设置代理：

       ```
       git config --global http.proxy "socks5://127.0.0.1:1080"
       git config --global https.proxy "socks5://127.0.0.1:1080"
       ```

14. 安装gnome-tweak，优化

    ```
    sudo apt install gnome-tweak-tool
    ```

15. 安装Gnome Shell Extensions

    用Firefox浏览器（Chrome浏览器不会提示安装）打开[链接](https://extensions.gnome.org)，按照提示安装浏览器插件GNOME Shell integration，之后安装chrome-gnome-shell：

    ```
    sudo apt install chrome-gnome-shell
    
    ```

    之后就可以安装想安装的Gnome Shell Extensions:

    [AlternateTab](https://extensions.gnome.org/extension/15/alternatetab/)

    [Clipboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/)

    [Dash to Dock](https://extensions.gnome.org/extension/307/dash-to-dock/)

    [Drop Down Terminal](https://extensions.gnome.org/extension/442/drop-down-terminal/)

    [Dynamic Top Bar](https://extensions.gnome.org/extension/885/dynamic-top-bar/)

    [Show Applications](https://extensions.gnome.org/extension/812/show-applications/) 

    [Show Desktop Button](https://extensions.gnome.org/extension/1194/show-desktop-button/)

    [TopIcons Plus](https://extensions.gnome.org/extension/1031/topicons/)

    [User Themes](https://extensions.gnome.org/extension/19/user-themes/)

16. 安装Gnome Pie，快捷启动器

    ```
    sudo add-apt-repository ppa:simonschneegans/testing
    sudo apt update
    sudo apt install gnome-pie
    ```

17. 安装Okular，PDF阅读器

    ```
    sudo apt install okular
    ```

    配置Google 翻译：按`Ctrl + 4`（切换成文本选择工具），选择一个字或词右击，选择网页配置网页快捷方式，新建`Google 翻译`，速搜网址`https://translate.google.com/#auto/zh-CN/\{@}`，搜索关键字随便填`t`即可。之后拉大设置框，把`Google 翻译`设为首选，取消其他不需要的选项。重启才生效。

18. 安装GoldDict词典

    ```
    sudo apt install goldendict
    ```

    安装后导入下载好的词典：`美国传统英汉双解学习词典.mdx`，`新牛津英汉双解大词典.mdx`。

19. 安装有道词典

    获取[链接](https://github.com/yomun/youdaodict_5.5)里提供的版本：

    ```
    wget https://github.com/yomun/youdaodict_5.5/raw/master/youdao-dict_1.1.1-0~ubuntu_amd64.deb
    ```

    直接安装。

20. 安装WPS

    按照[链接](https://www.jianshu.com/p/3407c18e1337)里安装。

21. 安装PyCharm

    用教育邮箱在[官网](https://www.jetbrains.com/student/)申请学生礼包，下载、解压，按照`Install-Linux-tar.txt`安装即可，在设置里勾选`Create desktop entry`。

22. 安装Git

    ```
    sudo apt install git
    
    ```

23. 安装Visual Studio Code

    从[官网](https://code.visualstudio.com/)下载安装即可。

24. 安装Sublime Text 3

    按照[链接](https://www.sublimetext.com/docs/3/linux_repositories.html)安装，安装后不能输入中文解决办法：

    ```
    git clone https://github.com/lyfeyaj/sublime-text-imfix.git
    cd sublime-text-imfix && ./sublime-imfix
    
    ```

25. 安装网易云音乐

    按照[链接](https://blog.csdn.net/weixin_40993826/article/details/80343032)的方法安装。

    安装后桌面歌词会乱码，按[链接](https://reuixiy.github.io/technology/computer/system-software/2017/09/23/lyrics-messy-code-after-dist-upgrade.html)安装Qt5.8和修改`netease-cloud-music.desktop.desktop`文件：

    ```
    wget http://download.qt.io/archive/qt/5.8/5.8.0/qt-opensource-linux-x64-5.8.0.run
    chmod +x qt-opensource-linux-x64-5.8.0.run
    sudo ./qt-opensource-linux-x64-5.8.0.run
    
    ```

    然后弹出安装界面，一直默认，最后：

    ```
    vim /usr/share/applications/netease-cloud-music.desktop
    
    ```

    将`Exec`行改为：

    ```
    Exec=env LD_LIBRARY_PATH=/opt/Qt5.8.0/5.8/gcc_64/lib netease-cloud-music %U --no-sandbox
    
    ```

26. 安装Teamviewer

    [官网](https://www.teamviewer.com/zhcn/download/linux/)下载安装。

27. 安装QQ、WeChat、Tim

    按照[链接](https://github.com/wszqkzqk/deepin-wine-ubuntu)安装。

28. 安装VLC视频播放器

    从应用商店`Ubuntu 软件`搜索`VLC`下载安装。

29. 安装Persepolis，下载工具

    ```
    sudo add-apt-repository ppa:persepolis/ppa
    sudo apt update
    sudo apt install persepolis
    
    ```

    Chrome安装扩展程序：`Persepolis Download Manager Integration`。

30. 安装curl

    ```
    sudo apt install curl
    ```

31. 系统备份

    参照[链接](https://blog.csdn.net/qq_35523593/article/details/78545530)，用root用户和切换到根目录。

    不包括`/home`和`/boot`的备份：

    ```
    tar -cvpzf /media/ubuntu_backup_`date +%Y%m%d`.tar.gz --exclude=/proc --exclude=/tmp --exclude=/boot --exclude=/home --exclude=/lost+found --exclude=/media --exclude=/mnt --exclude=/run /
    
    ```

    `/boot`备份：

    ```
    tar -cvpzf /media/ubuntu_boot_backup_`date +%Y%m%d`.tar.gz /boot
    
    ```

    `/home`备份：

    ```
    tar -cvpzf /media/ubuntu_home_backup_`date +%Y%m%d`.tar.gz /home
    
    ```

    有可能备份到最后系统会提示”tar: 由于前次错误,将以上次的错误状态退出”，这个警告可以忽略，没什么影响的。