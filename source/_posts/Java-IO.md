---
title: Java IO
date: 2022-12-11 22:05:00
tags: Java
categories: Java
---

## File对象

File对象可以操作文件和目录。
* 构造File对象，可以传入绝对路径或者相对路径。注意：File对象既可以表示文件，也可以表示目录。特别要注意的是，构造一个File对象，即使传入的文件或目录不存在，代码也不会出错，因为构造一个File对象，并不会导致任何磁盘操作。只有当我们调用File对象的某些方法的时候，才真正进行磁盘操作。

```JAVA
File f = new File("绝对路径/相对路径")
```

* API

```JAVA
// 获取路径
getPath()，返回构造方法传入的路径，
getAbsolutePath()，返回绝对路径，
getCanonicalPath()，它和绝对路径类似，但是返回的是规范路径。

// 获取权限和大小
boolean canRead()：是否可读；
boolean canWrite()：是否可写；
boolean canExecute()：是否可执行(对目录而言，是否可执行表示能否列出它包含的文件和子目录)；
long length()：文件字节大小。

// 创建和删除文件
createNewFile()：创建新文件
delete()：删除该文件
createTempFile()：创建临时文件
deleteOnExit()：在JVM退出时自动删除该文件。

// 遍历文件和目录
当File对象表示一个目录时，可以使用list()和listFiles()列出目录下的文件和子目录名
boolean mkdir()：创建当前File对象表示的目录；
boolean mkdirs()：创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
boolean delete()：删除当前File对象表示的目录，当前目录必须为空才能删除成功。
```

* Java标准库还提供了一个Path对象，它位于java.nio.file包。Path对象和File对象类似，但操作更加简单：

## InputStream

## OutputStream
