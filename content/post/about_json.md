---
title: "About_json"
date: 2024-02-29T14:18:38Z
draft: true
summary: "关于JSON的一些内容"
---

# 0x00 | 总述
`JSON`的全写是`JavaScript Object Notation`，很明显，是`JS`的副产物。准确的说，是类似于`XML`的储存和交换文本信息的语法，用于各种配置文件，以及网络（主要是网络）上的文本信息交换。

`JSON`的优点是小且易于阅读（至少相比`XML`而言）。许多编程语言都提供了`JSON`的解析工具，就算没有，手动实现一个简单的`JSON`解析器也是不太难的。

# 0x01 | 语法
`JSON`的语法实际上就是`JavaScript`中描述数据的语法。

`JSON`几乎仅由`键值对`，`对象`，`数组`组成。`对象`中包含一个或多个`键值对`，而`数组`中包含多个`对象`。

`JSON`以键值对保存所有数据，并且以`,`分割所有键值对或对象。同时，`JSON`允许你使用以`\`开头的转义字符。另外`{}`用于保存对象，而`[]`用于保存数组。

关于键值对，其`键（key）`一定是`字符串（string）`，而`值（value）`可以是`数值（number，浮点或整型）`，`布尔值（bool）`，`null`，`对象（object）`或`数组（array）`。

# 0x02 | 在JavaScript中使用JSON
作为`JS`之子(?)，很明显`JSON`可以无缝嵌入`JS`：
```javascript
var my_json = [
	{"name":"nihao" , "message":"nihaoma?"},
	{"name":"a" , "message":"¿"}
]

// ...

my_json[0].message = "???";
console.log(my_json[0].name);
```
很多时候我们需要将从服务器传来或从本地读取的字符串`JSON`转换为`JavaScript`对象，这时候可以考虑使用`JSON.parse()`。
```javascript
JSON.parse(text[,reviver])
```
其中，`text`是必须的，是一个有效的`JSON`字符串。而`reviver`是一个可选的用于转换结果的函数，JSON中的每一个成员都将作为参数调用这个函数。
下面是一个示例：
```javascript
var json = JSON.parse('{ "name":"nihao", "score":5}');
console.log(json.name + ' ' + json.score);
```

# 0x03 | 在JavaScript中创建JSON字符串
使用`JSON.stringify()`可以将`JavaScript`对象转换为字符串，语法如下：
```javascript
JSON.stringify(value[,replacer[,space]])
```
`value`是必须的，将要转换为`JSON字符串`的值，通常为对象或数组。`replacer`和`space`都是可选的，前者是用于转换结果的函数或数组，后者为文本添加缩进，空格和换行符。

# 0x04 | 在Java中解析和构建JSON
实际上你甚至可以拼接字符串来硬写，就是可能会比较不优雅且低效。

由于`Java`并没有自带的`JSON`解析库（但是`Jakarta`有个`jakarta.json-api`），你需要使用第三方库，比如`FastJson`。

如果你使用`Maven`，可以在`pom.xml`中添加如下内容：
```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.fastjson2/fastjson2 -->
<dependency>
    <groupId>com.alibaba.fastjson2</groupId>
    <artifactId>fastjson2</artifactId>
    <version>2.0.40</version>
</dependency>
```
你可以在上面的链接中找到最新版本。
```java
// 构建JSON演示
JSONArray array = new JSONArray();  
while (resultSet.next()) {  
    String name = resultSet.getString("user_id");  
    Date time = resultSet.getDate("vote_time");  
    float score = resultSet.getFloat("vote_score");  
    JSONObject obj = new JSONObject();  
    obj.put("name",name);  
    obj.put("vote_time",time);  
    obj.put("vote_score",score);  
    array.add(obj);  
}  
out.print(array);// 直接输出JSON字符串，也可以用JSON.toJSONString(arrOrObj)

// 解析JSON演示
JSONObject object = JSONObject .parseObject("{\"boolean\":true,\"string\":\"string\",\"list\":[1,2,3],\"int\":2}");
//string
String s = object.getString("string"); System.out.println(s);
//int
int i = object.getIntValue("int");
System.out.println(i);
//boolean
boolean b = object.getBooleanValue("boolean");
System.out.println(b);
//list
List<Integer> integers = JSON.parseArray(object.getJSONArray("list").toJSONString(),Integer.class); integers.forEach(System.out::println);
//null
System.out.println(object.getString("null"));
```

---
参考：
[Java 中 JSON 的使用 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/java-json-instro.html)
[JSON 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/json/json-tutorial.html)