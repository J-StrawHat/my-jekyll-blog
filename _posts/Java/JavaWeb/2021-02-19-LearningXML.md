---
title: XML 学习笔记
author: JoyDee
categories: [Java, JavaWeb]
tags: [JavaWeb, XML]
date: 2021-02-19 16:03:00 +0800
---

# 一、XML 概述

XML 指**可拓展**标记语言（EXtensible Markup Language），可拓展指的是，XML 标签并没有被预定义，需要自行定义标签。

XML 是 W3C 的推荐标准，语法极为严格。

## XML的自我描述性

XML 标签具有自我描述性，如下举例：

```xml
<note>
<to>Nami</to>
<from>Luffy</from>
<heading>Reminder</heading>
<body>Don't forget go to school this weekend!</body>
</note>
```

显然，上面的 XML 文档，并没有做任何事情，仅仅是包装在 XML 标签中的纯粹信息。我们需要**编写软件或程序**，才能传送、接收和显示出这个文档。

## XML 与 HTML 的区别

|          |               XML                |                  HTML                  |
| :------: | :------------------------------: | :------------------------------------: |
|  着重点  |            数据的内容            |               数据的外观               |
|   用途   |        **传输和存储**数据        |            格式化并显示数据            |
|   标签   |              自定义              |                 预定义                 |
| 空格字符 | 标签体内部的空格字符**不会删减** | 将多个连续的空格字符合并为一个空格字符 |
|   语法   |               严格               |                 较松散                 |

# 二、XML 语法

XML 文档举例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<students>
    <student number = "strawhat_0001">
        <name>Luffy</name>
        <age>20</age>
        <sex>male</sex>
    </student>
    <student number = "strawhat_0002">
        <name>Nami</name>
        <age>20</age>
        <sex>female</sex>
    </student>
</students>
```

## 组成部分

1. 文档声明

   格式：`<?xml 属性列表 ?>`；

   其中，属性列表有：

   + `version`：版本号，必须项，大部分为 `1.0`
   + `encoding`：编码方式，告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1，但我们希望是 `utf-8`
   + `standalone`：是否独立。取值若为 `yes`，则不依赖其他文件

2. 指令：结合 CSS 的（略）

3. 标签：标签名称自定义的，其命令规则为：

   + 名称可以包含字母、数字以及其他的字符
   + 名称不能包含空格
   + 名称不能以数字或者标点符号开始
   + 名称不能以**字母 xml（或者 XML、Xml 等等）**开始

   > **实用经验**：XML 文档常有一个对应的数据库，其中的字段会对应 XML 文档中的元素，因而推荐使用数据库的命名规则来命名 XML 文档中元素

4. 属性：尽量使用元素来描述数据。而仅仅使用属性来提供与数据无关的信息。

   有时候会向元素 分配 `ID` 应用，这些 `ID` 索引可用于标识 XML 元素（属性值是唯一的），它起作用的方式与 HTML 中 id 属性是一样的。

   > 元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。

5. 文本：在 XML 中，一些字符，如 `<`、`>`、`&`、`'`、`"` 等，拥有特殊的意义。若希望展示上述特殊字符，可以使用 实体引用 （如 `&lt;` 来代替 `<` 等），也可以使用 CDATA 区来“原样展示”内容

   CDATA 区，即不应该由 XML 解析器解析的文本数据，格式为：

   ```xml
   <![CDATA[
   	数据
   ]]>
   ```

## 注意事项

1. XML 文档的后缀名为 `.xml`
2. XML 第一行必须定义为**文档声明**
3. XML 文档中有且仅有一个根标签
4. **属性值**必须使用**引号**(单双都可)引起来
5. 标签必须正确关闭，如 `<br />`
6. XML 标签名称**大小写敏感**

## 约束（XML规则）

约束，即规定了 XML 文档书写规则。

拥有正确语法的 XML 被称为"形式良好"的 XML。而通过 DTD 验证的XML是"合法"的 XML。

以下为两种 XML 的约束技术：

### XML DTD

DTD 的目的是定义 XML 文档的结构。DTD 使用一系列合法的元素来定义文档结构。（也就说，规定 XML 文档 应该按什么东西进行编写）

DTD 可被成行地声明于 XML 文档中，也可作为一个外部引用。

+ 内部 DTD：将约束规则定义在 XML 文档中。如下：

  ```xml
  <?xml version="1.0"?>
  <!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to (#PCDATA)>
  <!ELEMENT from (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
  ]>
  <note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend</body>
  </note>
  ```

+ 外部 DTD：将约束规则定义在外部的 DTD 文件中，然后在 XML 文档中，以 `DOCTYOE` 进行声明：

  + DTD文件若在本地：`<!DOCTYPE 根标签名 SYSTEM "DTD文件的位置">`
  + DTD文件若在网络：`<!DOCTYPE 根标签名 PUBLIC "DTD文件名称(自定义)" "DTD文件的位置URL">`

  如下举例：XML文档，拥有一个外部的 DTD

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE students SYSTEM "student.dtd"> <!--外部文档声明-->
  
  <students>
  	<student number="s001">
  		<name>Luffy</name>
  		<age>20</age>
  		<sex>hehe</sex>
  	</student>
  	<student number="s002">
  		<name>Nami</name>
  		<age>24</age>
  		<sex>female</sex>
  	</student>
  </students>
  ```

  其外部的 DTD文件：`student.dtd`

  ```
  <!ELEMENT students (student*) >
  <!ELEMENT student (name,age,sex)>
  <!ELEMENT name (#PCDATA)>
  <!ELEMENT age (#PCDATA)>
  <!ELEMENT sex (#PCDATA)>
  <!ATTLIST student number ID #REQUIRED>
  ```

  > 如上规定，`<name>`、`<age>`、`<sex>` 三个标签必须按顺序编写；`#PCDATA` 表明 标签体内容为 一字符串；`ATTLIST` 声明了属性，属性名称为 `number`，类型为 `ID`（表示该属性值必须唯一）；`#REQUIRED` 要求了该属性必须定义

### XML Schema

相较于 DTD，Schema 对标签体的内容进行了更加严格的限定。

比如说：我希望在 `<age>` 标签体内容中限定只能是数字，而不能是其他的字符。对于 Schema的约束，甚至连 `<age>` 标签体中的数字大小有规定，又或者，对于 `<sex>` ，规定只有两种取值。

Schema 的约束文件名后缀为 `xsd`

步骤：

1. 填写 XML 的根元素

2. 引用 `xsi` 前缀，属固定格式。（如下方  `student.xml`文档的第 `2` 行）推荐取值为：

   ```xml
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   ```

3. 引入 `xsd` 文件命名空间（如下方  `student.xml`文档的第 `2` 行）

   ```xml
   xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
   ```

   > 也就说，给路径为`http://www.itcast.cn/xml`的文件起一个别名，叫 `student.xsd`

4. 为简化前缀，我们为每一个 `xsd` 约束声明一个前缀，作为标识。（如下方  `student.xml`文档的第 `4` 行）

   > 假如说，我设置 `xmlns:a ="代替的链接"`，那么要使用该链接下的标签 `<age>`，则需要像 `<a:age>` 这样去编写。
   >
   > 再比如 `xmlns="代替的链接"`，那么要使用该链接下的标签 `<age>`，只需 `<age>` 这样去编写。
   >
   > ```xml
   > <students xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
   > 	  xsi:schemaLocation="
   >             	http://www.itcast.cn/xml  student.xsd
   >                http://www.itcast.cn/xml2  student2.xsd
   >          "
   >     	  xmlns:a="http://www.itcast.cn/xml"
   >           xmlns:b="http://www.itcast.cn/xml2"
   > >
   >     <student>
   >         <a:name>...</a:name>
   >         <b:name>...</b:name>
   >     </student>
   > </students>
   > ```

如下举例，XML 文档 `student.xml`如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<students   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
			xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
    		xmlns="http://www.itcast.cn/xml">
    <student number="heima_0001">
        <name>tom</name>
        <age>18</age>
        <sex>male</sex>
    </student>
</students>
```

`student.xsd` 文件如下：

```
<?xml version="1.0"?>
<xsd:schema xmlns="http://www.itcast.cn/xml"
        xmlns:xsd="http://www.w3.org/2001/XMLSchema"
        targetNamespace="http://www.itcast.cn/xml" elementFormDefault="qualified">
    <xsd:element name="students" type="studentsType"/>
    <xsd:complexType name="studentsType">
        <xsd:sequence>
            <xsd:element name="student" type="studentType" minOccurs="0" maxOccurs="unbounded"/>
        </xsd:sequence>
    </xsd:complexType>
    <xsd:complexType name="studentType">
        <xsd:sequence>
            <xsd:element name="name" type="xsd:string"/>
            <xsd:element name="age" type="ageType" />
            <xsd:element name="sex" type="sexType" />
        </xsd:sequence>
        <xsd:attribute name="number" type="numberType" use="required"/>
    </xsd:complexType>
    <xsd:simpleType name="sexType">
        <xsd:restriction base="xsd:string">
            <xsd:enumeration value="male"/>
            <xsd:enumeration value="female"/>
        </xsd:restriction>
    </xsd:simpleType>
    <xsd:simpleType name="ageType">
        <xsd:restriction base="xsd:integer">
            <xsd:minInclusive value="0"/>
            <xsd:maxInclusive value="256"/>
        </xsd:restriction>
    </xsd:simpleType>
    <xsd:simpleType name="numberType">
        <xsd:restriction base="xsd:string">
            <xsd:pattern value="heima_\d{4}"/>
        </xsd:restriction>
    </xsd:simpleType>
</xsd:schema> 
```

# 三、解析 XML 文档

操作 XML 文档：

+ 解析（读入）：将文档中的数据读入到内存中；
+ 写入：将内存中的数据保存到 XML 文档中，从而实现持久化存储

解析 XML 文档的方式：

|      | DOM                                                          | SAX                        |
| ---- | ------------------------------------------------------------ | -------------------------- |
| 描述 | 将标记语言文档**一次性**加载进内存，<br>在内存中形成一棵 **DOM** 树 | 逐行读入，基于**事件**驱动 |
| 优点 | 操作方便，可对文档进行 CRUD 的所有操作                       | 内存消耗小                 |
| 缺点 | 对内存消耗较大                                               | 只能读入，不能增删改       |

常见的 XML 解析器：

1. JAXP：sun 公司提供的解析器，支持 DOM 和 SAX 两种思想
2. DOM4J：一款非常优秀的解析器
3. **Jsoup**：Jsoup 是一款 Java 的 HTML 解析器，可直接解析某个 URL 地址、 HTML 文本内容。它提供了一套非常方便的 API，可通过 DOM，CSS 以及类似于 jQuery 的操作方法来取出并操作数据。
4. PULL：Android 操作系统内置的解析器，通过 SAX 方式。

## 相关对象

### Jsoup 工具类

能够解析 HTML 或 XML 文档，返回 `Document` 对象。其方法主要围绕于  `parse()`

+ `parse(File in, String charsetName)`：解析 XML 或 HTML **文件**。

+ `parse(String html)`：解析xml或html字符串。

  ```java
  String str = "
      xml或HTML中的内容 
  	";
  Document document = Jsoup.parse(str);
  ```

+ `parse(URL url, int timeoutMills)`：通过网络路径 URL 获取指定的 HTML 或 XML 的文档对象 （可用于爬虫）

  > URL 为 统一资源定位符。

  ```java
  URL url = new URL("https://baike.baidu.com/item/jsoup/9012509?fr=aladdin");
  //传入的 URL 代表网络中的一个资源路径
  Document document = Jsoup.parse(url, 10000); //限定超时时间
  ```

### Document 文档对象

`Document` 对象代表了内存中的 DOM 树。

获取 `Element` 对象的方法有：

+ `getElementById(String id)`：根据id属性值获取唯一的 `element` 对象
+ `getElementsByTag(String tagName)`：根据标签名称获取元素对象集合
+ `getElementsByAttribute(String key)`：根据属性名称获取元素对象集合
+ `getElementsByAttributeValue(String key, String value)`：根据对应的属性名和属性值获取元素对象集合

### Elements 对象

`Elements`  对象，是元素 `Element` 对象的集合，你可以理解为 `ArrayList<Element>` 

### ELement 元素对象

（一）获取**子元素**对象的方法：（方法名同 `Document` 对象的相关方法）

+ `getElementById(String id)`：根据id属性值获取唯一的 `element` 对象
+ `getElementsByTag(String tagName)`：根据标签名称获取元素对象集合
+ `getElementsByAttribute(String key)`：根据属性名称获取元素对象集合
+ `getElementsByAttributeValue(String key, String value)`：根据对应的属性名和属性值获取元素对象集合

（二）获取相应属性值的方法：

+ `String attr(String key)`：从该标签元素中，根据属性名称来获取相应的属性值

（三）获取文本内容：

+ `String text()`：获取**所有子标签**的纯文本内容
+ `String html()`：获取标签体的所有内容

## 利用 Jsoup 解析 XML

步骤如下：

1. 导入 jar 包

   > XML 文档，最好放到 `src` 目录下。

2. 获取 `Document` 对象

3. 获取对应的标签 `Element` 对象

4. 获取数据

举例如下：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220182205.png"/>

`student.xml` XML 文件如下：（约束暂时省略不写）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<students>
    <student number = "heima_0001">
        <name>Luffy</name>
        <age>20</age>
        <sex>male</sex>
    </student>
    <student number = "heima_0002">
        <name>Nami</name>
        <age>20</age>
        <sex>female</sex>
    </student>
</students>
```

`JsoupDemo1.java` ，解析代码如下 ：

```java
package top.JoyDee.LearningXML.jsoup;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.IOException;
import java.net.URL;

public class JsoupDemo1 {
    public static void main(String[] args) throws IOException {
        //1. 导入jsoup的jar包
        //2. 获取Document对象，根据xml文档获取
        //2.1 获取student.xml的path
        URL tmpUrl = JsoupDemo1.class.getClassLoader().getResource("student.xml");
        //使用类加载器，通过其getResource方法获取URL，对URL转换为String
        if(tmpUrl != null){ //避免空指针异常
            String path = tmpUrl.getPath();
            //2.2 解析XML文档，加载文档进内存，获取DOM树--->Document
            Document document = Jsoup.parse(new File(path), "utf-8");
            //3. 获取元素对象Element
            Elements elements = document.getElementsByTag("name");

            //3.1 获取第一个element对象
            Element e = elements.get(0);
            String name = e.text();
            System.out.println(name);
        } else{
            System.out.println("tmpUrl is NULL");
        }
    }
}
```

## 快捷查询方式

1. XPath：即 XML 路径语言，它是一种用来确定 XML （标准通用标记语言的子集）文档中某部分位置的语言。需导入 jar 包。语法请查询 w3cschool 的相关参考手册

2. `selector`：

   `Element` 有一个方法：`Elements select(String cssQuery)`，传入 CSS选择器 的字符串形式，返回 `Elements` 

   语法：参考 `Selector` 类中定义的语法。

   举例使用：

   ```java
   package top.JoyDee.LearningXML.jsoup;
   
   import org.jsoup.Jsoup;
   import org.jsoup.nodes.Document;
   import org.jsoup.select.Elements;
   
   import java.io.File;
   import java.io.IOException;
   
   public class JsoupWithSelectorDemo {
       public static void main(String[] args) throws IOException {
           //1.获取student.xml的path
           String path = JsoupWithSelectorDemo.class.getClassLoader().getResource("student.xml").getPath();
           //2.获取Document对象
           Document document = Jsoup.parse(new File(path), "utf-8");
   
           //3.查询name标签
           org.jsoup.select.Elements elements = document.select("name");
           System.out.println(elements);
           System.out.println("-----------------");
           //4.获取student标签并且number属性值为heima_0001的age子标签
           //4.1.获取student标签并且number属性值为heima_0001
           org.jsoup.select.Elements elements2 = document.select("student[number=\"heima_0001\"]");
           System.out.println(elements2);
           System.out.println("----------------");
           //4.2获取student标签并且number属性值为heima_0001的age子标签
           Elements elements3 = document.select("student[number=\"heima_0001\"] > age");
           System.out.println(elements3);
       }
   }
   ```

   运行结果：

   ```
   <name>
    Luffy
   </name>
   <name>
    Nami
   </name>
   -----------------
   <student number="heima_0001"> 
    <name>
     Luffy
    </name> 
    <age>
     20
    </age> 
    <sex>
     male
    </sex> 
   </student>
   ----------------
   <age>
    20
   </age>
   ```

   