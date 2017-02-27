---
title: YouCompleteMe笔记
date: 2016-11-11
layout: post
comments: true
tags: [python, metaclass]
---

### Build Vim8 From Source

* 卸载旧版本的vim
```bash
sudo apt-get remove vim vim-runtime gvim
sudo apt-get remove vim-tiny vim-common vim-gui-common vim-nox
```
* 安装依赖包
```bash
sudo apt-get install libncurses5-dev libgnome2-dev libgnomeui-dev \
    libgtk2.0-dev libatk1.0-dev libbonoboui2-dev \
    libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev \
    python3-dev ruby-dev lua5.1 lua5.1-dev luajit libluajit-5.1-dev \
    libperl-dev git
```
* 编译vim8.0
```bash
#设置代理
git config --global http.proxy "socks5://127.0.0.1:1080"
git config --global https.proxy "socks5://127.0.0.1:1080"
cd ~/download
git clone https://github.com/vim/vim.git vim
cd vim
# 其中python-config dir可以通过pytho-config --configdir查看
./configure --with-compiledby="freereaper" \
            --with-features=huge \
            --enable-multibyte \
            --enable-perlinterp=yes \
            --enable-rubyinterp=yes \
            --enable-pythoninterp=yes \
            --with-python-config-dir=/usr/lib/python2.7/config-x86_64-linux-gnu \
            --enable-python3interp=yes \
            --with-python3-config-dir=/usr/lib/python3.4/config-3.4m-x86_64-linux-gnu \
            --with-luajit --enable-luainterp=yes \
            --with-lua-prefix=/usr/include/lua5.1 \
            --enable-gui=gnome2 --enable-gnome-check --enable-cscope \
            --prefix=/usr --enable-fail-if-missing
make VIMRUNTIMEDIR=/usr/share/vim/vim80
sudo make install
```

###编译YCM
通过plug工具安装YouCompleteMe插件，建议走代理。
```bash
git config --global http.proxy "socks5://127.0.0.1:1080"
git config --global https.proxy "socks5://127.0.0.1:1080"
```
下载Pre_Buillt Clang Binaries，按照以下步骤编译ycm_core.so
```bash
wget http://llvm.org/releases/3.9.0/clang+llvm-3.9.0-x86_64-linux-gnu-ubuntu-14.04.tar.xz -O clang+llvm.tar.xz
xz -d clang+llvm.tar.xz
tar -xvf clang+llvm.tar.xz -C ~/ycm_temp/
mkdir -p ~/ycm_build
cd ~/ycm_build
cmake -G "Unix Makefiles" -DPATH_TO_LLVM_ROOT=~/ycm_temp/clang+llvm-3.9.0-x86_64-linux-gnu-ubuntu-14.04 . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
cmake --build . --target ycm_core 
```


