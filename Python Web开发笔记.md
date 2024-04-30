# Python Web开发笔记

## 一、前端开发

### 1、学习流程

```txt
目的：开发一个平台
    - 前端开发：HTML、CSS、JavaScript
    - web框架：接收请求并处理
    - MySQl数据库：数据存储位置
    
快速上手：基于Flask Web框架搭建网站

深入学习：
	基于Django框架
```

### 2、快速开发网站(Flask)

```shell
pip install flask
```

```python
'''
Flask中的Hello，World！
'''
from flask import Flask

app = Flask(__name__)

#创建网址 /show/info 和函数 index 的对应关系
#用户访问网址即执行 index 函数
@app.route('/show/info')
def index():
    return "Hello, World!"

if __name__ == '__main__':
    app.run()
```

- 使用`render_template`可以返回html文件中的内容，但要把html文件放在程序同目录下的`templates`文件夹中

```python
from flask import Flask,render_template

app = Flask(__name__)

#创建网址 /show/info 和函数 index 的对应关系
#用户访问网址即执行 index 函数
@app.route('/show/info')
def index():
    return render_template("index.html")

if __name__ == '__main__':
    app.run()
```

### 3、HTML语法(是一种标记语言)

- **结构如下**
  - HTML 标签通常是**成对出现**的，比如 `<b>` 和` </b>`
  - 标签对中的第一个标签是*开始标签*，第二个标签是*结束标签*
  - 只有`<body>`部分才会在浏览器中显示

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>标题</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```

- **head**

  ```html
  <head>
      <meta charset="UTF-8">
      <title>title</title>
  </head>
  ```

  - **编码**

    ```html
    <meta charset="UTF-8"
    ```

  - **标题**

    ```html
    <title>我的标题</title>
    <h1>这是一个标题</h1>
    <h2>这是一个标题</h2>
    <h3>这是一个标题</h3>
    ```

- **body**

  - **段落**

    ```html
    <p>这是一个段落。</p>
    <p>这是另外一个段落。</p>
    ```

  - **超链接**

    ```html
    <a href="https://www.runoob.com">这是一个链接</a>
    ```

  - **图像**

    ```html
    <img src="/images/logo.png" width="258" height="39" />
    ```

  - **换行**

    ```html
    <br />
    ```

  - **div和span**

    - div , 一个元素占一整行（块级标签）

    ```html
    <div>Hello</div>
    <div>World!</div>
    ```

    - span ,  不额外占位（行内标签、内联标签）

    ```html
    <span>Hello</span>
    <span>World!</span>
    ```

    