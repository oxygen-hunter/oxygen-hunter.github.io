---
title: zotero better bibtex配置教程
date: 2024-11-22 13:52:09
categories:
tags:
---

## intro

better bibtex for zotero 是一款zotero文献管理插件，可以改变zotero文献索引+自定义导出文献格式（类似google scholar的格式）



## install

https://retorque.re/zotero-better-bibtex/installation/



## set up

1. 插件设置 [1]
   1. 点击 **编辑——首选项——Better BibTeX**；设置 引用格式公式 为 `auth.fold.lower + year + shorttitle(1,0).lower`；
   2. 设置 不导出的字段 为 `abstract,file,shorttitle,langid,month,keywords`；
   3. 取消选择 `对标题应用标题大小写格式 和 使用大括号括起首字母大写的单词以保持大小写格式`。
   4. 设置 导入时格式化标题为句首大写 为 `否（按原样导入标题）`；
   5. 转至 **导出** 选项卡，设置 快速复制条目格式 为 `Better BibTeX`。

2. 设置完了要手动重建zotero key，具体操作 [2]
   1. 我的文库-右边文献全选-Better Bibtex-刷新Bibtex引用



## use

1. 选个文库-导出条目-格式选Better bibtex-导出
2. 效果展示



```
@article{boi2024smart,
  title = {Smart Contract Vulnerability Detection: The Role of Large Language Model (LLM)},
  author = {Boi, Biagio and Esposito, Christian and Lee, Sokjoon},
  year = {2024},
  journal = {SIGAPP Appl. Comput. Rev.},
  volume = {24},
  number = {2},
  pages = {19--29},
  issn = {1559-6915},
  doi = {10.1145/3687251.3687253},
  urldate = {2024-10-09}
}
```





## references

[1] https://www.cnblogs.com/BoyaYan/p/17310302.html

[2] https://blog.sciencenet.cn/blog-531760-1002555.html

