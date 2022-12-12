---
title: Java注解和反射常用API
date: 2022-12-12 22:53:12
tags: Java
categories: Java
---
(整理自廖雪峰Java教程)

## 反射

通过 `Class` 实例获取 `class` 信息的方法称为反射

### 获取 `Class` 实例：

1. 直接通过一个class的静态变量class获取：

```java
        Class cls = String.class;
```

2. 如果我们有一个实例变量，可以通过该实例变量提供的getClass()方法获取：

```java
    String s = "Hello";
    Class cls = s.getClass();
```

3. 如果知道一个class的完整类名，可以通过静态方法Class.forName()获取：

```java
   Class cls = Class.forName("java.lang.String");
```

### 访问字段

```JAVA
Field getField(name)：根据字段名获取某个public的field（包括父类）
Field getDeclaredField(name)：根据字段名获取当前类的某个field（不包括父类）
Field[] getFields()：获取所有public的field（包括父类）
Field[] getDeclaredFields()：获取当前类的所有field（不包括父类）
```

* Field 对象

```JAVA
getName()：返回字段名称，例如，"name"；
getType()：返回字段类型，也是一个Class实例，例如，String.class；
getModifiers()：返回字段的修饰符，它是一个int，不同的bit表示不同的含义。
```

* 暴力反射

```JAVA
调用Field.setAccessible(true)的意思是，别管这个字段是不是public，一律允许访问。
```

* 字段操作

```JAVA
//获取：对于一个Person实例，我们可以先拿到name字段对应的Field，再获取这个实例的name字段的值：
Object p = new Person("Xiao Ming");
Class c = p.getClass();
Field f = c.getDeclaredField("name");
Object value = f.get(p);
System.out.println(value); // "Xiao Ming"

//设值：
Person p = new Person("Xiao Ming");
System.out.println(p.getName()); // "Xiao Ming"
Class c = p.getClass();
Field f = c.getDeclaredField("name");
f.setAccessible(true);
f.set(p, "Xiao Hong");
System.out.println(p.getName()); // "Xiao Hong"
```

### 调用方法

```JAVA
Method getMethod(name, Class...)：获取某个public的Method（包括父类）
Method getDeclaredMethod(name, Class...)：获取当前类的某个Method（不包括父类）
Method[] getMethods()：获取所有public的Method（包括父类）
Method[] getDeclaredMethods()：获取当前类的所有Method（不包括父类）
```

* Method 对象

```JAVA
getName()：返回方法名称，例如："getScore"；
getReturnType()：返回方法返回值类型，也是一个Class实例，例如：String.class；
getParameterTypes()：返回方法的参数类型，是一个Class数组，例如：{String.class, int.class}；
getModifiers()：返回方法的修饰符，它是一个int，不同的bit表示不同的含义。
```

### 获取构造函数

### 获取继承关系

### 动态代理

## 注解
