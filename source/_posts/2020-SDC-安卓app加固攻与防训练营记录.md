---
title: 2020 SDC 安卓app加固攻与防训练营记录
date: 2020-10-19 08:42:17
categories: Reverse Engineering
tags: Android RE
---

# 前期环境准备

## 通知

@所有人
感谢大家报名《2020 SDC 安卓app加固攻与防》训练营,下面给大家说下训练营需要做好预先准备的内容:
1、提前准备好500G的存储空间。课程会给大家准备好aosp完整编译和实验环境，因此需要提前准备好足够的空间。
2、课程全程在pixel手机上进行实验，该机型随报名课程赠送每人一个。
3、需要提前掌握aosp系统编译与刷机、常见逆向分析常见工具如jadx,jeb，ida，gdb等的使用
4、鉴于直接拉aosp代码非常耗时，我这里上传了压缩的aosp8.0源码，下载解压直接搭建源码编译环境即可，复制这段内容后打开百度网盘手机App，操作更方便哦 链接:https://pan.baidu.com/s/1XrJ_TziryjuDBwa5JCpbIg 提取码:7x41

---

训练营地点确定了。在上海裕景大饭店
时间10/22  9：00-18：00



## aosp系统编译





## jadx，jeb，ida，gdb在android上的使用

jadx：https://github.com/skylot/jadx

jeb：https://www.52pojie.cn/thread-722648-1-1.html

jeb要java 8.121及之前版本



# 训练营内容

## 1. and app加固技术发展

dex，整体粒度

函数粒度

vmp，dex2c，指令粒度



只加固onCreate函数，因为参数固定

用jni语言映射java，因为jni不好逆？



加壳后每个函数解释器是相同的，不然so会很大

---

判断壳的类型：

1. 整体壳只要完整dump下来就行



2. 函数壳在dump完内存dex镜像后，依然存在函数内容无效的情况，（直接nop或随机填充）



3. 指令粒度壳：

* 如果每个函数绑定到相同地址（共享解释器），是vmp

* 如果每个被保护的函数逻辑不同，大小不同，是dec2c



vmp映射关系，const string之类的指令可以很方便猜出来，因为长度固定，允许多对一映射，每个函数的映射关系都不一样



## 2. and app脱壳原理

dexcode的前16字节是固定的

```
struct DexCode {
	u2
	u2
	u2
	u2
	u4
	u4
}
```





如何整体dex获取？

1. 内存中dex文件的起始地址和大小
2. 时机问题（不同的时机dump的内容不一样，越晚越完整？）



Dalvik下通用脱壳

关键字pathList以及内部类lement，根据classloader来脱壳



loadDex->DexFile->openDexFile->。。



Dalvik下两个有效脱壳点：

1. 主要是带dex前缀的？
2. dexfileparse，dvmDexFileOpen



getDex和getBytes函数，可以直接用纯java代码写脱壳工具





ART脱壳点

1. openCommon，openMemory
2. 修改dex2oat在dex编译优化流程中完成脱壳
3. 利用构造函数DexFile::DexFile()脱壳



拿到artmethod，也可脱壳，ida打开后搜artmethod方法，



solveClass，defineClass



## 3. FART脱壳机的原理

三大功能

* dex dump

* 主动调用

* 修复组件







## 4. hyperpwn动态调试vmp