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

