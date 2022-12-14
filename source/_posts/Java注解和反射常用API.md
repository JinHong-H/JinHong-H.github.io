---
title: Java注解和反射常用API
date: 2022-12-14 10:30:12
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

通过Class实例获取Constructor的方法如下：

```JAVA
getConstructor(Class...)：获取某个public的Constructor；
getDeclaredConstructor(Class...)：获取某个Constructor；
getConstructors()：获取所有public的Constructor；
getDeclaredConstructors()：获取所有Constructor。
```

* 注意Constructor总是当前类定义的构造方法，和父类无关，因此不存在多态的问题。
调用非public的Constructor时，必须首先通过 `setAccessible(true)` 设置允许访问。 `setAccessible(true)` 可能会失败。

* 示例：

```JAVA
// 获取构造方法Integer(String)
Constructor cons2 = Integer.class.getConstructor(String.class);
Integer n2 = (Integer) cons2.newInstance("456");
```

  

### 获取继承关系

通过Class对象可以获取继承关系：

```JAVA
Class getSuperclass()：获取父类类型；
Class[] getInterfaces()：获取当前类实现的所有接口。
```

通过Class对象的 `isAssignableFrom()` 方法可以判断一个向上转型是否可以实现。

### 动态代理

Java标准库提供了一种动态代理（Dynamic Proxy）的机制：可以在运行期动态创建某个interface的实例。
* 在运行期动态创建一个interface实例的方法如下：

1. 定义一个InvocationHandler实例，它负责实现接口的方法调用；
2. 通过Proxy.newProxyInstance()创建interface实例，它需要3个参数：
   1. 使用的ClassLoader，通常就是接口类的ClassLoader；
   2. 需要实现的接口数组，至少需要传入一个接口进去；
   3. 用来处理接口方法调用的InvocationHandler实例。
3. 将返回的Object强制转型为接口。

* 示例：

```JAVA
public class Main {
    public static void main(String[] args) {
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method);
                if (method.getName().equals("morning")) {
                    System.out.println("Good morning, " + args[0]);
                }
                return null;
            }
        };
        Hello hello = (Hello) Proxy.newProxyInstance(
            Hello.class.getClassLoader(), // 传入ClassLoader
            new Class[] { Hello.class }, // 传入要实现的接口
            handler); // 传入处理调用方法的InvocationHandler
        hello.morning("Bob");
    }
}

interface Hello {
    void morning(String name);
}

```

## 注解

### 使用

* 注解（Annotation）是Java语言用于工具处理的标注：

* 注解可以配置参数，没有指定配置的参数使用默认值；

* 如果参数名称是value，且只有一个参数，那么可以省略参数名称。

### 定义

* 语法

```JAVA
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

注解的参数类似无参数方法，可以用default设定一个默认值（强烈推荐）。最常用的参数应当命名为value。

#### 元注解

  有一些注解可以修饰其他注解，这些注解就称为元注解（meta annotation）
* @Target
最常用的元注解是@Target。使用@Target可以定义Annotation能够被应用于源码的哪些位置：

```JAVA
类或接口：ElementType. TYPE；
字段：ElementType. FIELD；
方法：ElementType. METHOD；
构造方法：ElementType. CONSTRUCTOR；
方法参数：ElementType. PARAMETER。
```

定义注解用在方法或字段上，可以把@Target注解参数变为数组 `{ ElementType. METHOD, ElementType. FIELD }`

* @Retention
  另一个重要的元注解@Retention定义了Annotation的生命周期：

```JAVA
仅编译期：RetentionPolicy.SOURCE；
仅class文件：RetentionPolicy.CLASS；
运行期：RetentionPolicy.RUNTIME。
```

如果@Retention不存在，则该Annotation默认为CLASS。因为通常我们自定义的Annotation都是RUNTIME，所以，务必要加上@Retention(RetentionPolicy. RUNTIME)这个元注解：

* @Inherited
  使用@Inherited定义子类是否可继承父类定义的Annotation。@Inherited仅针对@Target(ElementType. TYPE)类型的annotation有效，并且仅针对class的继承，对interface的继承无效：
  

#### 定义步骤

1. 用@interface定义注解：

```JAVA
public @interface Report {
}
```

2. 添加参数、默认值：

```JAVA
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

把最常用的参数定义为value()，推荐所有参数都尽量设置默认值。

3. 用元注解配置注解：

```JAVA
@Target(ElementType. TYPE)
@Retention(RetentionPolicy. RUNTIME)
public @interface Report {

    int type() default 0;
    String level() default "info";
    String value() default "";

}
```

其中，必须设置@Target和@Retention，@Retention一般设置为RUNTIME，因为我们自定义的注解通常要求在运行期读取。一般情况下，不必写@Inherited和@Repeatable。

### 处理

Java的注解本身对代码逻辑没有任何影响。根据@Retention的配置：

SOURCE类型的注解在编译期就被丢掉了；
CLASS类型的注解仅保存在class文件中，它们不会被加载进JVM；
RUNTIME类型的注解会被加载进JVM，并且在运行期可以被程序读取。

如何使用注解完全由工具决定。SOURCE类型的注解主要由编译器使用，因此我们一般只使用，不编写。CLASS类型的注解主要由底层工具库使用，涉及到class的加载，一般我们很少用到。只有RUNTIME类型的注解不但要使用，还经常需要编写。

#### API

* 判断某个注解是否存在于Class、Field、Method或Constructor：

```JAVA
Class.isAnnotationPresent(Class)
Field.isAnnotationPresent(Class)
Method.isAnnotationPresent(Class)
Constructor.isAnnotationPresent(Class)
```

* 读取Annotation：
  读取到注解之后，结合反射相关API可以对类进行运行期的动态操作

```JAVA
Class.getAnnotation(Class)
Field.getAnnotation(Class)
Method.getAnnotation(Class)
Constructor.getAnnotation(Class)
```

* 注意：可以在运行期通过反射读取RUNTIME类型的注解，千万不要漏写@Retention(RetentionPolicy. RUNTIME)，否则运行期无法读取到该注解。
