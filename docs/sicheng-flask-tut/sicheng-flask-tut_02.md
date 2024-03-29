# Flask 入门系列(三)–模板

在第一篇中，我们讲到了 Flask 中的 Controller 和 Model，但是一个完整的 MVC，没有 View 怎么行？前端代码如果都靠后台拼接而成，就太麻烦了。本篇，我们就介绍下 Flask 中的 View，即模板。

### 系列文章

*   Flask 入门系列(一)–Hello World
*   Flask 入门系列(二)–路由
*   Flask 入门系列(三)–模板
*   Flask 入门系列(四)–请求，响应及会话
*   Flask 入门系列(五)–错误处理及消息闪现
*   Flask 入门系列(六)–数据库集成

### 模板

Flask 的模板功能是基于[Jinja2 模板引擎](http://jinja.pocoo.org/)实现的。让我们来实现一个例子吧。创建一个新的 Flask 运行文件（你应该不会忘了怎么写吧），代码如下：

```py
from flask import Flask
from flask import render_template

app = Flask(__name__)

@app.route('/hello')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)

if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)

```

这段代码同上一篇的多 URL 路由的例子非常相似，区别就是”hello()”函数并不是直接返回字符串，而是调用了”render_template()”方法来渲染模板。方法的第一个参数”hello.html”指向你想渲染的模板名称，第二个参数”name”是你要传到模板去的变量，变量可以传多个。

那么这个模板”hello.html”在哪儿呢，变量参数又该怎么用呢？别急，接下来我们创建模板文件。在当前目录下，创建一个子目录”templates”（注意，一定要使用这个名字）。然后在”templates”目录下创建文件”hello.html”，内容如下：

```py
<!doctype html>
<title>Hello Sample</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}

```

这段代码是不是很像 HTML？接触过其他模板引擎的朋友们肯定立马秒懂了这段代码。它就是一个 HTML 模板，根据”name”变量的值，显示不同的内容。变量或表达式由”{{ }}”修饰，而控制语句由”{% %}”修饰，其他的代码，就是我们常见的 HTML。

让我们打开浏览器，输入”http://localhost:5000/hello/man”，页面上即显示大标题”Hello man!”。我们再看下页面源代码

```py
<!doctype html>
<title>Hello from Flask</title>

  <h1>Hello man!</h1>

```

果然，模板代码进入了”Hello {{ name }}!”分支，而且变量”{{ name }}”被替换为了”man”。Jinja2 的模板引擎还有更多强大的功能，包括 for 循环，过滤器等。模板里也可以直接访问内置对象如 request, session 等。对于 Jinja2 的细节，感兴趣的朋友们可以自己去查查。

#### 模板继承

一般我们的网站虽然页面多，但是很多部分是重用的，比如页首，页脚，导航栏之类的。对于每个页面，都要写这些代码，很麻烦。Flask 的 Jinja2 模板支持模板继承功能，省去了这些重复代码。让我们基于上面的例子，在”templates”目录下，创建一个名为”layout.html”的模板：

```py
<!doctype html>
<title>Hello Sample</title>
<link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
<div class="page">
    {% block body %}
    {% endblock %}
</div>

```

再修改之前的”hello.html”，把原来的代码定义在”block body”中，并在代码一开始”继承”上面的”layout.html”：

```py
{% extends "layout.html" %}
{% block body %}
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}
{% endblock %}

```

打开浏览器，再看下”http://localhost:5000/hello/man”页面的源码。

```py
<!doctype html>
<title>Hello Sample</title>
<link rel="stylesheet" type="text/css" href="/static/style.css">
<div class="page">

  <h1>Hello man!</h1>

</div>

```

你会发现，虽然”render_template()”加载了”hello.html”模板，但是”layout.html”的内容也一起被加载了。而且”hello.html”中的内容被放置在”layout.html”中”{% block body %}”的位置上。形象的说，就是”hello.html”继承了”layout.html”。

#### HTML 自动转义

我们看下下面的代码：

```py
@app.route('/')
def index():
    return '<div>Hello %s</div>' % '<em>Flask</em>'

```

打开页面，你会看到”Hello Flask”字样，而且”Flask”是斜体的，因为我们加了”em”标签。但有时我们并不想让这些 HTML 标签自动转义，特别是传递表单参数时，很容易导致 HTML 注入的漏洞。我们把上面的代码改下，引入”Markup”类：

```py
from flask import Flask, Markup

app = Flask(__name__)

@app.route('/')
def index():
    return Markup('<div>Hello %s</div>') % '<em>Flask</em>'

```

再次打开页面，”em”标签显示在页面上了。Markup 还有很多方法，比如”escape()”呈现 HTML 标签, “striptags()”去除 HTML 标签。这里就不一一列举了。

我们会在 Flask 进阶系列里对模板功能作更详细的介绍。

本文中的示例代码可以在这里下载。

转载请注明出处: [思诚之道](http://www.bjhee.com/flask-3.html)