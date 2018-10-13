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

   1. 先用系统的gcc-7安装驱动，基本步骤和[链接](https://blog.csdn.net/StrugglePeach/article/details/77940490)里的类似，安装后先别安装Cuda9.1。

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

      基本步骤和[链接](https://blog.csdn.net/ksws0292756/article/details/80120561)里的类似，不过安装的Cuda是9.0版本。下载Cuda9.0的时候下载.run文件。安装Cuda9.0的时候，有个是问是否安装NVIDIA驱动的，前面已经安装过了，所以选择no，如果安装的是第一个cuda则软链接（symbolic link）可选yes，否则选no。

      下载cuDNN的时候下载Library for Linux版本，下载后解压：

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

      不需要了之后，卸载命令：

      ```
      sudo /usr/local/cuda-9.0/bin/uninstall_cuda_9.0.pl
      ```

   4. 安装Cuda7.5对应版本cuDNN

      正常步骤安装Cuda7.5会失败，原因是缺乏安装工具。故安装前需把`cuda_7.5.18_linux.run`解压，然后复制`InstallUtils.pm`到`/usr/lib/x86_64-linux-gnu/perl-base/`并且设置环境：

      ```
      sh ./cuda_7.5.18_linux.run --tar mxvf
      sudo cp InstallUtils.pm /usr/lib/x86_64-linux-gnu/perl-base/
      export $PERL5LIB
      ```

      之后就可以和Cuda9.0一样安装了，注意软链接（symbolic link）选择no。cudnn复制到文件夹`cuda-7.5`。

      不需要了之后，卸载命令：

      ```
      sudo /usr/local/cuda-7.5/bin/uninstall_cuda_7.5.pl
      ```

6. 安装Anaconda3

   按照[链接](https://docs.anaconda.com/anaconda/install/linux)安装,换成清华大学的镜像源：

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

    需要系统可正常执行`shadowsocksr`的Python版，因此需要安装Python2：

    ```
    sudo apt install python-minimal
    ```

    从[链接](https://github.com/erguotou520/electron-ssr)下载electron-ssr，用gdebi安装electron-ssr:

    ```
    sudo gdebi electron-ssr_0.2.3_amd64.deb
    ```

    安装后在系统`设置`-->`网络`-->`网络代理` ，设置为`手动`：

    | **HTTP代理**  |                               | **0**    |
    | ------------- | ----------------------------- | -------- |
    | **HTTPS代理** |                               | **0**    |
    | **FTP代理**   |                               | **0**    |
    | **Socks主机** | **127.0.0.1**                 | **1080** |
    | **忽略主机**  | **localhost,127.0.0.0/8,::1** |          |

    还是全局翻墙。按照[链接](https://blog.csdn.net/yucicheung/article/details/79332049)里给Chrome下载配置SwitchOmega后Chrome可以PAC翻墙。

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

    安装后导入美国传统英汉双解学习词典.mdx，新牛津英汉双解大词典.mdx。

19. 安装有道词典

    获取[链接](https://github.com/yomun/youdaodict_5.5)里提供的版本：

    ```
    wget https://github.com/yomun/youdaodict_5.5/raw/master/youdao-dict_1.1.1-0~ubuntu_amd64.deb
    ```

    直接安装。

20. 安装WPS

    按照[链接](https://www.jianshu.com/p/3407c18e1337)里安装。

21. 安装PyCharm

    用教育邮箱在[官网](https://www.jetbrains.com/student/)申请学生礼包，下载，解压到`/usr/local`下，按照`Install-Linux-tar.txt`安装即可，在设置里勾选`Create desktop entry`。

22. 安装Git

    ```
    sudo apt install git
    
    ```

23. 安装Sublime Text 3

    按照[链接](https://www.sublimetext.com/docs/3/linux_repositories.html)安装，安装后不能输入中文解决办法：

    ```
    git clone https://github.com/lyfeyaj/sublime-text-imfix.git
    cd sublime-text-imfix && ./sublime-imfix
    
    ```

24. 安装网易云音乐

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

25. 安装Teamviewer

    [官网](https://www.teamviewer.com/zhcn/download/linux/)下载安装。

26. 安装QQ、WeChat、Tim

    按照[链接](https://github.com/wszqkzqk/deepin-wine-ubuntu)安装。

27. 安装VLC视频播放器

    从应用商店`Ubuntu 软件`搜索`VLC`下载安装。

28. 安装Persepolis，下载工具

    ```
    sudo add-apt-repository ppa:persepolis/ppa
    sudo apt update
    sudo apt install persepolis
    
    ```

    Chrome安装扩展程序：`Persepolis Download Manager Integration`。

29. 安装curl

    ```
    sudo apt install curl
    ```

30. 系统备份

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