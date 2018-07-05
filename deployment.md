# How to deploy
两种部署方式：`Docker部署`和`直接本地部署`

## 使用Docker

使用`Docker-Compose`进行一键部署，所用镜像托管在`DockerHub`和`daoCloud`上。

### Download

`clone`该仓库：

```shell
$ git clone https://github.com/TheYelda/Deployment.git
```

### Installation

- Docker安装（Mac/Windows需要注册`DockerHub`账号）：
  - [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  - [Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)
  - [Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
- Docker-Compose安装
  - [Linux](https://docs.docker.com/compose/install/)
  - Windows/Mac已经集成在Docker中

### Configure

1. 

2. 配置`Nginx`转发：

   在`./conf.d/yelda.conf`的第**2**行中，使用本机IP替代`127.0.0.1`：

   ```python
   set $myhost x.x.x.x;
   ```

3. 配置后端程序：

   在`./instance/config.py`中，修改配置

   - 使用本机IP替代`127.0.0.1`：

     ```python
     DB_HOST = 'x.x.x.x'
     ```

   - 创建目录，分别存放用户头像和医学影像图片，在配置文件的`PHOTOS_FOLDER `和`MEDICAL_IMAGES_FOLDER  `中配置。

     ```python
     PHOTOS_FOLDER = 'yelda/photos'
     MEDICAL_IMAGES_FOLDER = 'yelda/medical-images'
     ```

   - 配置会话管理密钥（可选）：

     修改`instance/config.py`的`SECRET_KEY  `字段即可。

   - 配置初始管理员账号密码：

     配置`ADMIN_USERNAME`和`ADMIN_PASSWORD `字段。

### Run

一键构建和启动容器：

```shell
$ sudo docker-compose up
```

如果出现报错，一般是因为**完全启动顺序**导致的连接问题。

首先，`Ctrl+c`终止之前运行的容器。建议在首次启动时，分别执行以下步骤：

1. 构建容器

   ```shell
   $ sudo docker-compose build
   ```

2. 启动数据库容器

   ```shell
   $ sudo docker-compose up -d db
   ```

3. 启动其它容器

   ```shell
   $ sudo docker-compose up
   ```

此时，可以再浏览器中输入`localhost:8080`访问网站。

之后，可以直接一键启动：

```shell
$ sudo docker-compose up -d
```

`-d`指定了容器在后台运行。

需要终止容器时：

```shell
$ sudo docker-compose down
```



## 直接本地部署

***Prerequisite***

- Nginx
- python 3.5+
- nodejs, npm
- MySQL 5.7

以下部署过程以`Ubuntu 16.04`为例。项目放置于`/yourpath/yelda`目录下。

### 负载均衡器

创建`yelda.conf`文件：

```nginx
server {
	listen 8080;

	# SSL configuration     
    # listen 443 ssl;
    server_name localhost;

	root /yourpath/yelda/www/static/;

    access_log /yourpath/yelda/log/access_log;
    error_log /yourpath/yelda/log/error_log;

    location ~ ^\/api\/.*$ {
        rewrite ^.+api/?(.*)$ /$1 break;
        proxy_pass       http://127.0.0.1:10086;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
}
```

将该文件放入`/etc/nginx/conf.d`或`/etc/nginx/sites-enabled`文件夹中（根据nginx版本不同，放入目录）

重新加载配置文件：

```shell
$ sudo nginx -s reload
```

### 数据库

登录MySQL，创建数据库，支持`UTF-8`编码：

```sql
CREATE DATABASE yelda DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

无需创建数据表。

### 前端

参考[前端README](https://github.com/TheYelda/AppClient/blob/master/README.md)

下载仓库：

```shell
$ https://github.com/TheYelda/AppClient.git
```

在仓库目录下：

```shell
npm install
npm run serve
npm run build
```

将生成的`dist/`目录下所有文件拷贝到`yelda/www/static`目录下。

### 后端

参考[后端README](https://github.com/TheYelda/AppServer/blob/master/README.md)

下载仓库：

```shell
$ https://github.com/TheYelda/AppServer.git
```

将`server/`目录下所有文件放置于`yelda/www`目录下

进入`yelda/www`目录，安装虚拟环境并启动：

```shell
$ pip install virtualenv
$ virtualenv venv -p python3
$ . venv/bin/activate
```

安装项目依赖模块：

```shell
(venv) $ pip install -r requirements.txt
```

进行配置：

新建`www/instance`目录，并创建`www/instance/config.py`文件，写入以下个人配置（以下内容只是举例）：

```python
DB_USERNAME = 'username'      # MySQL用户名
DB_PASSWORD = 'password'      # MySQL密码
DB_HOST = '127.0.0.1'         # MySQL主机
DB_PORT = '3306'              # 数据库端口
DB_NAME = 'yelda'             # 数据库名称
SECRET_KEY = 'I wont tell u'  # 用户会话管理秘钥
PHOTOS_FOLDER = 'yelda/photos'                  # 头像图片文件夹
MEDICAL_IMAGES_FOLDER = 'yelda/medical-images'  # 医疗图像文件夹
MAX_CONTENT_LENGTH = 16 * 1024 * 1024           # 上传文件大小限制
ADMIN_USERNAME = 'Admin'        # 管理员用户名
ADMIN_PASSWORD = '12345678'     # 管理员密码
```

并确保对应`PHOTOS_FOLDER`和`MEDICAL_IMAGES_FOLDER`的目录已存在。

运行后端程序：

```shell
(venv) $ gunicorn -w 4 -b 0.0.0.0:10086 production:app
```



## 运行效果

在浏览器中输入[localhost:8080](localhost:8080)可查看，使用上述配置的管理员账户和密码可以直接登陆。医生账号需要通过管理员审核方可登录。
