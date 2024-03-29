# 代码约定

![代码约定](img/conventions.png)

# 代码约定

在 Python 社区中有许多关于代码风格的约定。如果你写过一段时间 Python 了，那么也许对此已经有些了解。 我会简单介绍一下，同时给你一些 URL 链接，从中你可以找到关于这个话题的详细信息。

## 让我们提出一个 PEP！

**PEP**全称是“Python Enhancement Proposal”（Python 增强提案）。你可以在[python.org](http://www.python.org/dev/peps/)上找到它们以及对应的索引目录。 PEP 在索引目录中按照数字编号排列，包括了元 PEP（meta-PEP，讨论关于 PEP 的细节）。与之对应的是技术 PEP（technical PEP），思考的是诸如 Python 内部实现的改良这样的话题。

有一些 PEP，比如 PEP 8 和 PEP 257，影响了 Python 代码风格的标准。 PEP 8 包括了 Python 代码风格的规约。 而 PEP 257 包括了文档字符串（docstrings， 在 Python 中给代码加文档的标准方式）的规约。

### PEP 8: Python 代码风格规约

PEP 8 是对 Python 代码风格的官方规约。 我建议你阅读它并将之付诸在 Flask 项目（以及其他 Python 项目）的开发实践中。 当项目规模膨胀到多个包含成百上千行代码的文件时，这样做会使你的代码更加工整、了然。毕竟 PEP 8 的建议都是围绕着实现更加可读的代码这个目标。 另外，如果你的项目准备开源，潜在的奉献者（contributors）会很高兴看到你的代码是遵循 PEP 8 的。

一个至关重要的建议是每级缩进使用 4 个空格。不要使用 tab。 如果你打破了这个规约，它将会成为你（以及你的队友）在项目间切换的一个负担。 这种不一致一向是任意语言心中的痛，但是对于 Python，一门着重留白的语言，这是一个不可承受之重。 因为 tab 与 space 之间的混搭会导致不可预期且难以排查的错误。

### PEP 257: 文档字符串规约

PEP 257 覆盖了 Python 的另一项标准:**docstrings**。 你可以阅读 PEP 中的定义和相关建议，不过这里会给一个例子来展示一个文档字符串应该是怎样的：

```py
def launch_rocket():
    """主要的火箭发射调度器

    启动发射火箭所需的每一个步骤。
    """
    # [...] 
```

这种风格的文档字符串可以通过一些诸如[Sphinx](http://sphinx-doc.org/)的软件来生成不同格式的文档。 同时它们也有助于让你的代码更加工整。

> 参见
> 
> *   PEP 8 [`legacy.python.org/dev/peps/pep-0008/`](http://legacy.python.org/dev/peps/pep-0008/)
> *   PEP 257 [`legacy.python.org/dev/peps/pep-0257/`](http://legacy.python.org/dev/peps/pep-0257/)
> *   Sphinx [`sphinx-doc.org/`](http://sphinx-doc.org/)，一个文档生成器，同出于 Flask 作者之手

## 相对形式的 import

开发 Flask 应用时，使用相对形式的 import 会让你的生活更加轻松。 原因很简单。之前，当需要 import 一个内部模块时，你也许要显式指明应用的包名（the app's package name）。假设你想要从*myapp/models.py*中导入`User`模型：

```py
# 使用绝对路径来导入 User
from myapp.models import User 
```

用了相对形式的 import 后，你可以使用点标记法：第一个`.`来表示当前目录，之后的每一个`.`表示下一个父目录。

```py
# 使用相对路径来导入 User
from .models import User 
```

这种做法的好处在于使得 package 变得更加模块化了。 现在你可以重命名你的 package 并在别的项目中重用模块，而无需忍受更新被硬编码的包名之苦。

> 参见
> 
> *   你可以在 PEP 328 的[这一节](http://www.python.org/dev/peps/pep-0328/#guido-s-decision)里读到更多关于相对形式的 import 的语法
> *   在写作本书的过程中，我碰巧在这个 Tweet 上面看到了一个使用相对形式的 import 的好处： [`twitter.com/dabeaz/status/372059407711887360`](https://twitter.com/dabeaz/status/372059407711887360) Just had to rename our whole package. Took 1 second. Package relative imports FTW!

## 总结

*   尽量遵循 PEP 8 中的代码风格规约。
*   尽量遵循 PEP 257 中的文档字符串规约。
*   使用相对形式的 import 来 import 你的应用中的内部模块。