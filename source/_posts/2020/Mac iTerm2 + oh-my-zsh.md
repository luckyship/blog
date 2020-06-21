---
title: 【Mac 终端配置】iTerm2 + oh-my-zsh
tags:
  - 工具
copyright: true
comments: true
date: 2020-06-21 23:41:55
categories: 工具
photos:
---

## 前置校验
```bash
# 1、默认已安装 Homebrew、iTerm2
# 2、查看是否安装了zsh，查看是否返回 /usr/bin/zsh
cat /etc/shells
# 若未安装
brew install zsh

# iTerm2 启动项配置
chsh -s /bin/zsh      # 设置为zsh
chsh -s /bin/bash     # 设置为bash（Mac自带默认）
```

## 安装 ob-my-zsh
```bash
# 方式一：wegt安装
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

# 方式二：curl 安装
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 方式三：手动安装
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

# 卸载
uninstall_oh_my_zsh zsh

# zsh配置环境变量
~/.zshrc
```

## 安装 Powerline
```bash
# 1、检测是否已经安装，若有版本信息则已安装
pip show powerline-status

# 2、将 powerline-status 安装在/usr/根目录中
pip install --user powerline-status

# 上一步若显示没有 pip,先安装pip
sudo easy_install pip
```

## 设置字体
```bash
# 1、新建文件夹(如~/Desktop/OpenSource)，文件夹下
cd ~/Desktop/OpenSource
git clone https://github.com/powerline/fonts.git --depth=1
# 2、进入脚本目录
cd fonts
# 3、执行脚本
./install.sh

# 进入 iTerm2 -> Preferences -> Profiles -> Text -> Font -> Change Font
# 选择Meslo LG S for Powerfine, 常规， 12
```

## 设置配色方案
```bash
# 直接下载tar.zip包(包含全部配色)
# 进入 iTerm2 -> Preferences -> Profiles->Color 
# 选择 Color Presets->import 选择解压好的目录下schemes目录中相应配色方案导入
```

## 安装 oh-my-zsh 主题
```bash
# 下载安装 agnoster 主题，将主题拷贝到oh my zsh的zsh中
cd ~/Desktop/OpenSource
git clone https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor.git
cd oh-my-zsh-agnoster-fcamblor/
./install

# 将 ZSH_THEME 值改为 agnoster，ecs 退出，:wq 保存
vi ~/.zshrc
```

## 安装插件
```bash
# ======================== 高亮插件 ========================
# 在 ~ 目录下新建文件夹 zsh-plugins(~/.zshrc 默认目录)
cd zsh-plugins
git clone git://github.com/zsh-users/zsh-syntax-highlighting.git
vim .zshrc
# 文末添加以下配置
source ~/zsh-pludins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
cd ~/.oh-my-zsh/custom/plugins
vim .zshrc
# 文末添加以下配置
plugins=(zsh-syntax-highlighting)

# ======================== 自动补齐插件 ========================
cd zsh-plugins
http://mimosa-pudica.net/src/incr-0.2.zsh
# 将文件放到 ~/zsh-pludins/inrc下
vim .zshrc
source ~/.oh-my-zsh/plugins/incr/incr*.zsh
```
