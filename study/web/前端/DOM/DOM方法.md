# DOM方法

1. getElementById()
2. getElementsByTagName
3. getElementsByClassName
4. appendChild
5. removeChild
6. replaceChild
7. insertBefore
8. createAttribute
9. createElement
10. createTextNode
11. getAttribute
12. setAttribute

## 1. getElementById()

```javascript
/**
 * node: 节点
 * return: 对应id的element，没有则返回null
 */
node.getElementById("id");
// 实例1
document.getElementById("intro");
```

## 2. getElementsByTagName

```javascript
node.getElementsByTagName("tagname");
// 实例1
document.getElementsByTagName("p");
// 实例2
document.getElementById("main").getElementsByTagName("p");
```

## 3. getElementsByClassName

```javascript
node.getElementsByClassName("classname");
// 实例1
document.getElementsByClassName("intro");
```

## 4. appendChild

```html
<div id="div1">
    <p id="p1">This is a paragraph.</p>
    <p id="p2">This is another paragraph.</p>
</div>
<script>
    let para=document.createElement("p");
    let node=document.createTextNode("This is new.");
    para.appendChild(node);
    
    let element=document.getElementById("div1");
    element.appendChild(para);
</script>
```

## 5. removeChild

```html
<div id="div1">
    <p id="p1">This is a paragraph.</p>
    <p id="p2">This is another paragraph.</p>
</div>
<script>
    let parent=document.getElementById("div1");
    let child=document.getElementById("p1");
    parent.removeChild(child);
</script>
```

## 6. replaceChild



## 7. insertBefore

```html
<div id="div1">
    <p id="p1">This is a paragraph.</p>
    <p id="p2">This is another paragraph.</p>
</div>
<script>
    let para=document.createElement("p");
    let node=document.createTextNode("This is new.");
    para.appendChild(node);
    
    let element=document.getElementById("div1");
    let child=document.getElementById("p1");
    element.insertBefore(para,child);
</script>
```

## 8. createAttribute

```html
<div id="div1">
    <p id="p1">This is a paragraph.</p>
    <p id="p2">This is another paragraph.</p>
</div>

<script>
    let para=document.createElement("p");
    let node=document.createTextNode("This is new.");
    para.appendChild(node);
    
    let parent=document.getElementById("div1");
    let child=document.getElementById("p1");
    parent.replaceChild(para,child);
</script>
```

## 9. createElement



## 10. createTextNode



## 11. getAttribute



## 12. setAttribute



## 参考链接

1. [W3school](https://www.w3school.com.cn/htmldom/dom_methods.asp)