### StringEscapeUtils

StringEscapeUtils类可以对html，xml，sql和js代码进行转义，来防止sql注入及XSS注入

添加依赖包

```
<dependency>
    <groupId>commons-lang</groupId>
    <artifactId>commons-lang</artifactId>
    <version>2.5</version>
</dependency>
```

测试代码如下：

```
@Test
public void test1() {

    System.out.println("escapeHtml : " + StringEscapeUtils.escapeHtml("<a>ddd</a>"));
    System.out.println("unescapeHtml : " + StringEscapeUtils.unescapeHtml("&lt;a&gt;ddd&lt;/a&gt;"));

    System.out.println("escapeXml : " + StringEscapeUtils.escapeXml("<name>ddd</name>"));
    System.out.println("unescapeXml : " + StringEscapeUtils.unescapeXml("&lt;name&gt;ddd&lt;/name&gt;"));

    System.out.println("escapeSql : " + StringEscapeUtils.escapeSql("'1' or '1"));

    System.out.println("escapeJavaScipt : " + StringEscapeUtils.escapeJavaScript("<script>alert('attack')</script>"));
    System.out.println("unescapeJavaScipt : " + StringEscapeUtils.unescapeJavaScript("<script>alert(\\'attack\\')<\\/script>"));
}
```

输出结果如下：

```
escapeHtml : &lt;a&gt;ddd&lt;/a&gt;
unescapeHtml : <a>ddd</a>
escapeXml : &lt;name&gt;ddd&lt;/name&gt;
unescapeXml : <name>ddd</name>
escapeSql : ''1'' or ''1
escapeJavaScipt : <script>alert(\'attack\')<\/script>
unescapeJavaScipt : <script>alert('attack')</script>
```