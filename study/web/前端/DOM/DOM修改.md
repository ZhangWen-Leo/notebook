# DOM修改

1. 修改HTML元素
2. 创建HTML元素
3. 改变HTML样式
4. 创建新的HTML元素
5. 使用事件

## 1. 修改HTML元素

- 改变 HTML 内容
- 改变 CSS 样式
- 改变 HTML 属性
- 创建新的 HTML 元素
- 删除已有的 HTML 元素
- 改变事件（处理程序）

## 2. 创建HTML元素

```html
<html lang="en">
    <body>
        <p id="p1`">Hello World!</p>
        <script>
            document.getElementById("p1").innerHTML="New text!";
        </script>`
    </body>
</html>
```

## 3. 改变HTML样式

```html
<html lang="en">
    <body>
        <p id="p2">Hello world!</p>
        <script>
            document.getElementById("p2").style.color="blue";
        </script>
    </body>
</html>
```

## 4. 创建新的HTML元素

```html
<div id="d1">
    <p id="p1">This is a paragraph.</p>
    <p id="p2">This is another paragraph.</p>
</div>
<script>
    let para=document.createElement("p");
    let node=document.createTextNode("This is new.");
    para.appendChild(node);
    
    let element=document.getElementById("d1");
    element.appendChild(para);
</script>
```

## 5. 使用事件

```html
<html lang="en">
    <body>
        <input type="button" onclick="document.body.style.backgroundColor='lavender';"
        value="Change background color" />
    </body>
</html>
```

```html
<html lang="en">
    <body>
        <script>
            function ChangeBackground()
            {
            document.body.style.backgroundColor="lavender";
            }
        </script>
        <input type="button" onclick="ChangeBackground()"
        value="Change background color" />
    </body>
</html>
```

```html
<html lang="en">
    <body>
        <p id="p1">Hello world!</p>
        <script>
            function ChangeText()
            {
            document.getElementById("p1").innerHTML="New text!";
            }
        </script>
        <input type="button" onclick="ChangeText()" value="Change text">
    </body>
</html>
```

## 参考链接

1. [HTML DOM - 修改](https://www.w3school.com.cn/htmldom/dom_modify.asp)
