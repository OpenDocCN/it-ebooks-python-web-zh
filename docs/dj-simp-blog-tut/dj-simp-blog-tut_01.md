# Django 简介

# 写作目的

喜欢一个学习观点`以教促学`, 一直以来, 学习的时候经常会发现, 某个方法某个问题自己已经明白了, 但是在教给别人的时候确说不清楚, 所以慢慢的学会了`以教促学`这种方法, 在教给别人知识的同时也能够提升自己对语言, 对框架的理解.

希望达到的目标:

*   希望能写出一个系列文章, 我也不知道到底能写多少
*   能够让认真阅读这个系列的文章的人, 能在读完之后做出一个简单的博客
*   教会读者使用简单的 git 操作和 github
*   希望能够加深自己对`Django`的理解

# Django 简介

Django 是`python`中目前风靡的 Web Framework, 那么什么叫做`Framework`呢, 框架能够帮助你把程序的整体架构搭建好, 而我们所需要做的工作就是填写逻辑, 而框架能够在合适的时候调用你写的逻辑, 而不需要我们自己去调用逻辑, 让 Web 开发变的更敏捷.

> Django 是一个高级 Python Web 框架, 鼓励快速,简洁, 以程序设计的思想进行开发. 通过使用这个框架, 可以减少很多开发麻烦, 使你更专注于编写自己的 app, 而不需要重复造轮子. Django 免费并且开源.

## Django 特点

*   完全免费并开源源代码
*   快速高效开发
*   使用 MTV 架构(`熟悉 Web 开发的应该会说是 MVC 架构`)
*   强大的可扩展性.

# Django 工作方式

![工作方式](img/4648e2a3.png)

用户在浏览器中输入`URL`后的回车, 浏览器会对`URL`进行检查, 首先判断协议,如果是`http`就按照 Web 来处理, 然互调用`DNS 查询`, 将域名转换为`IP 地址`, 然后经过网络传输到达对应 Web 服务器, 服务器对 url 进行解析后, 调用`View`中的逻辑(MTV 中的 V), 其中又涉及到 Model(MTV 中的 M), 与数据库的进行交互, 将数据发到 Template(MTV 中的 T)进行渲染, 然后发送到浏览器中, 浏览器以合适的方式呈现给用户

> 通过文字和图的结合希望读者能够初步理解 Django 的工作方式