# 学习Git，初步接触Flask

2019年11月30日

## git使用

在电脑上设置github邮箱和账户名

```shell
git config --global user.name "用户名"
git config --global user.email "邮箱"
```

生成SSH key，之后将公钥添加到GitHub上

```shell
ssh-keygen -t rsa -C "邮箱"
```


## 换源
```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo rm /etc/apt/sources.list
sudo vim /etc/apt/sources.list
```

添加内容
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
```
之后执行``sudo apt-get update``

## Pyton3 虚拟环境

```
sudo apt-get install python3-venv
python3 -m venv venv
```
激活虚拟环境``source venv/bin/active``，使用deactivate命令还原

安装flask ``pip install flask``，如果遇到Werkzeug包安装失败，使用``pip install -i http://pypi.douban.com/simple --trusted-host pypi.douban.com　Flask``

可用``pip freeze``查看虚拟环境中安装了哪些包

## Flask基本知识

### 初始化

Flask需要创建应用实例，web服务器会使用WSGI（Web server gateway interface)的协议，把接收自客户端的所有请求都转交给这个对象处理。创建方式：

```python
from flask import Flask
app = flask(__name__)   #构造函数，只有一个必须指定的参数，即应用主模块或包的名称
```

### 路由和函数视图

路由：处理URL和函数之间关系的程序
用``app.route``装饰器声明路由：

```python
@app.route('/')
def index():
    return '<h1>Hello World</h1>'   #把index函数注册为应用根地址的处理程序
```

使用``app.add_url_rule()``方法注册index函数：
```python
def index():
    return '<h1>Hello World</h1>'

app.add_url_rule('/', 'index', index)   #支持三个参数url 端点名 视图函数
```

视图函数：处理入站请求的函数，函数的返回值称为响应，是客户端接收到的内容

对可变地址的支持：
```python
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, {}!</h1>'.format(name)
```

完整应用
```python
from flask import Flask
app = Flask(__name__)


@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
```

执行
```
export FLASK_APP=hello.py 
flask run
```

请求响应循环，待补全 ,
上下文临时把某些对象变为全局访问
1. 应用上下文 ``current_app`` ``g``
2. 请求上下文 ``request`` ``session``

通过url与视图函数之间的映射查找对应url，请求对象、**4种请求钩子**、响应对象

## 模板

Jinja2模板引擎，在默认情况下，Flask在应用目录中的``templates``子目录里寻找模板，通过``render_template()``函数把Jinja2模板引擎集成到了应用当中。函数的第一个参数是模板的文件名，随后的参数是键-值对，表示模板中变量对应的具体值。

### 变量

变量占位符 ``{{}}``，变量的值可以通过过滤器进行修改，过滤器添加在变量名之后，二者之间用竖线分隔。其中``safe``过滤器能够避免Jinja2对变量的转义，例如``<h1>Hello</h1>``不会被渲染为``&lt;h1&gt;Hello&lt;/&gt;``。

### 控制结构

通过控制结构，改变模板的渲染流程。if、for、宏、继承、区块

Bootstrap样式集成，``flask-bootstrap``

**在继承时，使用super()保留原有内容**

### 自定义错误界面

```python
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500):
def internal_server_error(e):
    return render_template('500.html'), 500
```

通过模板继承在保证页面布局统一的前提下减少人力劳动。 ``git checkout 3c``

### 链接

对于包含可变部分的动态路由，在模板中构建正确的url相对困难。为避免产生错误，Flask提供了``url_for()``辅助函数，使用应用的URL映射中保存的信息生成URL。

例如在此前的例子中使用``url_for('index')``得到的结果是/，即应用的根URL。调用``url_for('index', _external=True)``返回的是绝对地址，实例中为 http://localhost:5000/。

>生成链接应用内不同路由的连接时可以只用相对地址，如果要声称在浏览器之外使用的链接，必须使用绝对地址。

在生成动态URL时，将动态部分作为关键字传入，例如 ``url_for('user', name='andao', _external=True)``。

**问题**，url_for应该在哪个位置使用？

### 静态文件

static路由是flask为了支持静态文件自动添加的，url为``/static/<filename>``

在模板中使用``url_for('static', filename = 'filename')``获取动态链接

### 使用Flask-Moment本地化日期和时间

``Moment.js``,``Flask-Moment``拓展能简化将Moment.js集成到Jinja2模板中的过程。

拓展初始化方法，与Flask-Bootstarp类似
```python
from flask_moment import Moment
moment = Moment(app)
```

需要在模板中引入该库``moment.include_moment()``

时间问题待解决
