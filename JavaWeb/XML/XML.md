# XML描述

XML是可扩展的标记语言，用以保存数据，也可以作为配置文件。





# hello,xml

如本例，xml可以自定义标签

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Perosn>
    <person sn="001">
        <name>jack</name>
        <sex>man</sex>
    </person>
    <person sn="002">
        <name>lucy</name>
        <sex>woman</sex>
    </person>
    <person sn="003">
        <name>origin</name>
        <sex>man</sex>
    </person>
</Perosn>
```



xml也可以使用单标签

```xml
<person name="harry" sex="man"/>
```



本例中的`Person`标签称为根元素，也叫顶级元素，即没有父标签且只有一个



若想在xml中使用表示<>，需要使用转义字符

例如此处使用`&lt;`和`&gt;`分别表示`<>`

```xml
<name>&lt;origin&gt;</name>
```



使用CDATA可以告诉编译器CDATA里面的内容只是纯文本，不需要解析

```xml
<name>
    <![CDATA[<<<<<<<]]>
</name>
```







# dom4j解析xml

xml用以保存数据，而每次读取数据的过程就叫做解析

这里使用dom4j来解析xml文件

首先将dom4j的jar包引入

```java
public void test() throws DocumentException {
    //创建一个SAXReader输入流，去读取Person.xml配置文件，生成Document对象
    SAXReader saxReader = new SAXReader();
    Document document = saxReader.read("src/com/origin/XML/index.xml");
    //通过document对象获取根元素，即Person标签
    Element rootElement = document.getRootElement();
    //通过根元素获取根元素内的每个person标签
    List<Element> person = rootElement.elements("person");
    for (Element p : person) {
        //asXML()方法可以把标签对象转换为标签字符串
        //System.out.println(p.asXML());
        //得到每个person的name标签对象
        Element nameEle = p.element("name");
        //getText()方法可以获得标签中的文本内容
        String name = nameEle.getText();
        //直接获取指定标签的本文
        String sex = p.elementText("sex");
        //获取标签的属性值
        String sn = p.attributeValue("sn");
        //System.out.println(sn);
    }
}
```

