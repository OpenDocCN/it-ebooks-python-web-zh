# 部署

![部署](img/deployment.png)

# 部署

最终，你终于可以向全世界展示你的应用了。是时候部署它了。这个过程总能让人感到受挫，因为有太多任务需要完成。同时在部署的过程中你需要做出太多艰难的决定。我们会谈论一些关键的地方以及我们一些可能的选择。

## 托管主机

首先，你需要一个服务器。世上服务器提供商成千，但我只取三家。我不会谈论如何开始使用它们的服务的细节，因为这超出本书的范围。相反，我只会谈论它们作为 Flask 应用托管商上的优点。

### Amazon Web Services EC2（因为国情问题，让我们直接看下一个吧）

Amazon Web Services 指的是一套相关的服务，提供商是……~~卓越~~亚马逊！今日，许多著名的初创公司选择使用它，所以你或许已经听过它的大名。AWS 服务中我们最关心的是 EC2，全称是 Elastic Compute Cloud。EC2 的最大的卖点是你能够获得虚拟主机，或者说实例（这是 AWS 官方称呼），在仅仅几秒之内。如果你需要快速拓展你的应用，就只需启动多一点 EC2 实例给你的应用，并且用一个负载平衡器(load balancer)管理它们。（这时还可以试试 AWS Elastic Load Balancer）

对于 Flask 而言，AWS 就是一个常规的虚拟主机。付上一些费用，你可以用你喜欢的 Linux 发行版启动它，并安上你的 Flask 应用。之后你的服务器就起来了。不过它意味着你需要一些系统管理知识。

### Heroku

Heroku 是一个应用托管网站，基于诸如 EC2 的 AWS 的服务。他们允许你获得 EC2 的便利，而无需系统管理经验。

对于 Heroku，你通过`git push`来在它们的服务器上部署代码。这是非常便利的，如果你不想浪费时间 ssh 到服务器上，安装并配置软件，继续整个常规的部署流程。这种便利是需要花钱购买的，尽管 AWS 和 Heroku 都提供了一定量的免费服务。

> **参见** Heroku 有一个如何在它们的服务器上部署 Flask 应用的教程： [`devcenter.heroku.com/articles/getting-started-with-python`](https://devcenter.heroku.com/articles/getting-started-with-python)
> 
> **注意** 管理你自己的数据库将会花上许多时间，而把它做好也需要一些经验。通过配置你自己的站点来学习数据库管理是好的，但有时候你会想要外包给专业团队来省下时间和精力。Heroku 和 AWS 都提供有数据库管理服务。我个人还没试过，但听说它们不错。如果你想要保障数据安全以及备份，却又不想要自己动手，值得考虑一下它们。
> 
> *   Heroku Postgres: [`www.heroku.com/postgres`](https://www.heroku.com/postgres)
> *   Amazon RDS: [`aws.amazon.com/rds/`](https://aws.amazon.com/rds/)

### Digital Ocean

Digital Ocean 是最近出现的 EC2 的竞争对手。一如 EC2，Digital Ocean 允许你快速地启动虚拟主机（在这里叫 droplet）。所有的 droplet 都运行在 SSD 上，而在 EC2，如果你用的是普通服务，你是享受不到这种待遇的。对我而言，最大的卖点是它提供的控制接口比 AWS 控制面板简单和容易多了。Digital Ocean 是我个人的最爱，我建议你考虑下它。

在 Digital Ocean，Flask 应用部署方式就跟在 EC2 一样。你会得到一个全新的 Linux 发行版，然后需要安装你的全套软件。

## 部署工具

这一节将包括一些为了向别人提供服务，你需要安装在服务器上的软件。最基本的是一个前置服务器，用来反向代理请求给一个运行你的 Flask 应用的应用容器。你通常也需要一个数据库，所以我们也会略微谈论下这方面的内容。

### 应用容器

在开发应用时，本地运行的那个服务器并不能处理真实的请求。当你真的需要向公众发布你的应用，你需要在应用容器，例如 Gunicorn，上运行它。Gunicorn 接待请求，并处理诸如线程的复杂事务。

要想使用 Gunicorn，需要通过 pip 安装`gunicorn`到你的虚拟环境中。运行你的应用只需简单的命令。为了简明起见，让我们假设这就是我们的 Flask 应用：

*app.py*

```py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello World!" 
```

哦，这真简明扼要。现在，使用 Gunicorn 来运行它吧，我们只需执行这个命令：

```py
(ourapp)$ gunicorn rocket:app
2014-03-19 16:28:54 [62924] [INFO] Starting gunicorn 18.0
2014-03-19 16:28:54 [62924] [INFO] Listening at: http://127.0.0.1:8000 (62924)
2014-03-19 16:28:54 [62924] [INFO] Using worker: sync
2014-03-19 16:28:54 [62927] [INFO] Booting worker with pid: 62927 
```

你应该能在 [`127.0.0.1:8000`](http://127.0.0.1:8000) 看到“Hello World!”。

为了在后台运行这个服务器（也即使它变成守护进程），我们可以传递`-D`选项给 Gunicorn。这下它会持续运行，即使你关闭了当前的终端会话。

如果我们这么做了，当我们想要关闭服务器时就会困惑于到底应该关闭哪个进程。我们可以让 Gunicorn 把进程 ID 储存到文件中，这样如果想要停止或者重启服务器时，我们可以不用在一大串运行中的进程中搜索它。我们使用`-p <file>`选项来这么做。现在，我们的 Gunicorn 部署命令是这样：

```py
(ourapp)$ gunicorn rocket:app -p rocket.pid -D
(ourapp)$ cat rocket.pid
63101 
```

要想重新启动或者关闭服务器，我们可以运行对应的命令：

```py
(ourapp)$ kill -HUP `cat rocket.pid` # 发送一个 SIGHUP 信号，终止进程
(ourapp)$ kill `cat rocket.pid` 
```

默认下 Gunicorn 会运行在 8000 端口。如果这已经被另外的应用占用了，你可以通过添加`-b`选项来指定端口。

```py
(ourapp)$ gunicorn rocket:app -p rocket.pid -b 127.0.0.1:7999 -D 
```

#### 将 Gunicorn 摆上前台

> **注意** Gunicorn 应该隐藏于反向代理之后。如果你直接让它监听来自外网的请求，它很容易成为拒绝服务攻击的目标。它不应该接受这样的考验。只有在 debug 的情况下你才能把 Gunicorn 摆上前台，而且完工之后，切记把它重新隐藏到幕后。 }

如果你像前面说的那样在服务器上运行 Gunicorn，将不能从本地系统中访问到它。这是因为默认情况下 Gunicorn 绑定在 127.0.0.1。这意味着它仅仅监听来自服务器自身的连接。所以通常使用一个反向代理来作为外网和 Gunicorn 服务器的中介。不过，假如为了 debug，你需要直接从外网发送请求给 Gunicorn，可以告诉 Gunicorn 绑定 0.0.0.0。这样它就会监听所有请求。

```py
(ourapp)$ gunicorn rocket:app -p rocket.pid -b 0.0.0.0:8000 -D 
```

> **注意**
> 
> *   从文档中可以读到更多关于运行和部署 Gunicorn 的信息 : [`docs.gunicorn.org/en/latest/`](http://docs.gunicorn.org/en/latest/)
> *   Fabric 是一个可以允许你不通过 SSH 连接到每个服务器上就可以执行部署和管理命令的工具 : [`docs.fabfile.org/en/latest`](http://docs.fabfile.org/en/latest)

### Nginx 反向代理

反向代理处理公共的 HTTP 请求，发送给 Gunicorn 并将响应带回给发送请求的客户端。Nginx 是一个优秀的客户端，更何况 Gunicorn 强烈建议我们使用它。

要想配置 Nginx 作为运行在 127.0.0.1:8000 的 Gunicorn 的反向代理，我们可以在*/etc/nginx/sites-available*下给应用创建一个文件。不如称之为*exploreflask.com*吧。

*/etc/nginx/sites-available/exploreflask.com*

```py
# Redirect www.exploreflask.com to exploreflask.com
server {
        server_name www.exploreflask.com;
        rewrite ^ http://exploreflask.com/ permanent;
}

# Handle requests to exploreflask.com on port 80
server {
        listen 80;
        server_name exploreflask.com;

        # Handle all locations
        location / {
                # Pass the request to Gunicorn
                proxy_pass http://127.0.0.1:8000;

                # Set some HTTP headers so that our app knows where the request really came from
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
} 
```

现在在*/etc/nginx/sites-enabled*下创建该文件的符号链接，接着重启 Nginx。

```py
$ sudo ln -s \
/etc/nginx/sites-available/exploreflask.com \
/etc/nginx/sites-enabled/exploreflask.com 
```

你现在应该可以发送请求给 Nginx 然后收到来自应用的响应。

> **参见** Gunicorn 文档中关于配置 Nginx 的部分会给你更多启动 Nginx 的信息: [`docs.gunicorn.org/en/latest/deploy.html#nginx-configuration`](http://docs.gunicorn.org/en/latest/deploy.html#nginx-configuration)

#### ProxyFix

有时，你会遇到 Flask 不能恰当处理转发的请求的情况。这也许是因为在 Nginx 中设置的某些 HTTP 报文头部造成的。我们可以使用 Werkzeug 的 ProxyFix 来 fix 转发请求。

*app.py*

```py
from flask import Flask

# Import the fixer
from werkzeug.contrib.fixers import ProxyFix

app = Flask(__name__)

# Use the fixer
app.wsgi_app = ProxyFix(app.wsgi_app)

@app.route('/')
def index():
    return "Hello World!" 
```

> **参见** 在 Werkzeug 文档中可以读到更多关于 ProxyFix 的信息: [`werkzeug.pocoo.org/docs/contrib/fixers/#werkzeug.contrib.fixers.ProxyFix`](http://werkzeug.pocoo.org/docs/contrib/fixers/#werkzeug.contrib.fixers.ProxyFix)

## 总结

*   你可以把 Flask 应用托管到 AWS EC2， Heroku 和 Digital Ocean。（译者注：建议托管到国内的云平台上）
*   Flask 应用的基本部署依赖包括一个应用容器（比如 Gunicorn）和一个反向代理（比如 Nginx）。
*   Gunicorn 应该退居 Nginx 幕后并监听 127.0.0.1（内部请求）而非 0.0.0.0（外部请求）
*   使用 Werkzeug 的 ProxyFix 来处理 Flask 应用遇到的特定的转发报文头部。