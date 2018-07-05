# 软件设计文档

小组成员：颜泽鑫、王治鋆、徐海洋、杨耿聪

我们采取了前后端分离的设计思路进行项目的开发。前端是基于 Vue 生成的网页客户端；后端以 Python Flask 服务器为核心，用于提供 RESTful API服务；同时，我们还搭建了 Nginx 服务器作为后端和前端的中间层，通过静态文件服务和 HTTP 代理来实现前后端分离，使得两边的开发可以同步进行，中间只需要依赖 RESTful API 进行对接。这种模式可以让后端只专注于 RESTful 接口和数据库的开发而不必关注如何提供前端资源，同时前端可以只关注业务逻辑的实现而不必考虑向后端请求的复杂路由。

![architecture](https://raw.githubusercontent.com/TheYelda/AppServer/master/doc/architecture.png)

根据以上架构，我们可以把项目划分为两个子项目，AppClient 和 AppServer。

## 一、AppClient

## 二、AppServer

#### 1、技术选型及理由

项目后端采用的技术栈为 Nginx + Gunicorn + Flask + MySQL 。

- Nginx：一个高性能的 Web 服务器，负责向前端提供静态文件服务，同时针对 Flask 提供的 RESTful API 服务实现反向代理，实现了前后端的分离。
- Gunicorn：一个轻量高效的Python WSGI 服务器，资源消耗低，支持异步I/O，非阻塞以及高并发。由于 Flask 自带的 WSGI 服务器性能较低，并发访问性能不高，因此使用 Gunicorn 替代之。
- Flask：作为一款轻量级的框架，具有自由、灵活、可扩展性强的特点，第三方库的选择很多，适合快速开发。
- MySQL：项目涉及多张数据表，适合采用关系型数据库管理系统。而 MySQL 是目前最流行的关系型数据库管理系统，具有体积小、速度快的特点。

#### 2、架构设计

服务端开发主要涉及 RESTful API的开发，同时还要提供私密配置项用于配置项目有关的账号密码等配置，项目主要有以下几个部分：

- models：数据库模型
- api：api的实现
- instance：项目的私密配置

#### 3、模块划分

根据业务需求，我们分析出一共需要Account、Job、Image、Label这四张表，并在此基础设计出了符合RESTful规范的[API](https://theyelda.docs.apiary.io/)。

有了以上的设计，我们很清晰地划分出了项目的模块：

```
└──server：服务端开发的源码
     ├─app：服务端主要代码
     │    ├─__init__.py：初始化app对象
     │    ├─model：定义数据模型及数据表相关操作
     │    │    ├─__init__.py：初始化数据库连接
     │    │    ├─accounts.py: 用户数据表模块 
     │    │    ├─jobs.py: 任务数据表模块 
     │    │    ├─images.py: 图像数据表模块 
     │    │    └─labels.py: 标注数据表模块 
     │    │
     │    └─api：各模块API
     │         ├─__init__.py：初始化api命名空间
     │         ├─account.py: 用户模块
     │         ├─authorization.py: 用户认证模块
     │         ├─image.py: 图像模块
     │         ├─job.py: 任务模块
     │         ├─label.py: 标注模块
     │         ├─upload.py: 处理图片上传的模块
     │         ├─self.py: 获取自身id的模块
     │         └─utils.py: 通用工具模块
     │
     ├─instance
     │    └─config.py：私密配置文件
     ├─config.py：普通配置文件
     ├─run.py：入口文件
     └─requirements.txt：第三方包需求文件
```

#### 4、所用的软件设计技术

##### 装饰器模式

装饰器模式把复杂的功能简单化，分散化，然后在运行期间根据需要来动态组合，从而实现动态的为对象添加功能。在项目中使用了非常多的装饰器，如某些API的登录验证:

```python
@login_required
def get(self, account_id):
    """Retrieve a single account by id."""
    ......
```

##### 工厂模式

`server/app/__init__.py`中实现了一个`create_app`的函数，完成了其他模块的初始化工作之后，再返回这个 app 对象。把应用实例创建的过程交给工厂函数，通过工厂函数选择所要使用的配置，然后在`run.py`中创建适用于不同环境下的应用:

```python
app = create_app(config_name)
app.run(host=app.config.get('HOST'), port=port)
```

##### 面向对象编程

项目使用了面向对象编程的封装思想，按照业务逻辑将代码的数据表模块和API模块分别进行了封装，如`sever/app/api/account.py`中处理单个用户资源的类:

```python
class AccountResource(Resource):
    """Deal with single account."""
    ......
```
