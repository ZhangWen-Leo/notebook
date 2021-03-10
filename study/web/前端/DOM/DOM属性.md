# DOM属性

1. innerHTML
2. nodeName
3. nodeValue
4. nodeType

## 1. innerHTML

获取元素的内容

```html
<html lang="en">
<body>

<p id="intro">Hello World!</p>

<script>
    const txt = document.getElementById("intro").innerHTML;
    document.write(txt);
</script>

</body>
</html>
```

## 2. nodeName

- nodeName是只读的
- 元素节点的nodeName与标签名相同
- 属性节点的nodeName与属性名相同
- 文本节点的nodeName始终是 #text
- 文档节点的nodeName始终是 #document

## 3. nodeValue

- 元素节点的nodeValue是undefined或null
- 文本节点的nodeValue是文本本身
- 属性节点的nodeValue是属性值

## 4. nodeType

|元素类型|nodeType|
|---|---|
|元素|1|
|属性|2|
|文本|3|
|注释|8|
|文档|9|

## 参考文献

1. [HTML DOM 属性](https://www.w3school.com.cn/htmldom/dom_properties.asp)
