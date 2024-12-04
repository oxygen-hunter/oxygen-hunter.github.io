---
layout: hexo
title: hexo-node版本问题解决
date: 2024-11-22 15:24:39
tags:
---

## intro

起因是hexo@3.8.0与手动安装的node16.x.x不兼容，导致hexo d的时侯出现一个mode type报错



## solution

与hexo@3.8.0兼容的node是12.14.0，参考 [1]

所以把node降版本或者hexo升版本即可，参考 [2]

测试发现hexo升版本还是存在问题，hexo@7.3.0与node18.20.5不兼容



坑点：

1. 必须用兼容的node去装hexo，如果用高版本node装低版本hexo，再切回低版本node，此时依然不兼容且卸载时容易出问题。所以需要在高版本node安装，高版本node卸载，再换回低版本
2. 在项目内安装hexo的时侯先把旧的`packages-lock.json`和`node_modules`删掉，然后用`npm install hexo@3.9.0 --save`，其中--save不能少，不然不会修改本项目里的packages.json，之后使用的时候用`npx hexo clean`去启动
3. 想全局安装hexo的话，`npm install -g hexo@3.9.0 `





一些用到的命令：

1. nvm安装，卸载和切换

```
nvm install 12.14.0
nvm use 12.14.0
nvm uninstall 12.14.0
```

2. npm安装hexo

```
npm install hexo@3.9.0 --save
```





## references

[1] https://hexo.io/docs/

[2] https://www.jb51.net/article/202124.htm