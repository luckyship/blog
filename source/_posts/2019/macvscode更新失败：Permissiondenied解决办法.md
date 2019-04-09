---
title: macvscode更新失败：Permissiondenied解决办法
tags:
  - 安装
copyright: true
comments: true
date: 2019-04-09 23:15:24
categories: 工具
photos:
---

## 场景 -- mac vscode不能安装更新
> Could not create temporary directory: Permission denied

## 原因分析
mac下`/Users/username/Library/Caches/`用户文件不一样，root和username

导致

> drwxr-xr-x   6 username  staff   204B Jan 17 20:33 com.microsoft.VSCode
> drwxr--r--   2 root    staff    68B Dec 17 13:51 com.microsoft.VSCode.ShipIt

## 解决方案
```
// 1. 关闭vscode

// 2. 这一步是需要输入密码的
sudo chown $USER ~/Library/Caches/com.microsoft.VSCode.ShipIt/ 

// 3. 这一步是不需要输入密码的, 如果不进行第一步，第二步会报错
sudo chown $USER ~/Library/Caches/com.microsoft.VSCode.ShipIt/*

// 4. 更新xattr
xattr -dr com.apple.quarantine /Applications/Visual\ Studio\ Code.app
```

## 重新安装更新 完成😁