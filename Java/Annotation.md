# 注解（Annotation）

## 概述

注解，也叫元数据。一种代码级别的说明。

类、方法、变量、参数和包等都可以被标注。

### 内置注解

| Annotation         |                                                              |
| ------------------ | ------------------------------------------------------------ |
| `@Override`				 | 检查该方法是否是重载方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。 |
| `@Deprecated`      | 标记过时方法。如果使用该方法，会报编译警告。                 |
|` @SuppressWarnings` | 指示编译器去忽略注解中声明的警告。                         |

### 元注解

| Annotation    |                                                              |
| ------------- | ------------------------------------------------------------ |
| `@Retention`  | @Retention 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。 |
| `@Documented` | 标记这些注解是否包含在用户文档中。                           |
| `@Target`     | 当一个注解被 @Target 注解时，这个注解就被限定了运用的场景。（类、方法等） |
| `@Inherited`  | 标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类)。 |

### 自定义注解

注解通过 `@interface `关键字进行定义。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Author {
    String fullName() default "ZQL";
}
```

### 注解与反射

通过Java反射（类似python自省），来获取注解

```java
// 通过forName获取类
Class cls = Class.forName("com.zql.Book");
// getAnnotation获取类的注解，并强转为注解类
Author author = (Author)cls.getAnnotation(Author.class);
```