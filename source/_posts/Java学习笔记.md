---
title: Java学习笔记
date: 2020-09-23 09:58:00
categories: Java
tags: Java
---



# vscode+java疑难杂症

## 1. 报找不到主类的错误

示例代码

```java
package lixiao.sorthuluwa;

import lixiao.grandfather.*;
import lixiao.huluwa.*;

public class SortHuluwa {
    public static void main(String[] args) {
        Huluwa[] queue = {new Huluwa(1, "大娃")};
        Grandfather.getInstance().SortHuluwa(queue);
    }
}
```

运行

```
javac SortHuluwa.java 无错误
java SortHuluwa 报找不到主类的错误
```

原因，要带上包名运行

```
java lixiao.sorthuluwa.SortHuluwa
```



## 2. 多个package，多个java文件时不要点run code按钮

因为其他包还没编译，首先要编译其他包，然后编译自己

编译时不要用

```
javac -d . *.java
```

因为没有指定编译顺序，依赖会错误



可以写个bat脚本来做编译包和运行的事，比如

```bat
del .\*.class /s
echo "删除*.class成功"

javac -d . Huluwa.java
javac -d . Grandfather.java
javac -d . SortHuluwa.java
java lixiao.sorthuluwa.SortHuluwa

```



## 3. 设计问题：不希望父类被new Huluwa()，但允许子类的new Dawa()

即不希望看到父类的默认构造函数被外部使用者调用

但允许子类构造函数被外部使用者调用，做法是

```
public class Huluwa {
	protected Huluwa() {}
}
```

```
public class Dawa extends Huluwa {
	public Dawa() {...}
}
```

不可以使用默认访问权限（包内的class可见），因为虽然父类和子类偶尔处于一个包之下，不会出错，但万一不在一个包底下，直接编译错误

```
//Huluwa() {} //不可以
```

