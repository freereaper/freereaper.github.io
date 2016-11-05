---
title: 使用pyenv和virtualenv定制python环境
date: 2016-11-07
layout: post
comments: true
tags: [python, shell, pyenv]
---
{% cq %}
[pyenv](https://github.com/yyuu/pyenv)提供管理和切换不同python版本的功能。

[pyenv-virtualenv](https://github.com/yyuu/pyenv-virtualenv)属于pyenv的插件，可以定制相互隔离的python开发环境。

两者可以实现多版本，多环境的控制，可以为每个项目定制完全隔离的python运行环境。
{% endcq %}

## 安装pyenv和pyenv-virtualenv
 
```shell
function bootstrap-pyenv {
    __clone 'https://github.com/yyuu/pyenv.git' '.pyenv'
    if [ ! -d "$HOME/.pyenv/plugins/pyenv-virtualenv" ]; then
        mkdir -p "$HOME/.pyenv/plugins"
        git clone https://github.com/yyuu/pyenv-virtualenv.git $HOME/.pyenv/plugins/pyenv-virtualenv
    fi
}
```
其中__clone具体实现在[init.zsh](https://github.com/freereaper/dotfiles/blob/dev/init.zsh)。

<!-- more -->

添加`pyenv init`和`pyenv virtualenv-init`到.zshrc中：
```shell
# file : .zshrc
# pyenv cfg
export PYENV_ROOT="${HOME}/.pyenv"

if [ -d "${PYENV_ROOT}" ]; then
    export PATH="${PYENV_ROOT}/bin:${PATH}"
    eval "$(pyenv init -)"

    if [ -d "${PYENV_ROOT}/plugins/pyenv-virtualenv" ]; then
        eval "$(pyenv virtualenv-init -)"
    fi
fi

export PYENV_VIRTUALENV_DISABLE_PROMPT=1
```

## pyenv的使用

### 安装和卸载python

``` shell
#查看可以安装的python版本
pyenv install --list

#安装版本3.5.1，系统会从python官网下载安装对应的python版本。
#这里下载会比较慢，可以从后面介绍的两种方法安装。
pyenv install 3.5.1

#更新，为所有安装的可执行文件(~/.pyenv/versions/*/bin/*)c创建shims,
#因此，每当你增删python版本或带有可执行文件的包(如pip)以后，都应该执行
#一次本命令
pyenv rehash

#查看系统已经安装的python版本
pyenv versions

#卸载某个版本的python
pyenv uninstall 3.5.1
```

可选安装方法：
* 直接下载对应的版本压缩包,例如Python-3.5.1.tgz，拷贝至`~/.pyenv/cache/`中，
如果没有cache目录，则手动创建，并修改文件名为Python-3.5.1.tar.gz。
```shell
mv Python-3.5.1.tgz Python-3.5.1.tar.gz
pyenv install 3.5.1 -v
```

* 使用国内镜像的pyenv源安装。
```shell
export PYTHON_BUILD_MIRROR_URL="http://pyenv.qiniudn.com/pythons/"
pyenv install 3.5.1 -v
```

### pyenv与virtualenv

`pyenv global 3.5.1`切换当前python版本为3.5.1，`pyenv global system`切换回系统版本。

当系统安装了多个python版本，为了保持系统环境足够干净，可以使用`pyenv virtualenv`来创建虚拟python环境。

创建一个以python3.5.1为解释器的虚拟环境可以通过以下命令实现:
```shell
pyenv virtualenv 3.5.1 env351
```
此时会在`~/.pyenv/versions/`目录下创建一个env351的目录，通过`pyenv versions`即可看到当前创建的虚拟环境。

为项目单独配置一个虚拟的python运行环境：
```shell
#创建名为project的虚拟环境
pyenv virtualenv 3.5.1 project

#设置进入project目录自动切换为project环境。
#会生成.python_versions文件
cd project_root
pyenv local project

#安装对应的lib，所有的lib都会被安装到虚拟环境中,不会破坏系统本身的环境。
pip install ...
```


卸载创建成功的虚拟环境：
```shell
pyenv uninstall env351
```

