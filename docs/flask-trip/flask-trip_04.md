# 组织你的项目

![组织你的项目](img/organizing.png)

# 组织你的项目

Flask 会把项目组织的职责托付给你。 这是我喜欢使用 Flask 开始项目的其中一个理由，但是这意味着你不得不思考怎么组织你的代码。 你可以把这个应用放到一个文件中，或者把它分割多个包。然而这两种结构并不适合大多数项目。 这里有一些固定的组织模式，你可以遵循它们以便于开发和部署。

## 约定

在这一段中我想要先约定一些概念。

**版本库（Repository）**：你的应用的根目录。这个概念来自于版本控制系统，但在这里有所拓展。 当我在这一章提到“版本库”时，指的是你的项目的根目录。在开发你的应用时，你不太可能会离开这个目录。

**包（Package）**：包含了你的应用代码的一个包。在这一章，我将深入探讨以包的形式建立你的应用，但是现在只需知道包是版本库的一个子目录。

**模块（Module）**：一个模块是一个简单的，可以被其它 Python 文件引入的 Python 文件。一个包由多个模块组成。

> **参见**
> 
> *   在这里可以读到更多的关于 Python 模块的内容: [`docs.python.org/2/tutorial/modules.html`](http://docs.python.org/2/tutorial/modules.html)
> *   这个链接中也有一节关于包的内容: [`docs.python.org/2/tutorial/modules.html#packages`](http://docs.python.org/2/tutorial/modules.html#packages)

## 组织模式

### 单一模块

在许多 Flask 例子里，你会看到它们把所有的代码放到一个单一文件中，通常是*app.py*。对于一些微（~~写完就丢~~）项目来说这恰到好处，毕竟你只需要处理几个路由（route）并且只有百来行代码。（示例用的应用就是这样）

单一模块的应用的版本库看起来像这样：

```py
app.py
config.py
requirements.txt
static/
templates/ 
```

在这个例子中，应用逻辑部分会存放在*app.py*

### 包

当你开始在一个变得更加复杂的项目上工作时，单一模块就会造成严重的问题。 你需要为模型（model）和表单（form）定义多个类，而它们会跟你的路由和配置代码又吵又闹。所有的一切让你焦头烂额。 为了解决这个问题，我们得把应用中不同的组件分开到单独的、高内聚的一组模块 - 也即是包 - 之中。

基于包的应用的版本库看起来就像是这样：

```py
config.py
requirements.txt
run.py
instance/
  /config.py
yourapp/
  /__init__.py
  /views.py
  /models.py
  /forms.py
  /static/
  /templates/ 
```

这个结构允许你理智地整理你的应用的不同组件。 有关模型的类定义全待在*models.py*，而路由定义在*views.py*，有关表单的类定义全待在*forms.py*（我们等会会用整整一章的篇幅谈谈表单）。

下面的表格列举了大多数 Flask 应用都有的基本组件。 对于你的应用，可能还需要别的一些文件，但这些适用于大多数 Flask 应用。

| 组件 | 作用 |
| --- | --- |
| run.py | 这个文件中用于启动一个开发服务器。它从你的包获得应用的副本并运行它。这不会在生产环境中用到，不过依然在许多 Flask 开发的过程中看到。 |
| requirements.txt | 这个文件列出了你的应用依赖的所有 Python 包。你可能需要把它分成生产依赖和开发依赖。[请看第三章] |
| config.py | 这个文件包含了你的应用需要的大多数配置变量 |
| instance/config.py | 这个文件包含不应该出现在版本控制的配置变量。其中有类似调用密钥和数据库 URI 连接密码。同样也包括了你的应用中特有的不能放到阳光下的东西。比如，你可能在*config.py*中设定`DEBUG = False`，但在你自己的开发机上的*instance/config.py*设置`DEBUG = True`。因为这个文件可以在*config.py*之后被载入，它将覆盖掉`DEBUG = False`，并设置`DEBUG = True`。 |
| yourapp/ | 这个包里包括了你的应用。 |
| yourapp/__init__.py | 这个文件初始化了你的应用并把所有其它的组件组合在一起。 |
| yourapp/views.py | 这里定义了路由。它也许需要作为一个包（*yourapp/views/*），由一些包含了紧密相联的路由的模块组成。 |
| yourapp/models.py | 在这里定义了应用的模型。你可能需要像对待*views.py*一样把它分割成许多模块。 |
| yourapp/static/ | 这个文件包括了公共 CSS， Javascript, images 和其他你想通过你的应用展示出去的静态文件。默认情况下人们可以从*yourapp.com/static/*获取这些文件。 |
| yourapp/templates/ | 这里放置着你的应用的 Jinja2 模板。 |

### Blueprints

有朝一日你可能会发觉应用里有许多相关的路由了。如果是我，我会首先把*views.py*分割成一个包并把相关的路由组织成模块。 要是你已经这么做了，是时候把你的应用分解成[蓝图](http://docs.jinkan.org/docs/flask/blueprints.html)（blueprints）了

蓝图是按照一定程度上的自组织的方式，作为你的应用的一部分的组件。 它们表现得就像你的应用下的子应用一样。你可能使用不同的蓝图来对应管理面板（admin panel），前端（front-end）和用户面板（user dashboard）。 这使得你按照组件组织视图，静态文件和模板，并在组件间共享模型，表单和你的应用的其他部分。

你可以在第七章阅读到关于蓝图的更多内容。

## 总结

*   对于微应用，建议使用单一模块结构。
*   对于包含了视图，模型，表单以及更多的项目，使用包结构。
*   蓝图是把项目按照一些不同的组件组织起来的好办法。