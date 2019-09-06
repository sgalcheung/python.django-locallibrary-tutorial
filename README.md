# python.django-locallibrary-tutorial
 *[from MDN web docs,Server-side website programming,Django web framework (Python)](https://developer.mozilla.org/zh-CN/docs/Learn/Server-side/Django)
  [github:repo](https://github.com/mdn/django-locallibrary-tutorial)*
#### 新技能
认识Django框架，配置Django开发环境，Windows系统需要先安装python，通过cmd.exe安装
```bash
choco install python
```
如果没有安装[Chocolatey](https://chocolatey.org/install#installing-chocolatey)，先执行
```bash
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
新版本python已包含pip3（python包管理器，你可以列出安装的软件包）

之后就可以通过pip命令安装Django
```bash
pip install django
```
继续通过命令创建项目存储位置，Django应用程序的位置
```bash
mkdir locallibrary
cd locallibrary
```
然后通过django-admin工具创建项目骨架，创建后导航到文件夹
```bash
django-admin startproject locallibrary
cd locallibrary
```
使用manage.py和命令从此文件夹内运行开发Web服务器
```bash
python manage.py runserver
```
默认站点在[http://127.0.0.1:8000/](http://127.0.0.1:8000/)

继续创建catalog应用
```bash
python manage.py startapp catalog
```

最终结构目录如下
```bash
locallibrary/         # 网站文件夹
    manage.py         # 用来运行Django工具的脚本(由django-admin创建)
    locallibrary/     # 网站/项目文件夹(由django-admin创建)
        settings.py   # 包含所有的网站设置。这是可以注册所有创建的应用的地方，也是静态文件，数据库配置的地方，等等。
        urls.py       # 定义了网站url到view的映射。虽然这里可以包含所有的url，但是更常见的做法是把应用相关的url包含在相关应用中，你可以在接下来的教程里看到。
        wsgi.py       # 帮助Django应用和网络服务器间的通讯。你可以把这个当作模板。
    catalog/          # 应用文件夹 (由manage.py创建)
        admin.py      # 后台，这个就是可以用很少代码就能实现后台管理的神奇东东
        apps.py
        forms.py      # 表单，用户在浏览器上输入数据提交，对数据的验证工作以及输入框的生成等工作
        models.py     # 与数据库操作相关，存入或读取数据时用到这个，当然用不到数据库的时候 你可以不使用
        tests.py
        views.py      # 处理用户发出的请求，从 urls.py 中对应过来, 通过渲染 templates 中的网页可以将一些想要看到的内容输入到网页上
        __init__.py   # 一个空文件，Django/Python会将这个文件作为Python 包并允许你在项目的其他部分使用它
        migrations/   # 用来存储“migrations”——当你修改你的数据模型时，这个文件会自动升级你的数据库
        static/       # 静态文件，css,js,image
        templates/    # views.py 中的函数渲染 templates 中的 Html 模板，得到动态内容的网页，当然可以用缓存来提高速度
```
#### 设计LocalLibrary模型
<img src="https://mdn.mozillademos.org/files/14021/local_library_model_uml_v0_1.png" width="600">

模型通常在models.py(/locallibrary/catalog/）中定义
#### 运行数据库迁移
```bash
python manage.py makemigrations
python manage.py migrate
```
#### 注册模型
在目录应用程序(/locallibrary/catalog/admin.py)中打开admin.py，调用 admin.site.register 来注册它们
#### 创建一个超级用户
该账号具有完全访问该站点和所有必需的权限可以使用manage.py，你将被提示输入用户名，电子邮件地址，和强密码。
```bash
python manage.py createsuperuser
```
打开  http://127.0.0.1:8000/admin 登录超级用户之后，展示了所有模型，点击模型名并可以对具体记录进行编辑和删除，也可以添加，

之后就是编码普通用户访问界面

#### 部署 Django 到生产环境
通常有一个单独的 settings.py 文件用于生产环境，并从单独的文件或环境变量，导入敏感设置。即使其他源代码在公共存储库中可用，也应保护此文件。

在Heroku上安装LocalLibrary
* 在 Github 中创建应用程序储存库
* 在 GitHub 储存库的根目录中，创建文件Procfile（无扩展名），以声明应用程序的进程类型和入口点
* Web 应用程序的 Python requirements ，必须放在储存库根目录中的文件 requirements.txt 中。然后 Heroku 将在重建您的环境时，自动安装它们。您可以在命令行上，使用 pip 创建此文件（在 repo 根目录中，运行以下命令）：
 ```bash
 pip freeze > requirements.txt
 ```
* runtime.txt 文件（如果已定义）告诉 Heroku 使用哪种编程语言。在 repo 的根目录中，创建文件并添加以下文本：
* 获取 Heroku 帐户
* 安装客户端
* 创建并上传网站
 ```bash
 # 更改，可选
 git add .
 git commit -m "Description changes"
 git push origin master
 git push heroku master
 # 开始部署
 heroku create                                    # 创建一个名为 heroku 的 git remote
 git push heroku master                           # 推送到 Heroku 储存库
 heroku run python manage.py migrate              # 执行迁移操作
 heroku run python manage.py createsuperuser      # 创建我们的管理超级用户
 heroku open                                      # 打开浏览器访问新网站，发布的站点
 ```

 最后此应用部署站点 https://powerful-depths-56210.herokuapp.com
 第一次访问有点慢，是因为它处于睡眠状态，通过Heroku可以了解到，毕竟免费