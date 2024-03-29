# 模板

![模板](img/templates.png)

# 模板

尽管 Flask 并不强迫你使用某个特定的模板语言，它还是默认你会使用 Jinja。在 Flask 社区的大多数开发者使用 Jinja，并且我建议你也跟着做。有一些插件允许你用其他模板语言进行替代(比如[Flask-Genshi](http://pythonhosted.org/Flask-Genshi/)和[Flask-Mako](http://pythonhosted.org/Flask-Mako/))，但除非你有充分理由（不懂 Jinja 可不是一个充分的理由！），否则请保持那个默认的选项；这样你会避免浪费很多时间来焦头烂额。

> **注意** 几乎所有提及 Jinja 的资源讲的都是 Jinja2。Jinja1 确实曾存在过，但在这里我们不会讲到它。当你看到 Jinja 时，我们讨论的是这个 Jinja: [`jinja.pocoo.org/`](http://jinja.pocoo.org/)

## Jinja 快速入门

**Jinja**文档在解释这门语言的语法和特性这方面做得很棒。在这里我不会啰嗦一遍，但还是会再一次向你强调下面一点：

> Jinja 有两种定界符。`{% ... %}`和`{{ ... }}`。前者用于执行像 for 循环或赋值等语句，后者向模板输出一个表达式的结果。
> 
> **参见**： [`jinja.pocoo.org/docs/templates/#synopsis`](http://jinja.pocoo.org/docs/templates/#synopsis)

## 怎样组织模板

所以要将模板放进我们的应用的哪里呢？如果你是从头开始阅读的本文，你可能注意到了 Flask 在对待你如何组织项目结构的事情上十分随意。模板也不例外。你大概也已经注意到，总会有一个放置文件的推荐位置。记住两点。对于模板，这个最佳位置是放在包文件夹下。

```py
myapp/
    __init__.py
    models.py
    views/
    templates/
    static/
run.py
requirements.txt 
```

让我们打开模板文件夹看看。

```py
templates/
    layout.html
    index.html
    about.html
    profile/
        layout.html
        index.html
    photos.html
    admin/
        layout.html
        index.html
        analytics.html 
```

**模板**的结构平行于对应的路由的结构。对应于路由*myapp.com/admin/analytics*的模板是*templates/admin/analytics.html*。这里也有一些额外的模板不会被直接渲染。*layout.html*文件就是用于被其他模板继承的。

## 继承

就像蝙蝠侠一样，一个组织良好的模板文件夹也离不开继承带来的好处。**基础模板**通常定义了一个适用于所有的*子模板*的主体结构。在我们的例子里，*layout.html*是一个基础模板，而其他的*html*文件都是子模板。

通常，你会有一个顶级的*layout.html*定义你的应用的主体布局，外加站点的每一个节点也有自己的一个*layout.html*。如果再看一眼上面的文件夹结构，你会看到一个顶级的*myapp/templates/layout.html*，以及*myapp/templates/profile/layout.html*和*myapp/templates/admin/layout.html*。后两个文件继承并修改第一个文件。

继承是通过`{% extends %}`和`{% block %}`标签实现的。在双亲模板中，你可以定义要给子模板处理的 block。

*myapp/templates/layout.html*

```py
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>{% block title %}{% endblock %}</title>
    </head>
    <body>
    {% block body %}
        <h1>这个标题在双亲模板中定义</h1>
    {% endblock %}
    </body>
</html> 
```

在子模板中，你可以拓展双亲模板并定义 block 里面的内容。

*myapp/templates/index.html*

```py
{% extends "layout.html" %}
{% block title %}Hello world!{% endblock %}
{% block body %}
    {{ super() }}
    <h2>这个标题在子模板中定义</h2>
{% endblock %} 
```

`super()`函数让我们在子模板里加载双亲模板中这个 block 的内容。

> **参见** 若想了解更多关于继承的内容，请移步到 Jinja 模板继承方面的文档。 [`jinja.pocoo.org/docs/templates/#template-inheritance`](http://jinja.pocoo.org/docs/templates/#template-inheritance)

## 创建宏

凭借将反复出现的代码片段抽象成**宏**，我们可以实现 DRY 原则（Don't Repeat Yourself）。在撰写用于应用的导航功能的 HTML 时，我们可能会需要给“活跃”链接（比如，到当前页面的链接）一个不同的类。如果没有宏，我们将不得不使用一大堆 if/else 语句来从每个链接中过滤出“活跃”链接。

宏提供了模块化模板代码的一种方式；它们就像是函数一样。让我们看一下如何使用宏来标记活跃链接。

myapp/templates/layout.html

```py
{% from "macros.html" import nav_link with context %}
<!DOCTYPE html>
<html lang="en">
    <head>
    {% block head %}
        <title>我的应用</title>
    {% endblock %}
    </head>
    <body>
        <ul class="nav-list">
            {{ nav_link('home', 'Home') }}
            {{ nav_link('about', 'About') }}
            {{ nav_link('contact', 'Get in touch') }}
        </ul>
    {% block body %}
    {% endblock %}
    </body>
</html> 
```

现在我们调用了一个尚未定义的宏 - `nav_link` - 并传递两个参数给它：一个目标（比如目标视图的函数名）和我们想要展示的文本。

> **注意** 你可能注意到了我们在 import 语句中加入了**with context**。Jinja 的**上下文(context)**包括了通过`render_template()`函数传递的参数以及在我们的 Python 代码的 Jinja 环境上下文。这些变量能够被用于模板的渲染。
> 
> 一些变量是我们显式传递过去的，比如`render_template("index.html", color="red")`，但还有些变量和函数是 Flask 自动加入到上下文的，比如`request`，`g`和`session`。使用了`{% from ... import ... with context %}`，我们告诉 Jinja 让所有的变量也在宏里可用。
> 
> **参见**
> 
> *   所有的全局变量都是由 Flask 传递给 Jinja 上下文的: [`flask.pocoo.org/docs/templating/#standard-context`](http://flask.pocoo.org/docs/templating/#standard-context)
> *   通过上下文处理器（context processors），我们可以增加传递给 Jinja 上下文的变量和函数: [`flask.pocoo.org/docs/templating/#context-processors`](http://flask.pocoo.org/docs/templating/#context-processors)

是时候定义模板中用的`nav_link`宏了。

myapp/templates/macros.html

```py
{% macro nav_link(endpoint, text) %}
{% if request.endpoint.endswith(endpoint) %}
    <li class="active"><a href="{{ url_for(endpoint) }}">{{text}}</a></li>
{% else %}
    <li><a href="{{ url_for(endpoint) }}">{{text}}</a></li>
{% endif %}
{% endmacro %} 
```

现在我们已经在*myapp/templates/macros.html*中定义了一个宏。我们所做的，就是使用 Flask 的`request`对象 - 默认在 Jinja 上下文中可用 - 来检查当前路由是否是传递给`nav_link`的那个路由参数。如果是，我们就在目标链接指向的页面上，于是可以标记它为活跃的。

> **注意** `from x import y`语句中要求 x 是相对于 y 的相对路径。如果我们的模板位于*myapp/templates/user/blog.html*，我们需要使用`from "../macros.html" import nav_link with context`。

## 自定义过滤器

Jinja 过滤器是在渲染成模板之前，作用于`{{ ... }}`中的表达式的值的函数。

```py
<h2>{{ article.title|title }}</h2> 
```

在这个代码中，`title`过滤器接受`article.title`并返回一个标题格式的文本，用于输出到模板中。它的语法，以及功能，皆一如 Unix 中修改程序输出的“管道”一样。

> **参见** 除了`title`，还有许许多多别的内建的过滤器。在这里可以看到完整的列表： [`jinja.pocoo.org/docs/templates/#builtin-filters`](http://jinja.pocoo.org/docs/templates/#builtin-filters)

我们可以自定义用于 Jinja 模板的过滤器。作为例子，我们将实现一个简单的`caps`过滤器来使字符串中所有的字母大写。

> **注意** Jinja 已经有一个`upper`过滤器能实现这一点，还有一个`capitalize`过滤器能大写第一个字符并小写剩余字符。这些过滤器还能处理 Unicode 转换，不过我们的这个例子将只专注于阐述相关概念。

我们将在*myapp/util/filters.py*中定义我们的过滤器。这个`util`包可以用来放置各种杂项。

myapp/util/filters.py

```py
from .. import app

@app.template_filter()
def caps(text):
    """Convert a string to all caps."""
    return text.uppercase() 
```

在上面的代码中，通过`@app.template_filter()`装饰器，我们能将某个函数注册成 Jinja 过滤器。默认的过滤器名字就是函数的名字，但是通过传递一个参数给装饰器，你可以改变它：

```py
@app.template_filter('make_caps')
def caps(text):
    """Convert a string to all caps."""
    return text.uppercase() 
```

现在我们可以在模板中调用`make_caps`而不是`caps`：`{{ "hello world!"|make_caps }}`。

为了让我们的过滤器在模板中可用，我们仅需要在顶级*__init__.py*中 import 它。

myapp/__init__.py

```py
# 确保 app 已经被初始化以免导致循环 import
from .util import filters 
```

## 总结

*   使用 Jinja 作为模板语言。

*   Jinja 有两种定界符：`{% ... %}`和`{{ ... }}`。前者用于执行类似循环或赋值的语句，后者向模板输出表达式求值的结果。

*   模板应该放在*myapp/templates/* - 一个在应用文件夹里面的目录。

*   我建议*template/*文件夹的结构应该与应用 URL 结构一一对应。
*   你应该在*myapp/templates*以及站点的每一部分放置一个*layout.html*作为布局模板。后者是前者的拓展。
*   可以用模板语言写类似于函数的宏。
*   可以用 Python 代码写应用在模板中的过滤器函数。